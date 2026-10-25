# Plano de Arquitetura e Integração RAG + Zotero (`RAG-SETUP.md`)

Este documento descreve a arquitetura técnica, o esquema de dados e o roteiro de implementação para integrar a biblioteca bibliográfica do **Zotero** aos arquivos **Markdown** locais e a um banco vetorial para **Recuperação Aumentada por Geração (RAG)** a serviço dos agentes de IA.

---

## 1. Visão Geral da Arquitetura em Três Camadas

A arquitetura desacopla gestão bibliográfica, representação semântica e recuperação vetorial:

```
┌────────────────────────────────────────────────────────┐
│ 1. Camada Bibliográfica: Zotero                        │
│    - Gerencia metadados formais, PDFs e coleções       │
│    - Define a chave de citação canônica (paper_id)     │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│ 2. Camada Semântica: Arquivos Markdown Locais          │
│    - Pasta: literatura/markdown/<paper_id>.md          │
│    - Preserva fórmulas KaTeX ($...$ e $$...$$)         │
│    - Contém marcadores físicos: <!-- page: X -->       │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│ 3. Camada Vetorial: Vector DB (RAG)                    │
│    - Segmentação em Chunks Semânticos com Metadados    │
│    - Modelo de Embeddings e Banco (ChromaDB / LanceDB) │
│    - Recuperação semântica + citação exata de página   │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│ 4. Camada de Agentes: Antigravity                      │
│    - econometrista / economista-industrial             │
│    - Respostas fundamentadas com autor, ano e página   │
└────────────────────────────────────────────────────────┘
```

---

## 2. Esquema Obrigatório de Metadados por Fragmento (*Chunk*)

Para garantir que qualquer afirmação feita por um agente possa ser auditada na página original do artigo científico, cada *chunk* inserido no banco vetorial **deve obrigatoriamente** carregar os seguintes 10 campos de metadados:

| Campo | Tipo | Descrição | Exemplo |
| :--- | :--- | :--- | :--- |
| `paper_id` | `str` | Chave de citação canônica unificada com o Zotero | `"alencar2016mudancas"` |
| `title` | `str` | Título integral do artigo | `"Mudanças de tendência da produção industrial..."` |
| `authors` | `str` | Autores do artigo | `"Alencar, A. P.; Rocha, F."` |
| `year` | `int` | Ano de publicação | `2016` |
| `doi` | `str` | DOI oficial do artigo | `"10.5935/0034-7140.20160014"` |
| `page_start` | `int` | Página física inicial do fragmento no PDF | `278` |
| `page_end` | `int` | Página física final do fragmento no PDF | `279` |
| `section` | `str` | Título da seção/subseção de origem | `"3. Modelo de Regressão Segmentada com Erros ARMA"` |
| `source_file` | `str` | Caminho do arquivo Markdown fonte | `"literatura/markdown/alencar2016mudancas.md"` |
| `chunk_id` | `str` | Identificador único determinístico do fragmento | `"alencar2016mudancas_sec03_chk002"` |

---

## 3. Estratégia de Segmentação (*Chunking*) Acadêmico

Segmentadores ingênuos baseados puramente em contagem de caracteres quebram fórmulas matemáticas e perdem o contexto de tabelas. A estratégia adotada deve seguir três regras de integridade:

1. **Segmentação Guiada por Cabeçalhos (*Header-Based Splitting*):**
   - O particionador divide o documento prioritariamente nas quebras de seções (`#`, `##`, `###`).
   - Se uma subseção exceder o limite de tokens (~500 a 800 tokens), aplica-se a subdivisão em parágrafos.

2. **Integridade de Fórmulas Matemáticas:**
   - Blocos de equações delimitados por `$$...$$` nunca podem ser fatiados. Caso uma equação exceda a janela, o chunk deve expandir para mantê-la íntegra.

3. **Rastreamento dos Marcadores de Página:**
   - O segmentador processa o documento mantendo o estado do marcador `<!-- page: X -->`.
   - Ao emitir um chunk, calcula `page_start` (última página aberta) e `page_end` (se houve transição de página no interior do chunk).

4. **Sobreposição (*Overlap*):**
   - 10% a 15% de sobreposição textual entre blocos contíguos da mesma seção para preservar contexto anafórico.

---

## 4. Tecnologias Recomendadas

- **Banco Vetorial Local Embutido:**
  * **ChromaDB** (`chromadb` em Python): Leve, opera via SQLite / DuckDB em arquivo local (`.chromadb/`), sem necessidade de containers Docker ou servidores externos.
  * *Alternativa de alta performance:* **LanceDB** (`lancedb`), baseado em formato Apache Arrow.
