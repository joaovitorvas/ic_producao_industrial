# Regras e Diretrizes do Grupo de Pesquisa em IA (.agents/AGENTS.md)

Este documento estabelece as diretrizes comuns, o padrão de rigor científico, a política de citações e a proteção de dados para todos os agentes inteligentes que atuam no projeto de **Iniciação Científica (IC): Mudanças de Tendência da Produção Industrial no Brasil** (IME-USP).

---

## 1. Contexto Institucional e Científico do Projeto

- **Projeto:** Mudanças de Tendência da Produção Industrial no Brasil: Modelagem Estatística, Quebras Estruturais e Avaliação Causal.
- **Instituição:** Instituto de Matemática e Estatística da Universidade de São Paulo (IME-USP).
- **Pesquisador:** João Vitor Vasconcelos Lima (Bacharelando em Estatística).
- **Orientadora:** Profª. Dra. Airlane Pereira Alencar (Departamento de Estatística — IME-USP).
- **Dados Principais:** Pesquisa Industrial Mensal — Produção Física (PIM-PF / IBGE), IPEADATA, Séries Internacionais da OCDE/FRED.
- **Abordagens Centrais:** Modelos de Regressão Segmentada com erros correlacionados (ARMA/SARIMA/ARIMAX), Modelos GARMA (Benjamin et al., 2003), Decomposição STL (LOESS), Controle Sintético (Abadie et al., 2010; Abadie, 2021) e Testes de Quebras Estruturais (Chow, Bai-Perron).

---

## 2. Princípios Fundamentais de Rigor Acadêmico

Todos os agentes devem seguir impreterivelmente estes quatro pilares:

1. **Separação Rígida entre Previsão, Descrição e Causalidade:**
   - **Previsão:** Modela a distribuição condicional $P(Y_{t+h} \mid \mathcal{F}_t)$ com base em correlações históricas e componentes estocásticos (ex.: SARIMA). Não implica causalidade.
   - **Descrição/Decomposição:** Identifica padrões empíricos, sazonalidade e tendências históricas (ex.: STL, taxas acumuladas). Não estabelece contrafactuais causais.
   - **Identificação Causal:** Requer desenho de pesquisa e pressupostos contrafactuais explícitos sob o modelo de Resultados Potenciais de Neyman-Rubin (ex.: SUTVA, trajetórias paralelas, convexidade dos pesos no Controle Sintético).
   - **Regra:** Nunca rotule um coeficiente de regressão ou previsão de série temporal como "efeito causal" a menos que a estratégia de identificação tenha sido formalmente especificada e defendida.

2. **Explicitação e Teste Formal de Pressupostos:**
   - Nenhum modelo pode ser apresentado como adequado sem o diagnóstico completo de seus pressupostos estatísticos:
     * Estacionariedade e ordem de integração $I(d)$ (ADF, KPSS, Phillips-Perron).
     * Ausência de autocorrelação nos resíduos (Ljung-Box, Breusch-Godfrey).
     * Homocedasticidade / ausência de efeitos ARCH (ARCH-LM).
     * Normalidade ou adequação distributiva (Shapiro-Wilk, resíduos quantílicos aleatorizados de Dunn e Smyth, 1996, para modelos GARMA).
     * No Controle Sintético: ausência de choque nos doadores (*donor pool* não contaminado), sobreposição de preditores, pesos não negativos somando 1 ($\sum w_j = 1, w_j \ge 0$) e placebos no espaço/tempo.

3. **Tolerância Zero a Alucinações Acadêmicas:**
   - Não invente referências bibliográficas, anos de publicação, DOIs, valores de estatísticas de teste, valores $p$ ou coeficientes estimados.
   - Caso uma evidência não esteja presente nos arquivos locais (`literatura/` ou `dados/`), o agente **deve expressar claramente a limitação**: *"Essa informação não foi encontrada nos documentos ou dados locais; recomenda-se verificar o paper original ou consultar a base de dados."*

4. **Neutralidade e Honestidade Metodológica:**
   - Não selecione modelos, subamostras ou transformações apenas porque confirmam uma narrativa desejada. Resultados nulos ou ausência de significância estatística são evidências científicas válidas e devem ser reportados com integridade.

---

## 3. Padrão de Citações e Rastreabilidade Documental

1. **Formato Padrão de Citação no Texto:**
   - Use o padrão autor-data acadêmico: `Sobrenome (Ano, p. X)` ou `(Sobrenome, Ano, Seção Y)`.
   - Exemplo: `Abadie (2021, p. 394)` ou `(Alencar & Rocha, 2016, Seção 3)`.

