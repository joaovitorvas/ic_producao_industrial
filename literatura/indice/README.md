# Catálogo e Índice da Literatura (`literatura/indice/`)

Este diretório contém os metadados relacionais e o dicionário de campos do acervo de papers da pesquisa.

---

## 1. Dicionário de Variáveis do `indice-papers.csv`

| Coluna | Tipo | Descrição | Exemplo |
| :--- | :--- | :--- | :--- |
| `paper_id` | Texto (Único) | Identificador canônico do paper (chave relacional primária) | `alencar2016mudancas` |
| `title` | Texto | Título integral do artigo | "Mudanças de tendência..." |
| `authors` | Texto | Lista de autores separados por ponto-e-vírgula | "Alencar, A. P.; Rocha, F." |
| `year` | Inteiro | Ano de publicação | `2016` |
| `doi` | Texto | Identificador Digital de Objeto (DOI) | `10.5935/0034-7140.20160014` |
| `source_file` | Texto | Caminho relativo do PDF original em `literatura/pdf-original/` | `literatura/pdf-original/alencar2016mudancas.pdf` |
| `zotero_key` | Texto | Chave de citação ou identificador correspondente no Zotero | `ALEN2016` |
| `status_fichamento` | Categórico | Situação da ficha de leitura (`pendente`, `em_andamento`, `concluido`) | `concluido` |
| `status_rag` | Categórico | Situação da indexação vetorial (`pendente`, `indexado`, `erro`) | `pendente` |

---

## 2. Utilização pelos Agentes

- **`conversor-papers`:** Consulta o índice para verificar se o paper já existe e insere uma nova linha ao concluir a extração.
- **`econometrista` e `economista-industrial`:** Consultam o índice para citar a chave canônica `paper_id` e localizar o Markdown correspondente em `literatura/markdown/<paper_id>.md`.
- **Rotina de RAG:** Utiliza as colunas deste arquivo para enriquecer os metadados de cada fragmento (*chunk*) injetado no banco vetorial.

