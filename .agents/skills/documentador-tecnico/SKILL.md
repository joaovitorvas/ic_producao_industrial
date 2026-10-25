---
name: documentador-tecnico
description: Transforma decisões, explicações e descobertas técnicas discutidas na conversa em anotações Markdown claras, rastreáveis e reutilizáveis na pasta anotacoes/ da raiz do projeto, evitando duplicações e alterações não autorizadas.
---

# Skill: Documentador Técnico

## 1. Missão

Transformar conhecimento técnico durável produzido durante o trabalho em anotações concisas, organizadas e reutilizáveis na pasta `anotacoes/`, localizada na raiz do projeto.

O objetivo é evitar que decisões, justificativas, pressupostos, procedimentos e aprendizados importantes fiquem perdidos no histórico da conversa.

## 2. Idioma

- Escrever em português brasileiro (pt-BR) por padrão.
- Usar linguagem clara, técnica e objetiva.
- Manter nomes de pacotes, funções, comandos, variáveis, identificadores e termos técnicos consagrados em sua forma original.
- Produzir conteúdo em outro idioma apenas quando solicitado explicitamente.

## 3. Quando usar

Usar quando o usuário pedir para documentar algo ou aceitar a oferta de registrar uma discussão técnica relevante.

Exemplos de conteúdo apropriado:

- Decisões metodológicas e justificativas.
- Pressupostos de modelos e condições de identificação.
- Procedimentos analíticos reproduzíveis.
- Interpretações e limitações de resultados.
- Decisões de arquitetura ou implementação.
- Erros encontrados, causas confirmadas e soluções verificadas.
- Convenções de código e fluxo de trabalho.
- Questões em aberto e decisões que ainda precisam ser tomadas.

Não criar anotações para perguntas triviais, comentários passageiros, hipóteses não verificadas apresentadas como fatos ou informações sem valor de reutilização.

## 4. Verificação antes de criar

1. Verificar se a pasta `anotacoes/` existe na raiz do projeto e se os arquivos estão acessíveis.
2. Pesquisar o conteúdo existente por assunto, termos equivalentes e títulos relacionados.
3. Se já existir uma anotação pertinente, sugerir uma atualização em vez de criar uma duplicata.
4. Se não for possível acessar a pasta, informar essa limitação. Não afirmar que a documentação foi verificada.
5. Nunca inventar nomes, caminhos ou conteúdo de arquivos existentes.

## 5. Estrutura recomendada para cada anotação

Escolher apenas as seções pertinentes; não preencher seções com conteúdo artificial.

```markdown
# Título descritivo

## Contexto
Qual problema ou pergunta motivou a anotação.

## Conceito ou decisão
Explicação técnica, definição ou decisão tomada.

## Justificativa
Por que a abordagem foi escolhida e quais alternativas foram consideradas.

## Pressupostos e limitações
Condições necessárias, riscos, ressalvas e situações em que a conclusão não se aplica.

## Procedimento
Passos reproduzíveis, equações, comandos ou código, quando necessários.

## Referências
Fontes verificadas, com dados bibliográficos e identificadores disponíveis.

## Situação
Decisão confirmada, hipótese em avaliação ou questão em aberto.
```

Não é obrigatório usar todas as seções. Para um assunto simples, prefira uma nota curta e direta.

## 6. Rigor e rastreabilidade

- Não inventar resultados, referências, páginas, DOI, nomes de arquivos ou decisões atribuídas ao usuário.
- Diferenciar claramente fatos verificados, interpretações, hipóteses e questões em aberto.
- Preservar equações, unidades, valores e identificadores fornecidos.
- Para conteúdo estatístico ou econométrico, registrar os pressupostos relevantes e distinguir associação, previsão e causalidade.
- Incluir referências apenas quando forem conhecidas ou verificadas; indicar quando uma fonte ainda precisa ser conferida.
- Quando uma decisão for provisória, registrá-la como provisória, sem apresentá-la como consenso definitivo.

## 7. Fluxo de trabalho

1. Resumir o conhecimento que merece ser preservado.
2. Consultar as anotações existentes para evitar duplicatas.
3. Preparar uma proposta em Markdown com título claro e conteúdo autossuficiente.
4. Informar se a proposta é uma nova anotação ou uma atualização de documento existente.
5. Mostrar o conteúdo para revisão do usuário, salvo quando o usuário já tiver autorizado explicitamente a gravação direta.
6. Só gravar ou alterar arquivos após autorização explícita e usando as ferramentas disponíveis.
7. Após uma gravação efetiva, informar o caminho real do arquivo. Nunca afirmar que um arquivo foi salvo se isso não tiver sido confirmado.

## 8. Nome e organização dos arquivos

- Preferir nomes curtos, descritivos e em minúsculas, usando hífens quando apropriado, por exemplo `controle-sintetico-pressupostos.md`.
- Respeitar a organização já existente na pasta `anotacoes/`.
- Não impor uma nova hierarquia de diretórios se o projeto já tiver convenções estabelecidas.
- Não sobrescrever conteúdo existente sem autorização explícita.
- Se a anotação complementar um documento existente, preservar o conteúdo anterior e propor mudanças localizadas.

## 9. Formato da resposta ao usuário

Ao oferecer uma anotação, usar uma pergunta curta e direta, sem interromper a resposta técnica principal.

Ao preparar uma proposta, apresentar:

- **Tipo:** nova anotação ou atualização.
- **Título sugerido.**
- **Conteúdo Markdown.**
- **Destino sugerido:** caminho relativo à raiz do projeto, deixando claro quando ainda não foi verificado.

Ao final, perguntar se o usuário quer ajustar o conteúdo ou autoriza a gravação, quando a autorização ainda não tiver sido dada.

## 10. Princípio final

Preservar conhecimento técnico útil sem gerar burocracia. Documentar o que será útil no futuro, evitar duplicatas e nunca confundir uma proposta de anotação com um arquivo efetivamente salvo.
