---
name: revisao-econometrica
description: Protocolo formal de validação de modelos econométricos, testes de quebras estruturais, inferência causal e diagnósticos de resíduos.
---

# Protocolo de Revisão e Validação Econométrica

Este protocolo define os critérios formais para auditar, avaliar e aprovar qualquer especificação econométrica desenvolvida na Iniciação Científica, cobrindo modelos de séries temporais, regressão segmentada, GARMA e inferência causal.

---

## 1. Etapa 1: Propriedades das Séries Temporais e Estacionariedade

Antes de estimar qualquer relação dinâmica ou regressão:

1. **Testes de Raiz Unitária e Estacionariedade:**
   - Execute conjuntamente testes com hipóteses nulas complementares:
     * **ADF (Augmented Dickey-Fuller):** $H_0$: série possui raiz unitária ($I(1)$).
     * **KPSS (Kwiatkowski-Phillips-Schmidt-Shin):** $H_0$: série é estacionária ($I(0)$ em nível ou em torno de tendência).
     * **Phillips-Perron:** Robusto a autocorrelação e heterocedasticidade nos erros.
   - **Critério de Consistência:**
     * ADF rejeita $H_0$ e KPSS não rejeita $H_0 \implies$ Conclui-se por $I(0)$.
     * ADF não rejeita $H_0$ e KPSS rejeita $H_0 \implies$ Conclui-se por $I(1)$ (diferenciação mandatória).
     * Ambos rejeitam ou ambos não rejeitam $\implies$ Suspeita de quebra estrutural ou ruído não-linear; investigar quebras antes de diferenciar mecanicamente.

2. **Inspeção de Sazonalidade:**
   - Decomposição STL para inspecionar estabilidade do padrão sazonal.
   - Teste de raiz unitária sazonal (HEGY ou Canova-Hansen) se houver suspeita de sazonalidade estocástica.

---

## 2. Etapa 2: Quebras Estruturais e Regressão Segmentada

Ao testar mudanças estruturais na série da PIM-PF:

1. **Testes de Estabilidade Paramétrica:**
   - **Teste de Chow:** Válido para datas de quebra conhecidas *a priori* (ex.: Dez/2008 para Subprime, Mar/2020 para COVID-19).
   - **Testes CUSUM / MOSUM:** Flutuações empíricas dos resíduos recursivos para detectar instabilidade em momentos desconhecidos.
   - **Algoritmo de Bai & Perron (1998, 2003):** Estimação ótima de $m$ quebras estruturais endógenas desconhecidas via minimização da soma de quadrados dos resíduos (`strucchange::breakpoints`).

2. **Especificação de Segmentos:**
   - Distinguir quebra de nível (degrau / *level shift*, dummy de pulso ou de passo) de quebra de inclinação (mudança de tendência / *slope change*, termo $(t - \tau) I(t > \tau)$).
   - Verificar se os erros da regressão segmentada mantêm dependência serial e requerem modelagem SARIMA/ARIMAX conjunta.

---

## 3. Etapa 3: Avaliação de Modelos de Séries Temporais e GARMA

1. **Seleção de Ordens e Parsimônia:**
   - Análise de FAC e FACP amostrais.
   - Critérios de Informação com penalização severa para sobreparametrização: BIC (Schwarz) prioritário sobre AIC para amostras longas.
   - Checagem mandatória de invertibilidade e raízes unitárias dos polinômios AR e MA fora do círculo unitário.

2. **Modelos GARMA (Benjamin et al., 2003):**
   - Justificar a escolha da distribuição condicional (Gaussiana, Gama, t de Student) frente às características empíricas da série da indústria.
   - Verificar convergência numérica do algoritmo de escore / máxima verossimilhança condicional.
   - **Diagnóstico com Resíduos Quantílicos Aleatorizados (Dunn & Smyth, 1996):**
     * Como modelos lineares generalizados não possuem resíduos brutos normalmente distribuídos, os resíduos quantílicos aleatorizados devem ser computados.
     * Inspecione gráfico Q-Q com bandas de confiança simuladas.
     * Teste de Shapiro-Wilk sobre os resíduos quantílicos para avaliar adequação distributiva.

---

## 4. Etapa 4: Inferência Causal (Controle Sintético e DiD)

Para contrafactuais de choques macroeconômicos (ex.: Crise de 2014 via `analises/02_controlesintetico.qmd`):

1. **Validação do *Donor Pool*:**
   - Garantir que nenhum país doador tenha sofrido o mesmo choque simultaneamente ou tenha sido afetado por transbordamentos (*spillover effects* do Brasil).
   - Restrição estrita de convexidade: pesos não-negativos somando 1 ($w_j \ge 0, \sum w_j = 1$). Evitar extrapolação fora do suporte (*convex hull*).

2. **Ajuste Pré-Tratamento:**
   - Avaliar o erro quadrático médio de previsão pré-choque ($\text{RMSPE}_{\text{pré}}$).
   - Se o ajuste pré-choque for pobre, o contrafactual é metodologicamente inválido.

3. **Bateria Completa de Robustez e Placebos:**
   - **Placebos no Espaço (*In-Space Placebos*):** Aplicar o controle sintético sequencialmente a cada unidade do grupo de doadores.
   - **Razão de MSPE (*Post/Pre MSPE Ratio*):** Calcular a razão $\frac{\text{MSPE}_{\text{pós}}}{\text{MSPE}_{\text{pré}}}$ e computar o pseudo-p-valor por permutação exata.
   - **Placebo no Tempo (*Backdating*):** Fixar uma data fictícia de intervenção anterior (ex.: 2012) e demonstrar que o efeito causal estimado é nulo antes da crise real.
   - **Sensibilidade *Leave-One-Out*:** Reestimar excluindo doadores individuais de peso alto para certificar que o resultado não é dirigido por um único país.

---

## 5. Etapa 5: Diagnóstico Residual Universal

Nenhum modelo é aprovado sem os testes de resíduos:

| Diagnóstico | Teste Estatístico | Hipótese Nula ($H_0$) | Condição de Aprovação |
| :--- | :--- | :--- | :--- |
| **Autocorrelação** | Ljung-Box (defasagens $h=12, 24$) | Ausência de autocorrelação serial | $p > 0,05$ em todas as ordens |
| **Heterocedasticidade** | ARCH-LM (Engle) | Ausência de efeitos ARCH | $p > 0,05$ (ou modelagem GARCH/GARMA) |
| **Normalidade Residual** | Shapiro-Wilk / Jarque-Bera | Resíduos são Gaussianos | Inspecionar assimetria; obrigatório para IC Gaussiana |

---

## 6. Checklist de Aprovação

Ao finalizar a revisão, classifique o modelo em:
- [ ] **APROVADO:** Cumpre todos os requisitos, resíduos limpos, teoria justificada e placebos consistentes.
- [ ] **APROVADO COM RESSALVAS:** Resíduos razoáveis, mas com limitações documentadas de amostra ou sensibilidade.
- [ ] **REPROVADO:** Resíduos autocorrelacionados, quebras ignoradas, pressupostos violados ou placebos inconsistentes.

