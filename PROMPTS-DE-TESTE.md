# Roteiro de Prompts de Teste dos Agentes (`PROMPTS-DE-TESTE.md`)

Este guia contém uma bateria de prompts formulados especificamente para validar o comportamento, o rigor científico e o cumprimento das regras de cada um dos quatro agentes criados para a Iniciação Científica sobre a produção industrial brasileira.

---

## 1. Testes para o Agente: `econometrista`

### Prompt 1.1: Quebras Estruturais e Regressão Segmentada na PIM-PF
> *"Atuando como o econometrista da pesquisa, quero modelar a série mensal da PIM-PF (produção física industrial do IBGE) entre 2002 e 2023. Suspeito que houveram três quebras marcantes: Subprime (2008), Recessão Doméstica (2014-2016) e COVID-19 (2020). Como devo formular matematicamente o modelo de regressão segmentada com erros SARIMA/ARMA conforme a abordagem de Alencar e Rocha (2016)? Quais são os pressupostos necessários para validar essa estimação e quais testes de diagnóstico nos resíduos devo obrigatoriamente executar?"*

**Critérios de Sucesso:**
- [x] Apresenta equação formal com termos lineares de tempo $t$ e termos segmentados $(t - \tau_j) I(t > \tau_j)$.
- [x] Especifica a estrutura de erro ARMA/SARIMA $\Phi(B)\epsilon_t = \Theta(B)a_t$.
- [x] Exige testes formais: Bai-Perron para endogeneidade de quebras, Ljung-Box para autocorrelação residual e ARCH-LM para heterocedasticidade.
- [x] Mantém a resposta rigorosamente estruturada nos 6 tópicos do agente.

---

### Prompt 1.2: Avaliação Causal e Controle Sintético da Crise de 2014
> *"No caderno `analises/02_controlesintetico.qmd`, estamos usando o método de Controle Sintético para estimar a perda contrafactual da produção industrial na recessão de 2014 a partir de países da OCDE. Um colega sugeriu incluir a Argentina e a Venezuela no donor pool e permitir pesos negativos para melhorar o ajuste pré-tratamento ($R^2$). Qual é a sua avaliação econométrica dessa sugestão à luz de Abadie (2021)?"*

**Critérios de Sucesso:**
- [x] Rejeita veementemente pesos negativos, explicando a necessidade geométrica da restrição de convexidade ($\sum w_j = 1, w_j \ge 0$) para evitar extrapolação fora do suporte (*convex hull*).
- [x] Alerta contra a inclusão de doadores contaminados por choques idiossincráticos severos ou que violem a comparabilidade.
- [x] Enfatiza a importância de placebos espaciais/temporais e a razão de MSPE pós/pré em vez de $R^2$.

---

### Prompt 1.3: Modelagem GARMA e Resíduos Quantílicos Aleatorizados
> *"Por que deveríamos cogitar o uso de modelos GARMA (Benjamin et al., 2003) para modelar a série da produção industrial em vez de simplesmente aplicar um ARIMA clássico? Como os resíduos quantílicos aleatorizados de Dunn e Smyth (1996) nos ajudam no diagnóstico se a distribuição condicional for não-Gaussiana?"*

**Critérios de Sucesso:**
- [x] Explica que GARMA acomoda distribuições não-gaussianas com variância dependente da média.
- [x] Detalha a matemática e a interpretação dos resíduos quantílicos aleatorizados (Dunn & Smyth, 1996), mostrando que sob a especificação correta eles devem se comportar assintoticamente como normais padrão independentes $N(0, 1)$.

---

## 2. Testes para o Agente: `economista-industrial`

### Prompt 2.1: Comparação Substantiva: Choque de 2008 vs. Recessão de 2014–2016
> *"Atuando como economista industrial, compare a natureza dos choques que atingiram a indústria de transformação brasileira na Crise Financeira Internacional de 2008 versus a Recessão Doméstica de 2014–2016. Como esses dois episódios se diferenciam quanto à velocidade de recuperação e aos canais de transmissão (câmbio, crédito, consumo e investimento)?"*

**Critérios de Sucesso:**
- [x] Caracteriza 2008 como choque exógeno agudo de liquidez global com recuperação em "V" estimulada por expansão monetária, crédito público e boom de commodities.
- [x] Caracteriza 2014-2016 como crise interna multifacetada (esgotamento fiscal, aperto da Selic, incerteza política e deterioração de expectativas) com perda estrutural de nível duradoura.
- [x] Formula hipóteses empíricas testáveis para o econometrista (ex.: quebra transitória de inclinação em 2008 vs. quebra permanente de nível e tendência em 2014).

---

### Prompt 2.2: Heterogeneidade Setorial por Categorias de Uso
> *"Se o Banco Central do Brasil eleva substancialmente a taxa Selic para combater pressões inflacionárias, qual é a resposta esperada diferencial entre os setores de Bens de Capital e Bens de Consumo Semiduráveis e Não Duráveis na PIM-PF? Quais mecanismos explicam essa divergência?"*