2. **Conexão com a Biblioteca Local (`literatura/`):**
   - Sempre que referenciar um artigo fichado ou convertido, utilize o identificador canônico `paper_id` correspondente ao registro em `literatura/indice/indice-papers.csv`.
   - Exemplo: `[alencar2016mudancas](file:///literatura/markdown/alencar2016mudancas.md)`.
   - Para trechos específicos recuperados via RAG ou Markdown, aponte a seção original e as páginas exatas.

3. **Transparência de Limitações:**
   - Se uma referência clássica for mencionada de memória sem arquivo local correspondente (ex.: Box & Jenkins, 1970), o agente deve deixar evidente que se trata de conhecimento geral da área e sugerir a inclusão do paper em `literatura/`.

---

## 4. Proteção, Integridade e Privacidade de Dados

1. **Proteção Estrita de Arquivos Brutos:**
   - Os arquivos em `dados/` (ex.: `dados/ipeadataPI.csv`) são dados primários e devem ser tratados como **somente leitura**.
   - Nenhuma rotina de código deve sobrescrever ou alterar a base bruta original. Quaisquer transformações (logaritmos, diferenças, reescalonamentos) devem gerar novas variáveis em código ou bases derivadas em `resultados/`.

2. **Proteção de Segredos, Chaves de API e Direitos Autorais (`literatura/`):**
   - Arquivos `.env` (ex.: `dados/.env`, com chaves como `FRED_API_KEY`) **nunca** devem ter seus valores impressos em logs, prompts, relatórios Quarto ou arquivos de documentação pública.
   - O arquivo `.gitignore` deve manter `.env` permanentemente bloqueado para commits no Git.
   - **Política *Bring Your Own Papers* (BYOP):** Os agentes e skills em `.agents/` são públicos no repositório, mas os PDFs acadêmicos (`literatura/pdf-original/`), as transcrições integrais (`literatura/markdown/`), os fichamentos locais (`literatura/fichamentos/`) e bancos vetoriais (`.rag_db/`, `.chromadb/`) são estritamente locais e devem permanecer ignorados no `.gitignore` para proteção de direitos autorais. Apenas a estrutura de diretórios (`.gitkeep`), os `README.md` e o catálogo de metadados (`literatura/indice/indice-papers.csv`) são versionados no Git.

3. **Reprodutibilidade Computacional:**
   - Qualquer análise quantitativa deve ser 100% reproduzível:
     * Fixação mandatória de semente estocástica com `set.seed(...)` em R para permutações, bootstraps ou placebos.
     * Uso de caminhos relativos à raiz do projeto (nunca caminhos absolutos locais como `C:/Users/...`).
     * Pacotes e versões gerenciados exclusivamente via `renv` (`renv.lock`).

---

## 5. Divisão de Responsabilidades no Grupo de Agentes

Cada agente possui um escopo de competência bem delimitado:

| Agente | Arquivo de Configuração | Escopo Principal |
| :--- | :--- | :--- |
| **Economista Industrial** | `.agents/agents/economista-industrial.md` | Contexto institucional, canais de transmissão macroeconômica, desindustrialização, choques exógenos e formulação de hipóteses teóricas. |
| **Econometrista** | `.agents/agents/econometrista.md` | Desenho de identificação causal, estimação de séries temporais, quebras estruturais, regressão segmentada, GARMA, Controle Sintético e diagnósticos. |
| **Revisor de Código Econometria** | `.agents/agents/revisor-codigo-econometria.md` | Auditoria de código R e Quarto (`.qmd`), prevenção de *data leakage*, controle numérico, pacotes `renv` e padrões de estilo. |
| **Conversor de Papers** | `.agents/agents/conversor-papers.md` | Conversão de PDFs acadêmicos para Markdown estruturado, preservação rigorosa de equações LaTeX, tabelas e extração de metadados para RAG. |

### Fluxo de Trabalho Integrado

```
[Literatura / Papers em PDF]
          │
          ▼
   (Conversor de Papers) ──> Markdown Estruturado & Metadados
          │
          ├──────────────────────────────────┐
          ▼                                  ▼
(Economista Industrial)              (Econometrista)
Contexto, Choques & Hipóteses     Modelagem, Quebras & Causalidade
          │                                  │
          └─────────────────┬────────────────┘
                            ▼
               (Revisor de Código Econometria)
                Auditoria de R, Quarto & renv
                            │
                            ▼
              [Relatórios Quarto e Resultados]
```

