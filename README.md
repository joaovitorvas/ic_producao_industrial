# Mudanças de Tendência da Produção Industrial no Brasil

Repositório central de documentação, códigos, notas teóricas e análises empíricas desenvolvidas no âmbito do projeto de **Iniciação Científica (IC)** no **Instituto de Matemática e Estatística da Universidade de São Paulo (IME-USP)**.

- **Pesquisador:** João Vitor Vasconcelos Lima (Bacharelando em Estatística — IME-USP)
- **Orientadora:** Profª. Dra. Airlane Pereira Alencar (Departamento de Estatística — IME-USP)
- **Palavras-chave:** Séries Temporais, Modelos de Regressão Segmentada, SARIMA / ARIMAX, Modelos GARMA, Decomposição STL, Controle Sintético.

---

## Sobre o Projeto

A atividade produtiva industrial está sujeita a fortes oscilações decorrentes de condições macroeconômicas internas e externas — como taxas de juros, câmbio, preços internacionais de *commodities* e crises financeiras ou sanitárias. Ao longo das últimas décadas, a série mensal da **Pesquisa Industrial Mensal — Produção Física (PIM-PF / IBGE)** atravessou quebras estruturais marcantes, com destaque para a **Crise Financeira Internacional de 2008 (*Subprime*)**, a **Recessão Doméstica de 2014–2016** e o choque sistêmico da **Pandemia de COVID-19 em 2020**.

Dando continuidade e expandindo a abordagem proposta em Alencar e Rocha (2016), este projeto tem como objetivos principais:

1. **Identificar pontos de mudança de nível e de tendência** na produção industrial brasileira (geral e setorial) por meio de métodos paramétricos e não-paramétricos;
2. **Estimar o impacto contrafactual de choques macroeconômicos** utilizando métodos de avaliação causal em painel agregado (Controle Sintético);
3. **Propor e ajustar modelos de séries temporais** que incorporem mudanças de tendência e a estrutura de dependência temporal e heterocedasticidade dos dados, incluindo:
   - Modelos de regressão segmentada com erros correlacionados (**ARMA / SARIMA / ARIMAX**);
   - Modelos Lineares Generalizados Autoregressivos e de Médias Móveis (**GARMA**, Benjamin et al., 2003), permitindo diferentes distribuições condicionais e variância dependente da média;
4. **Realizar diagnóstico completo de resíduos** — incluindo resíduos padronizados e **resíduos quantílicos aleatorizados** (Dunn e Smyth, 1996) — e construir **previsões fora da amostra com intervalos preditivos**.

---

## Estrutura do Repositório e Documentação

O projeto é documentado em formato de site reprodutível construído em **Quarto (`.qmd`)** e **R**. À medida que novas etapas da pesquisa são concluídas, os relatórios técnicos e cadernos teóricos são adicionados às pastas abaixo:

```text
ic_producao_industrial/
├── .github/
│   └── workflows/
│       └── publish.yml                      # Deploy automático para o GitHub Pages via GitHub Actions
├── _quarto.yml                              # Configuração global do site Quarto (com freeze: auto)
├── _freeze/                                 # Cache de resultados computacionais do R para build rápido
├── index.qmd                                # Página inicial do site
├── README.md                                # Documentação geral e guia do repositório
├── renv.lock                                # Controle de versões de pacotes R (reprodutibilidade)
├── dados/
│   ├── ipeadataPI.csv                       # Série mensal da Produção Industrial (IBGE / IPEADATA)
│   └── .env                                 # Chaves de API (ex.: FRED_API_KEY, ignorado pelo Git)
├── analises/                                # Análises empíricas e modelagem estatística
│   ├── 01_exploratoria.qmd                  # Análise exploratória, decomposição STL e tendências iniciais
│   └── 02_controlesintetico.qmd             # Impacto da Crise de 2014 via Controle Sintético (OCDE/FRED)
└── anotacoes/                               # Resumos teóricos, revisões de literatura e guias metodológicos
    └── variaveis_de_controle_sintetico.qmd  # Guia prático e teórico do Método de Controle Sintético
```

### Índice de Conteúdos Desenvolvidos

#### 1. Análises Empíricas (`analises/`)
- **[`01_exploratoria.qmd`](analises/01_exploratoria.qmd) — Análise Exploratória e Modelos Iniciais:**
  Inspeção visual da série histórica (2002–presente), datação econômica dos saltos e quebras estruturais (Dez/2008, Abr–Nov/2014 e Mar/2020), decomposição em tendência, sazonalidade e ruído via método **STL (LOESS)** e comparação de métricas de acurácia (EAM, EQM, EPAM) de modelos determinísticos preliminares.
- **[`02_controlesintetico.qmd`](analises/02_controlesintetico.qmd) — Impacto da Crise de 2014 com Controle Sintético:**
  Construção de um "Brasil Sintético" a partir de um painel internacional da OCDE/FRED (`2002–2019`), com reescalonamento pela média pré-intervenção, depuração econométrica do *donor pool* e bateria completa de validação (placebos no espaço, razão de MSPE, placebo no tempo via *backdating* para 2012 e análise *leave-one-out*).