**Critérios de Sucesso:**
- [x] Explica a alta sensibilidade de Bens de Capital ao custo de financiamento, taxa de desconto de projetos de investimento e incerteza sobre retorno futuro.
- [x] Mostra que Bens Não Duráveis (alimentos, remédios) possuem menor elasticidade-juros e dependem prioritariamente da massa salarial real.
- [x] Sugere especificação com defasagens distribuídas para testar a velocidade de transmissão monetária.

---

## 3. Testes para o Agente: `revisor-codigo-econometria`

### Prompt 3.1: Auditoria de Script R com Data Leakage e Sem Semente
> *"Revise o seguinte trecho de código R que calcula a previsão da série industrial e me diga se posso incluí-lo na análise:*
> ```r
> library(forecast)
> dados <- read.csv('C:/Users/joaov/Documents/dados/ipeadataPI.csv')
> # Normaliza a serie inteira
> media_global <- mean(dados$producao)
> sd_global <- sd(dados$producao)
> dados$prod_norm <- (dados$producao - media_global) / sd_global
> 
> # Treino e teste
> treino <- dados$prod_norm[1:200]
> teste <- dados$prod_norm[201:250]
> 
> modelo <- auto.arima(ts(treino, frequency = 12))
> previsao <- forecast(modelo, h = 50, bootstrap = TRUE)
> plot(previsao)
> ```"

**Critérios de Sucesso:**
- [x] Veredito imediato: `[REQUER CORREÇÃO]`.
- [x] Aponta o vazamento de dados (*data leakage*): normalizou a série usando `media_global` e `sd_global` calculadas sobre toda a amostra, contaminando o treino com informação do teste.
- [x] Aponta o uso de caminho absoluto da máquina (`C:/Users/joaov/...`).
- [x] Aponta a falta de `set.seed(...)` apesar de usar `bootstrap = TRUE`.
- [x] Fornece o código refatorado limpo, defensável e com caminhos relativos.

---

### Prompt 3.2: Validação de Implementação do `tidysynth`
> *"Estou montando a sintaxe do controle sintético via `tidysynth` para a crise de 2014. Como devo parametrizar a função `synthetic_control()` para garantir que os pesos sejam calculados unicamente no período pré-tratamento (2002 a 2013), e como estruturo a rotina para gerar os placebos espaciais de forma reproduzível?"*

**Critérios de Sucesso:**
- [x] Define a janela de preditores com `time_predictors = 2002:2013`.
- [x] Define o período pré-tratamento em `time_optimize_ssr = 2002:2013`.
- [x] Insere `set.seed(...)` obrigatório antes da rotina `generate_placebos()`.
- [x] Demonstra como plotar a razão de MSPE pós/pré sem distorções visuais.

---

## 4. Testes para o Agente: `conversor-papers`

### Prompt 4.1: Extração e Conversão de Página com Equações Complexas
> *"Converta este trecho simulado de um artigo empírico para Markdown acadêmico semântico:*
> `A equação do modelo segmentado com erro ARMA(1,1) é dada por Y_t = beta_0 + beta_1 * t + gamma * (t - T_b) * D_t + u_t, onde D_t é 1 se t > T_b e 0 caso contrário. O termo de erro segue (1 - phi * B) u_t = (1 + theta * B) a_t com a_t ~ iid N(0, sigma^2). Na Tabela 2, encontramos beta_1 = 0.45 (ep 0.08, t = 5.62, p < 0.001) e gamma = -0.72 (ep 0.11, t = -6.54, p < 0.001). A quebra ocorreu na página 282.`"

**Critérios de Sucesso:**
- [x] Converte as fórmulas para blocos KaTeX limpos (`$Y_t = \beta_0 + \dots$` e `$$...$$`).
- [x] Insere o marcador de página `<!-- page: 282 -->`.
- [x] Converte as estatísticas da Tabela 2 em uma tabela Markdown tabular do GitHub bem formatada com notas.
- [x] Fornece frontmatter YAML e checklist de controle de qualidade.

---

## 5. Teste Integrado Multidisciplinar

### Prompt 5.1: Alinhamento Teórico-Econométrico para a Iniciação Científica
> *"Com base nas regras do `.agents/AGENTS.md`, simule uma breve reunião entre o `economista-industrial` e o `econometrista`: precisamos decidir se a quebra da produção industrial brasileira em 2014 deve ser tratada prioritariamente como uma quebra estrutural em série univariada (regressão segmentada SARIMA) ou por meio de um contrafactual multivariado internacional (Controle Sintético). Qual a recomendação conjunta do grupo de pesquisa?"*

**Critérios de Sucesso:**
- [x] O `economista-industrial` contextualiza que a crise de 2014 decorreu de fatores estritamente domésticos, enquanto parceiros globais continuavam crescendo, o que justifica o contrafactual internacional.
- [x] O `econometrista` complementa que ambos os métodos são complementares: a regressão segmentada univariada data com precisão a quebra de inclinação e mede a taxa de contração pós-choque, enquanto o Controle Sintético estima a magnitude contrafactual do que teria ocorrido sem a recessão doméstica.
- [x] A recomendação final reforça o plano de trabalho da IC de João Vitor no IME-USP.

