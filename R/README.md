# Módulos e Scripts R (`R/`)

Este diretório contém funções utilitárias, rotinas customizadas de estimação, testes de hipótese e scripts modulares em R desenvolvidos para apoiar a modelagem da produção industrial brasileira.

---

## 1. Diretrizes de Desenvolvimento

1. **Modularidade e Funções Puras:**
   - Evite scripts monolíticos. Isole rotinas reutilizáveis em funções documentadas.
   - Nomeie arquivos com prefixos funcionais (ex.: `utils_series.R`, `estimacao_garma.R`, `testes_quebras.R`).

2. **Reprodutibilidade Estrita:**
   - Todas as dependências devem estar contempladas no `renv.lock`.
   - Use `set.seed(<inteiro>)` em funções que contenham componentes aleatórios (placebos, permutações, bootstrap).

3. **Caminhos Relativos:**
   - Sempre utilize caminhos relativos à raiz do projeto ou o pacote `here`.
   - Nunca grave dados diretamente na pasta `dados/` bruta.

4. **Auditoria Prévia:**
   - Antes de integrar novas rotinas aos cadernos Quarto (`analises/*.qmd`), submeta o código à revisão do agente `revisor-codigo-econometria`.