- **Modelos de Embeddings:**
  * **Nuvem:** `text-embedding-004` (Google Gemini API).
  * **Local / Offline:** `sentence-transformers/all-MiniLM-L6-v2` ou `BAAI/bge-m3` (excelente para termos acadêmicos e bilíngue Português/Inglês).

---

## 5. Exemplo de Script de Ingestão (`scripts/ingest_rag.py`)

Abaixo está a estrutura conceitual do script de indexação em Python:

```python
import os
import re
import yaml
import chromadb
from pathlib import Path

def parse_markdown_paper(file_path: Path):
    content = file_path.read_text(encoding="utf-8")
    
    # 1. Extração do Frontmatter YAML
    match = re.match(r"^---\n(.*?)\n---\n(.*)$", content, re.DOTALL)
    if not match:
        raise ValueError(f"Frontmatter não encontrado em {file_path}")
    
    metadata = yaml.safe_load(match.group(1))
    body = match.group(2)
    
    # 2. Rastreamento de páginas e seções
    chunks = []
    current_page = 1
    current_section = "Introdução"
    
    # Divide por parágrafos preservando blocos
    paragraphs = body.split("\n\n")
    current_chunk = []
    page_start = current_page
    chunk_index = 0
    
    for p in paragraphs:
        # Detecta marcador de página
        page_match = re.search(r"<!--\s*page:\s*(\d+)\s*-->", p)
        if page_match:
            current_page = int(page_match.group(1))
            continue
            
        # Detecta cabeçalho de seção
        if p.startswith("#"):
            current_section = p.lstrip("#").strip()
            
        current_chunk.append(p)
        
        # Critério de tamanho do chunk (~1500 caracteres)
        if sum(len(x) for x in current_chunk) > 1500:
            chunk_index += 1
            chunk_text = "\n\n".join(current_chunk)
            chunk_id = f"{metadata['paper_id']}_chk{chunk_index:03d}"
            
            chunks.append({
                "chunk_id": chunk_id,
                "text": chunk_text,
                "metadata": {
                    "paper_id": metadata["paper_id"],
                    "title": metadata["title"],
                    "authors": str(metadata["authors"]),
                    "year": int(metadata["year"]),
                    "doi": metadata.get("doi", ""),
                    "page_start": page_start,
                    "page_end": current_page,
                    "section": current_section,
                    "source_file": str(file_path).replace("\\", "/")
                }
            })
            current_chunk = []
            page_start = current_page
            
    return chunks

def index_all():
    client = chromadb.PersistentClient(path="./.rag_db")
    collection = client.get_or_create_collection(name="literatura_ic")
    
    md_files = Path("literatura/markdown").glob("*.md")
    for md_file in md_files:
        chunks = parse_markdown_paper(md_file)
        if not chunks:
            continue
            
        collection.upsert(
            ids=[c["chunk_id"] for c in chunks],
            documents=[c["text"] for c in chunks],
            metadatas=[c["metadata"] for c in chunks]
        )
        print(f"Indexados {len(chunks)} chunks de {md_file.name}")

if __name__ == "__main__":
    index_all()
```

---

## 6. Como os Agentes do Antigravity Consultam o RAG

Quando o banco vetorial estiver alimentado, os agentes utilizarão uma ferramenta (MCP Server ou script Python auxiliar) que recebe a query e retorna fragmentos formatados:

```json
{
  "query": "qual foi a data de quebra identificada na produção industrial por Alencar e Rocha?",
  "results": [
    {
      "paper_id": "alencar2016mudancas",
      "author": "Alencar & Rocha",
      "year": 2016,
      "page": 284,
      "section": "4. Resultados Empíricos",
      "text": "A estimativa pontual para a data da mudança de tendência na produção física ocorreu em dezembro de 2008..."
    }
  ]
}
```

O agente `econometrista` formula a resposta citando com precisão inquestionável:
> *"Conforme demonstrado em Alencar & Rocha (2016, p. 284, Seção 4), a estimativa pontual para a mudança estrutural de tendência situou-se em dezembro de 2008..."*

---

## 7. Roteiro de Implementação em Fases

1. **Fase 1 (Atual):** Estrutura de pastas, agentes e regras no Antigravity estabelecidas.
2. **Fase 2:** Conversão dos 5 papers centrais da IC para `literatura/markdown/` e geração dos fichamentos.
3. **Fase 3:** Instalação do ChromaDB local e execução do script de indexação semântica.
4. **Fase 4:** Conexão dos agentes do Antigravity ao ChromaDB via ferramenta de recuperação contextual.