#### 2. Anotações Teóricas e Metodológicas (`anotacoes/`)
- **[`variaveis_de_controle_sintetico.qmd`](anotacoes/variaveis_de_controle_sintetico.qmd) — Guia Prático: Método de Controle Sintético:**
  Guia didático e rigoroso baseado em Abadie (2021), abordando resultados potenciais, otimização binível ($\mathbf{W}^*$ e $\mathbf{V}^*$), modelo de fatores lineares e cota de viés, geometria da esparsidade, requisitos práticos de contexto e dados, inferência por permutação, extensões modernas e tutorial executável com o pacote `tidysynth`.

---

## Metodologia e Roteiro da Pesquisa

O desenvolvimento da pesquisa está organizado em cinco frentes metodológicas integradas ao cronograma de 12 meses:

| Etapa | Descrição Metodológica | Duração Prevista | Status |
| :--- | :--- | :---: | :---: |
| **1. Revisão da Literatura** | Estudo teórico de processos estocásticos, regressão segmentada, modelos SARIMA/ARIMAX, GARMA e avaliação causal em séries agregadas. | 2 meses | Em andamento |
| **2. Análise Descritiva e Decomposição** | Coleta via SIDRA/IBGE, IPEADATA e FRED; decomposição não-paramétrica via LOESS/STL (Cleveland et al., 1990) e avaliação de quebras e contrafactuais. | 1 mês | Concluído / Em refinamento |
| **3. Regressão com Erros ARMA (ARIMAX / SARIMA)** | Identificação de mudanças de nível e inclinação e estimação conjunta com estrutura autorregressiva e de médias móveis (Box e Jenkins, 1970; Alencar e Rocha, 2016). | 2 meses | Próxima etapa |
| **4. Modelos GARMA** | Ajuste de modelos lineares generalizados com componentes ARMA (Benjamin et al., 2003) via máxima verossimilhança condicional (`gamlss.util` / `forecast`), acomodando assimetria e heterocedasticidade. | 3 meses | Planejado |
| **5. Diagnóstico de Resíduos, Previsão e Artigo** | Análise de resíduos padronizados e quantílicos aleatorizados (Dunn e Smyth, 1996), comparação de previsões intervalares e redação do relatório/artigo final. | 4 meses | Planejado |

---

## Como Reproduzir o Ambiente e Publicar no GitHub Pages

O site do projeto é publicado em **<https://joaovitorvas.github.io/ic_producao_industrial/>**.

### Pré-requisitos
- **R** (versão $\ge 4.4$)
- **Quarto CLI** instalado no sistema
- Chave de API do FRED (*Federal Reserve Economic Data*) em `dados/.env` caso deseje reexecutar do zero as extrações da API em [`analises/02_controlesintetico.qmd`](analises/02_controlesintetico.qmd).

### 1. Restaurar os pacotes do R (`renv`)
O projeto utiliza o pacote `renv` para garantir reprodutibilidade exata das bibliotecas (`tidyverse`, `tidysynth`, `fredr`, `forecast`, `strucchange`, entre outras). No console do R, na raiz do projeto, execute:

```r
renv::restore()
```

### 2. Configurar variáveis de ambiente (para chamadas à API do FRED)
Para rodar a extração internacional via `fredr`, crie um arquivo `dados/.env` com a sua chave do FRED:

```env
FRED_API_KEY=sua_chave_aqui
```

### 3. Renderizar localmente e atualizar o cache (`_freeze/`)
O arquivo [`_quarto.yml`](_quarto.yml) está configurado com `execute: freeze: auto`. Isso significa que, ao rodar `quarto render` na sua máquina, o Quarto salva os resultados dos blocos de R na pasta `_freeze/` (e só reexecuta um documento quando o seu código-fonte é modificado):

```bash
quarto render
```

Para visualizar o site interativamente com recarregamento automático durante o desenvolvimento:

```bash
quarto preview
```

## Referências Principais

- **Abadie, A.** (2021). Using Synthetic Controls: Feasibility, Data Requirements, and Methodological Aspects. *Journal of Economic Literature*, 59(2), 391–425.
- **Alencar, A. P., & Rocha, F. M. M.** (2016). Forecasting Brazilian Industrial Production with level and trend changes after crisis and SARIMA models. *International Journal of Statistics and Economics*, 17(1), 22–29.
- **Benjamin, M. A., Rigby, R. A., & Stasinopoulos, D. M.** (2003). Generalized Autoregressive Moving Average Models. *Journal of the American Statistical Association*, 98(461), 214–223.
- **Box, G. E. P., & Jenkins, G. M.** (1970). *Time Series Analysis: Forecasting and Control*. Holden-Day.
- **Cleveland, R. B., Cleveland, W. S., McRae, J. E., & Terpenning, I.** (1990). STL: A Seasonal-Trend Decomposition Procedure Based on Loess. *Journal of Official Statistics*, 6(1), 3–73.
- **Dunn, P. K., & Smyth, G. K.** (1996). Randomized Quantile Residuals. *Journal of Computational and Graphical Statistics*, 5(3), 236–244.
- **Hyndman, R. J., & Athanasopoulos, G.** (2021). *Forecasting: Principles and Practice*. OTexts.
- **Morettin, P. A., & Tolói, C. M. C.** (2006). *Análise de Séries Temporais* (2ª ed.). São Paulo: Blucher.
- **Shumway, R. H., & Stoffer, D. S.** (2017). *Time Series Analysis and Its Applications: With R Examples* (4th ed.). Springer.

