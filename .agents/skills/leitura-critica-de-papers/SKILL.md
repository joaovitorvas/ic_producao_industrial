---
name: leitura-critica-de-papers
description: Protocolo sistemático de leitura crítica, avaliação de identificação causal e geração de fichamentos padronizados para a Iniciação Científica.
---

# Protocolo de Leitura Crítica e Fichamento de Artigos

Este protocolo orienta os agentes e o pesquisador na leitura analítica de artigos empíricos e teóricos de séries temporais, economia industrial e inferência causal, garantindo fichamentos padronizados e rastreabilidade para o Zotero e RAG.

---

## 1. Roteiro Sistemático de Leitura em 7 Dimensões

Ao processar qualquer artigo acadêmico para o projeto, extraia rigorosamente as seguintes sete dimensões:

### Dimensão 1: Identificação Bibliográfica e Metadados
- `paper_id`: Chave alfanumérica única (`sobrenomeANOtermo`, ex.: `abadie2021using`).
- `zotero_key`: Chave de item no Zotero.
- Autores, Ano, Título, Periódico, Volume, Número, Páginas e DOI.
- Palavras-chave metodológicas e temáticas.

### Dimensão 2: Pergunta de Pesquisa e Motivação Teórica
- Qual a pergunta exata que o artigo se propõe a responder?
- Qual a relevância teórica ou empírica dessa questão para a literatura econômica/estatística?
- Quais hipóteses substantivas ($H_1, H_2, \dots$) os autores formulam?

### Dimensão 3: Base de Dados e Desenho Amostral
- Qual a fonte primária dos dados (ex.: IBGE/PIM-PF, IPEADATA, FRED, Banco Central do Brasil)?
- Período coberto, frequência temporal (mensal, trimestral, anual) e granularidade geográfica/setorial.
- Transformações prévias efetuadas (ex.: ajuste sazonal via X-13, índice base, logaritmo, taxas de variação).

### Dimensão 4: Estratégia de Identificação e Estimação
- Qual o desenho de pesquisa adotado? (Série Temporal Univariada, Regressão Segmentada, GARMA, Controle Sintético, DiD, VAR/VEC).
- Quais são as equações formais do modelo?
- Quais pressupostos de identificação causal ou regularidade estatística são explicitamente assumidos pelos autores (ex.: trajetórias paralelas, SUTVA, exogeneidade estrita, estacionariedade)?

### Dimensão 5: Principais Resultados e Coeficientes
- Quais foram os valores estimados dos parâmetros centrais?
- Qual a significância estatística e a magnitude econômica dos efeitos reportados?
- As conclusões dependem criticamente de alguma escolha arbitrária de especificação?

### Dimensão 6: Diagnósticos e Bateria de Robustez
- Quais testes de resíduos foram apresentados (Ljung-Box, ARCH-LM, normalidade)?
- Quais verificações de sensibilidade foram feitas (mudança de subperíodos, controle de outliers, placebos espaciais/temporais)?

### Dimensão 7: Crítica Metodológica e Conexão com a Iniciação Científica
- Quais são as principais fragilidades do artigo (ameaças à validade interna ou externa)?
- O método é reproduzível com os dados e scripts fornecidos?
- **Conexão com a IC:** Como os achados ou a metodologia deste paper impactam diretamente a modelagem da produção industrial brasileira desenvolvida no IME-USP?

---

## 2. Template Padrão de Fichamento

Ao concluir a leitura crítica, o agente deve gerar o arquivo de fichamento correspondente em `literatura/fichamentos/<paper_id>-fichamento.md` seguindo rigorosamente a estrutura abaixo:

```markdown
---
paper_id: {paper_id}
title: "{Título Completo do Artigo}"
authors: [{Autor 1}, {Autor 2}]
year: {Ano}
journal: "{Nome do Periódico ou Conferência}"
doi: "{DOI}"
zotero_key: "{Zotero Key}"
data_fichamento: "YYYY-MM-DD"
avaliador: "{economista-industrial | econometrista | pesquisador}"
tags: [tag1, tag2, tag3]
---

# Fichamento Acadêmico: {paper_id}

## 1. Síntese Executiva (Abstract Expandido)
{Resumo conciso de 1 a 2 parágrafos cobrindo objetivo, método e principal achado}

## 2. Pergunta de Pesquisa e Hipóteses
- **Pergunta Central:** {Pergunta}
- **Hipótese 1:** {Hipótese}
- **Hipótese 2:** {Hipótese}

## 3. Dados e Contexto Empírico
- **Fonte dos Dados:** {Fontes}
- **Período e Frequência:** {Ex: 2002M01 a 2019M12 (Mensal)}
- **Tratamento das Variáveis:** {Logaritmo, deflacionamento, ajuste sazonal}

## 4. Metodologia e Equações Formais
{Especificação matemática completa com notação LaTeX}
$$
y_t = \alpha + \beta_1 t + \beta_2 (t - \tau) I(t > \tau) + \epsilon_t
$$

- **Pressupostos Assumidos:** {Listar pressupostos}
- **Estimador Utilizado:** {OLS, MLE, MCMC, Otimização Convexa}

## 5. Resultados Empíricos Centrais
| Variável / Parâmetro | Coeficiente | Erro-Padrão | Estatística t / p-valor |
| :--- | :--- | :--- | :--- |
| $\beta_1$ | ... | ... | ... |
| $\beta_2$ | ... | ... | ... |

## 6. Testes de Diagnóstico e Robustez Realizados
- **Resíduos:** {Diagnóstico de autocorrelação e heterocedasticidade}
- **Robustez:** {Testes placebos, quebras alternativas, janelas temporais}

## 7. Análise Crítica e Aplicação na Iniciação Científica
- **Pontos Fortes:** {Destaques metodológicos positivos}
- **Limitações e Ameaças à Validade:** {Fragilidades identificadas}
- **Utilidade Direta na IC (IME-USP):** {Como este paper fundamenta nossas escolhas de modelo para a PIM-PF}

## 8. Trechos e Citações Relevantes (com Página)
> "{Citação literal transcrita}" (p. {número})
```

---

## 3. Procedimento Operacional

1. Obtenha o arquivo Markdown convertido em `literatura/markdown/<paper_id>.md`.
2. Execute a leitura estruturada conforme as 7 dimensões.
3. Preencha o template de fichamento.
4. Salve em `literatura/fichamentos/<paper_id>-fichamento.md`.
5. Atualize o status na coluna `status_fichamento` de `literatura/indice/indice-papers.csv` para `concluido`.

