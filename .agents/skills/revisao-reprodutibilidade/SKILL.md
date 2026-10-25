---
name: revisao-reprodutibilidade
description: Checklist e protocolo de auditoria de reprodutibilidade em R, Quarto, renv e proteção de dados para a Iniciação Científica.
---

# Checklist e Protocolo de Reprodutibilidade Científica

Este protocolo orienta o agente `revisor-codigo-econometria` e o pesquisador na verificação minuciosa de scripts em R e relatórios em Quarto (`.qmd`), assegurando que qualquer membro da comunidade acadêmica consiga reproduzir com exatidão os resultados da pesquisa.

---

## 1. Os 7 Pilares de Reprodutibilidade do Projeto

### Pilar 1: Isolamento de Ambiente com `renv`
- [ ] O projeto utiliza `renv` para gerenciar pacotes.
- [ ] Todos os pacotes carregados via `library(...)` constam no arquivo `renv.lock`.
- [ ] O comando `renv::status()` deve retornar estado limpo e consistente.
- [ ] Nenhum pacote experimental deve ser instalado globalmente sem registrar a versão no lockfile.

### Pilar 2: Gestão Estrita de Sementes Estocásticas (`set.seed`)
- [ ] Toda e qualquer rotina que envolva geração de números pseudo-aleatórios deve ser precedida por `set.seed(<inteiro>)`.
- [ ] Isso se aplica obrigatoriamente a:
  * Placebos espaciais por permutação em Controle Sintético (`tidysynth`).
  * Reamostragem via Bootstrap para intervalos preditivos ou testes de quebra.
  * Simulações Monte Carlo ou permutações de resíduos.
- [ ] A semente deve ser documentada explicitamente no cabeçalho do script ou chunk.

### Pilar 3: Caminhos Relativos e Portabilidade Universal
- [ ] **Proibição absoluta de caminhos absolutos:** Nenhum script pode conter strings como `C:/Users/...`, `D:/...` ou `/home/...`.
- [ ] Todos os acessos a dados ou scripts devem ser relativos à raiz do projeto (`.`):
  * Exemplo correto: `read.csv("dados/ipeadataPI.csv")` ou `here::here("dados", "ipeadataPI.csv")`.
- [ ] Os scripts devem executar identicamente em Windows, Linux ou macOS.

### Pilar 4: Imutabilidade e Integridade dos Dados Brutos
- [ ] Os arquivos originais em `dados/` (ex.: `dados/ipeadataPI.csv`) devem ser tratados como somente leitura.
- [ ] Nunca utilize funções de escrita (`write.csv`, `saveRDS`) apontando para o arquivo de entrada bruto original.
- [ ] Bases transformadas, normalizadas ou tratadas devem ser salvas em diretórios intermediários ou em `resultados/`.

### Pilar 5: Prevenção de Vazamento de Informação (*Data Leakage*)
- [ ] Em previsões fora da amostra: parâmetros de centralização, variância, desestacionalização ou seleção de preditores devem ser calculados **exclusivamente na amostra de treino** (*in-sample*).
- [ ] Em Controle Sintético: pesos ótimos $\mathbf{W}^*$ e matriz $\mathbf{V}^*$ devem ser estimados **estritamente na janela pré-intervenção** ($t < T_0$). Nenhum dado pós-intervenção pode entrar na otimização de pesos.

### Pilar 6: Configurações e Cache do Quarto
- [ ] O arquivo `_quarto.yml` deve estar configurado com `execute: freeze: auto` para evitar recomputações desnecessárias de chunks pesados.
- [ ] Os chunks pesados que requerem otimização devem ter a diretiva `#| cache: true` ou salvar objetos calculados em `.rds` em `resultados/`.
- [ ] Não commitar pastas de cache temporário corrompidas (`.quarto/quarto-session-temp*`).

### Pilar 7: Proteção de Segredos e Chaves de API
- [ ] Arquivos `.env` (ex.: chave de API do FRED) estão explicitamente listados no `.gitignore`.
- [ ] Em scripts públicos e chunks do Quarto, use `Sys.getenv("FRED_API_KEY")` ou `readRenviron()` sem jamais imprimir a chave na saída do documento (`echo: false`, sem print do valor).

---

## 2. Roteiro de Auditoria de um Arquivo de Código

Ao auditar um script R (`.R`) ou relatório Quarto (`.qmd`), execute os seguintes passos de inspeção:

1. **Varredura de Caminhos:**
   Busque por regex: `[a-zA-Z]:[/\\\\]` ou `/Users/` ou `/home/`. Se encontrar, reprove imediatamente.

2. **Varredura de Sementes:**
   Identifique se há chamadas de funções com componente aleatório (`sample`, `runif`, `rnorm`, placebos de `tidysynth`, `boot`). Se houver, verifique se `set.seed(...)` precede a execução.

3. **Varredura de Pacotes:**
   Compare as chamadas `library(...)` com as dependências registradas no `renv.lock`.

4. **Varredura de Escrita de Arquivos:**
   Verifique os destinos de `write.csv`, `write_parquet`, `saveRDS`. Garanta que a pasta `dados/` bruta não é sobrescrita.

---

## 3. Template de Parecer de Reprodutibilidade

Gere o parecer no formato:

```markdown
### Relatório de Auditoria de Reprodutibilidade

- **Arquivo Auditado:** `caminho/do/arquivo.R`
- **Data da Auditoria:** YYYY-MM-DD
- **Auditor:** revisor-codigo-econometria

| Item Verificado | Status | Observação |
| :--- | :---: | :--- |
| Ausência de caminhos absolutos | [OK / FALHA] | ... |
| Sementes estocásticas fixadas | [OK / FALHA] | ... |
| Pacotes sincronizados no renv | [OK / FALHA] | ... |
| Integridade dos dados brutos | [OK / FALHA] | ... |
| Ausência de data leakage | [OK / FALHA] | ... |
| Proteção de chaves e senhas | [OK / FALHA] | ... |

**Veredito Final:** [APROVADO | REQUER CORREÇÃO]
```

