---
name: conversao-pdf-academico
description: Procedimento passo a passo para extração de PDFs acadêmicos, conversão para Markdown com equações KaTeX e registro em catálogo estruturado.
---

# Protocolo de Conversão e Conferência de PDFs Acadêmicos

Este protocolo define os procedimentos para transformar artigos científicos em formato PDF em documentos Markdown semânticos de alta fidelidade, garantindo a integridade de formulações matemáticas, tabelas e metadados para futura integração vetorial (RAG) e catálogo no Zotero.

---

## 1. Fluxo de Trabalho de Ponta a Ponta

```
[1. PDF Acadêmico] ──> Salvar em: literatura/pdf-original/<paper_id>.pdf
          │
          ▼
[2. Cadastro no Zotero] ──> Atribuir chave de citação (paper_id)
          │
          ▼
[3. Extração Semântica] ──> Conversão de texto, detecção de OCR e seções
          │
          ▼
[4. Higienização Matemática] ──> Validação de KaTeX ($...$ e $$...$$) e tabelas
          │
          ▼
[5. Inserção de Metadados] ──> Frontmatter YAML + marcadores <!-- page: X -->
          │
          ▼
[6. Salvamento do Markdown] ──> Salvar em: literatura/markdown/<paper_id>.md
          │
          ▼
[7. Registro no Catálogo] ──> Nova linha em: literatura/indice/indice-papers.csv
```

---

## 2. Padrões de Nomenclatura e Metadados

1. **Convenção do `paper_id`:**
   - Formato: `<sobrenome_primeiro_autor><ano><primeira_palavra_titulo_significativa>` (em letras minúsculas, sem acentos, sem hífens).
   - Exemplos:
     * Alencar & Rocha (2016) $\implies$ `alencar2016mudancas`
     * Abadie (2021) $\implies$ `abadie2021using`
     * Benjamin et al. (2003) $\implies$ `benjamin2003generalized`

2. **Frontmatter YAML Obrigatório no Topo do Markdown:**
   Cada arquivo em `literatura/markdown/<paper_id>.md` deve iniciar estritamente com:

```yaml
---
paper_id: alencar2016mudancas
title: "Mudanças de tendência da produção industrial no Brasil: uma análise empírica"
authors:
  - "Alencar, Airlane P."
  - "Rocha, Fabiana"
year: 2016
journal: "Revista Brasileira de Economia"
volume: 70
issue: 3
pages: "275-298"
doi: "10.5935/0034-7140.20160014"
zotero_key: "ALEN2016"
source_file: "literatura/pdf-original/alencar2016mudancas.pdf"
keywords: ["produção industrial", "regressão segmentada", "SARIMA", "quebra estrutural"]
---
```

---

## 3. Diretrizes de Formatação e Fidelidade Textual

1. **Marcadores de Página para Rastreabilidade:**
   - No início do texto correspondente a cada página do PDF original, insira um comentário HTML com o número da página:
     ```markdown
     <!-- page: 275 -->
     ```
   - Isso permite que o sistema RAG aponte exatamente a página original de onde um trecho ou citação foi recuperado.

2. **Formatação Matemática em KaTeX:**
   - Expressões no corpo do texto: delimitadas por um único cifrão (`$y_t$`, `$\beta_1$`).
   - Equações completas ou modelos em destaque: delimitadas por bloco de dois cifrões (`$$...$$`), com linhas em branco antes e depois:
     ```markdown
     $$
     \Phi(B) \Phi_s(B^s) (1 - B)^d (1 - B^s)^D Y_t = \Theta(B) \Theta_s(B^s) a_t
     $$
     ```
   - Verifique o fechamento de todos os cifrões para evitar falhas de renderização no Quarto ou no Antigravity.

3. **Formatação de Tabelas:**
   - Utilize sintaxe de tabela Markdown do GitHub (GFM).
   - Mantenha valores de coeficientes e erros-padrão em linhas distintas ou entre parênteses:
     ```markdown
     | Parâmetro | Estimativa | Erro-Padrão | Estatística t |
     | :--- | :---: | :---: | :---: |
     | Intercepto ($\alpha$) | 102.45 | (1.12) | 91.47*** |
     ```
   - Inclua as notas explicativas logo abaixo da tabela em itálico.

4. **Higienização de OCR:**
   - Elimine cabeçalhos e números de página repetitivos no topo/rodapé de cada página física (ex.: "Revista Brasileira de Economia - Vol. 70").
   - Corrija fusões tipográficas de OCR (`fi` lido como espaço ou símbolo estranho).

---

## 4. Registro no Catálogo Central (`literatura/indice/indice-papers.csv`)

Após a conversão, adicione a entrada correspondente ao arquivo de catálogo com a seguinte estrutura:

```csv
paper_id,title,authors,year,doi,source_file,zotero_key,status_fichamento,status_rag
alencar2016mudancas,"Mudanças de tendência da produção industrial no Brasil","Alencar & Rocha",2016,10.5935/0034-7140.20160014,literatura/pdf-original/alencar2016mudancas.pdf,ALEN2016,pendente,pendente
```

---

## 5. Checklist de Controle de Qualidade

Antes de considerar a conversão de um paper concluída:
- [ ] O arquivo está salvo com o nome exato `literatura/markdown/<paper_id>.md`.
- [ ] O frontmatter YAML está completo e válido.
- [ ] Todas as equações matemáticas estão formatadas e renderizam corretamente.
- [ ] As tabelas empíricas foram convertidas para Markdown GFM.
- [ ] Os marcadores `<!-- page: X -->` foram adicionados em todas as páginas.
- [ ] O PDF original foi salvo em `literatura/pdf-original/<paper_id>.pdf`.
- [ ] A linha correspondente foi adicionada em `literatura/indice/indice-papers.csv`.

