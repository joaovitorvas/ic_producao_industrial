# Biblioteca e Repositório de Literatura Acadêmica (`literatura/`)

Este diretório gerencia o acervo científico, artigos metodológicos, transcrições em Markdown, fichamentos críticos e o índice central de metadados da Iniciação Científica sobre a produção industrial brasileira.

---

## 1. Estrutura de Pastas

```text
literatura/
├── README.md                  # Este documento explicativo (versionado no Git)
├── pdf-original/              # Arquivos PDF originais (IGNORADOS no Git - uso local)
├── markdown/                  # Artigos transcritos em Markdown (IGNORADOS no Git - uso local)
├── fichamentos/               # Fichas de leitura crítica (IGNORADOS no Git - uso local)
└── indice/
    ├── indice-papers.csv      # Catálogo mestre de metadados dos artigos (versionado no Git)
    └── README.md              # Documentação dos campos do catálogo (versionado no Git)
```

> **Política de Direitos Autorais e Arquitetura *Bring Your Own Papers* (BYOP):**
> Os agentes pré-configurados (`.agents/`), os protocolos operacionais (`skills/`), a estrutura de diretórios (`.gitkeep`) e o catálogo de metadados (`literatura/indice/indice-papers.csv`) são **públicos** no repositório Git.
> Em contrapartida, os arquivos em `pdf-original/`, `markdown/` e `fichamentos/` estão bloqueados no [`.gitignore`](../.gitignore) para respeitar direitos autorais editoriais (AER, JEL, Econometrica, JASA, JCGS, etc.). Cada pesquisador que clonar o repositório deve adicionar seus próprios PDFs localmente e acionar os agentes para construir sua base de conhecimento local.

---

## 2. O Papel de Cada Subdiretório

### `pdf-original/`
- Armazena os documentos originais em formato PDF.
- **Convenção de nomenclatura:** `<paper_id>.pdf` (ex.: `alencar2016mudancas.pdf`, `abadie2021using.pdf`).
- Os PDFs servem como o documento primário de auditoria física e são vinculados aos registros do Zotero.

### `markdown/`
- Textos dos artigos transcritos em Markdown semântico de alta fidelidade pelo agente `conversor-papers`.
- Cada arquivo contém um cabeçalho YAML frontmatter padronizado com metadados acadêmicos completos.
- As formulações matemáticas são rigorosamente preservadas em KaTeX/LaTeX (`$...$` e `$$...$$`).
- Contém marcadores de página explícitos (`<!-- page: X -->`) que permitem ao sistema de RAG indicar a página exata da fonte em qualquer resposta empírica.
- **Convenção de nomenclatura:** `<paper_id>.md`.

### `fichamentos/`
- Fichas de leitura crítica e avaliação metodológica geradas a partir da skill `leitura-critica-de-papers`.
- Dissecam a pergunta de pesquisa, estratégia de identificação, pressupostos, resultados, robustez e a utilidade concreta para a modelagem da PIM-PF (IME-USP).
- **Convenção de nomenclatura:** `<paper_id>-fichamento.md`.

### `indice/`
- Contém a tabela mestra `indice-papers.csv` que atua como chave relacional do projeto.
- Conecta cada registro bibliográfico aos três mundos:
  1. **Zotero:** Chave do item e metadados bibliográficos (DOI, autores, ano).
  2. **Arquivos Locais:** PDF original, Markdown e Fichamento.
  3. **RAG / Banco Vetorial:** Identificador `paper_id` para filtragem de chunks nos embeddings.

---

## 3. Fluxo de Inclusão de um Novo Paper

Para adicionar um artigo ao ecossistema da pesquisa:

1. **Adicionar ao Zotero:** Importe a referência no Zotero, obtenha o PDF e atribua a chave de citação (ex.: `alencar2016mudancas`).
2. **Salvar o PDF:** Copie o arquivo para `literatura/pdf-original/<paper_id>.pdf`.
3. **Converter para Markdown:** Use o agente `conversor-papers` (via skill `conversao-pdf-academico`) para gerar `literatura/markdown/<paper_id>.md`.
4. **Registrar no Índice:** Adicione uma nova linha na tabela `literatura/indice/indice-papers.csv`.
5. **Realizar o Fichamento:** Utilize o agente `economista-industrial` ou `econometrista` (via skill `leitura-critica-de-papers`) para produzir o fichamento em `literatura/fichamentos/<paper_id>-fichamento.md`.
6. **Indexação Vetorial (RAG):** Quando a infraestrutura RAG estiver ativa, execute a ingestão dos chunks do Markdown com base nas diretrizes de `RAG-SETUP.md`.

