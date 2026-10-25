# Resultados Computacionais e Saídas (`resultados/`)

Este diretório armazena os artefatos gerados pelas análises empíricas da pesquisa, incluindo modelos serializados (`.rds`), tabelas exportadas em LaTeX/HTML e gráficos em alta resolução.

---

## 1. Estrutura do Diretório

- `figuras/`: Gráficos gerados pelo `ggplot2` em formatos vetoriais (`.pdf`, `.svg`) ou `.png` em alta definição (300 DPI).
- `tabelas/`: Saídas tabulares formatadas via `modelsummary`, `gt` ou `kableExtra` para inclusão em relatórios e no artigo final.
- `modelos/`: Objetos de modelos econométricos estimados e pesados serializados com `saveRDS(...)` para carregamento rápido nos cadernos Quarto sem necessidade de reestimação contínua.

---

## 2. Boas Práticas

- Nenhum arquivo aqui presente deve ser gerado manualmente; todos devem ser fruto de scripts reprodutíveis em `analises/` ou `R/`.
- Objetos temporários de cache gerados pelo Quarto permanecem em `_freeze/` e não devem poluir esta pasta.

