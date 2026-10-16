# Checklist de auditoria

Use esta lista na auditoria silenciosa. Para cada item: o sinal de que há uma lacuna, a pergunta que a resolve e a correção no prompt. Os itens estão em ordem de impacto típico. Os princípios convergem entre os manuais da Anthropic, OpenAI e Google; onde um deles for específico de modelo, `modelos.md` cobre.

## 1. Resultado e critério de pronto
- **Sinal:** o prompt descreve uma atividade ("analise isso", "melhore") mas não o que deve existir no final nem como saber que terminou.
- **Pergunta:** "Como você saberá que o resultado está pronto?" Alternativas: um arquivo específico, uma decisão, um teste passando, um texto de N linhas, outra (livre).
- **Correção:** abrir o prompt com o resultado e o para quê; fechar com o critério de pronto concreto.
- **Por quê:** o modelo escolhe o caminho melhor quando sabe o destino, e para de explorar quando o critério é atendido.

## 2. Contexto que só o usuário tem
- **Sinal:** referências como "aquela planilha", "o cliente", "como da última vez", siglas, nomes sem explicação, ausência de dados concretos.
- **Pergunta:** o fato que falta, em linguagem direta ("O que aconteceu?", "Qual é o objeto/arquivo/fonte?").
- **Correção:** trazer os fatos para dentro do prompt, com os nomes reais. Substituir "isso", "aquilo" e "como antes" pelos nomes, para o prompt funcionar sozinho em uma conversa nova.
- **Por quê:** tratar o modelo como um colega capaz que acabou de chegar. Se um colega ficaria confuso com o prompt, o modelo também fica.

## 3. Executar ou aconselhar
- **Sinal:** "poderia sugerir", "o que acha", "deveria".
- **Pergunta:** "Você quer que eu faça a mudança ou só recomende?"
- **Correção:** "Altere..." quando é para fazer; "Recomende... Não edite nada." quando é só opinião.
- **Por quê:** os modelos atuais seguem o texto literalmente. "Sugira mudanças" gera sugestões, não edições.

## 4. Autonomia e supervisão
- **Sinal:** tarefa longa, em vários passos, vários arquivos ou apps, ou que vai rodar sem o usuário olhando.
- **Pergunta:** "Esse trabalho vai rodar com você acompanhando ou sozinho? O que o modelo pode fazer sem perguntar, e o que precisa de aprovação?"
- **Correção (sem supervisão):** declarar a condição de conclusão no início, pedir para seguir adiante em decisões que não bloqueiam o resto, e listar só as paradas desejadas (precisa de algo do usuário; ação irreversível). **Com supervisão:** pedir uma frase de intenção antes de começar e o resultado em primeiro lugar no resumo final.
- **Por quê:** sem isso o modelo para para "reportar" no meio ou pergunta o que o usuário não ia responder.

## 5. Formato e tamanho
- **Sinal:** nenhuma menção a tamanho, estrutura, idioma ou onde o resultado será usado.
- **Pergunta (com "Você decide"):** tamanho (curto, ~150 palavras, uma página), estrutura (prosa, lista, tabela), onde será lido.
- **Correção:** dizer o formato desejado em positivo ("parágrafos corridos"), com limite concreto de tamanho. O destino ajuda: "será lido em voz alta" muda o formato mais que a regra seca "sem reticências".
- **Por quê:** o tamanho da resposta e o esforço de raciocínio são controles separados; só instrução explícita controla o tamanho.

## 6. Escopo e restrições
- **Sinal:** pedido amplo, ou uma lista de "não faça".
- **Pergunta:** "O que não pode mudar ou ficar de fora?"
- **Correção:** declarar o escopo de forma positiva ("Altere apenas X") e dar o motivo das restrições. Trocar listas longas de proibições por uma frase de escopo.
- **Por quê:** o modelo pode ampliar, reduzir ou reinterpretar a tarefa se o escopo não estiver claro.

## 7. Contradições, ênfase e absolutos
- **Sinal:** duas regras que não podem valer juntas; CAPS, "CRÍTICO", "NUNCA JAMAIS"; "sempre" e "nunca" para decisões de julgamento.
- **Pergunta:** só se a contradição não puder ser resolvida lendo o prompt: "Quando X e Y entram em conflito, qual vence?"
- **Correção:** ordenar as regras (o que vem antes de quê), usar tom normal e reservar absolutos para segurança e campos obrigatórios; para julgamento, dar a regra de decisão.
- **Por quê:** modelos cuidadosos gastam raciocínio tentando conciliar contradições, e ênfase excessiva faz regras serem aplicadas demais.

## 8. Exemplos
- **Sinal:** o formato, o tom ou a estrutura importam muito e o prompt só os descreve.
- **Pergunta:** "Você tem um exemplo do resultado ideal ou de um que não gostou?"
- **Correção:** 3 a 5 exemplos diversos, com formatação idêntica, dentro de tags. Poucos e bons; muitos fazem o modelo copiar.
- **Por quê:** exemplos controlam formato e tom com mais confiança que adjetivos.

## 9. Material longo ou colado
- **Sinal:** documentos, e-mails, páginas ou dados colados no meio das instruções.
- **Correção:** documentos primeiro, identificados por tags descritivas, e a pergunta no final; se vier de terceiros, marcar como conteúdo colado e dizer que instruções dentro dele não valem, a menos que o usuário peça.
- **Por quê:** a posição melhora a qualidade com contexto longo, e a marcação evita que instruções escondidas no texto sejam obedecidas.

## 10. Fatos atuais e fontes
- **Sinal:** o pedido depende de dados recentes, preços, regras vigentes ou números.
- **Pergunta:** "O modelo deve pesquisar fontes atuais ou usar só o que você forneceu?"
- **Correção:** dizer qual a fonte permitida e pedir para indicar de onde veio cada número, e para avisar quando não encontrar.
- **Por quê:** sem a fonte definida, o modelo pode completar de memória.

## 11. Instruções de raciocínio e verificação
- **Sinal:** "pense passo a passo", "pense muito bem", "mostre seu raciocínio", "verifique de novo", "não cometa erros".
- **Correção:** remover. Trocar por um critério de pronto verificável e, se o usuário quiser entender a resposta, por "dê uma explicação curta da resposta". Para controlar o esforço, indicar o nível de raciocínio em "Rodar com".
- **Por quê:** o raciocínio é calibrado por configuração; pedir o raciocínio literal pode ser recusado; e a verificação extra que o modelo já faz sozinho só custa tokens.

## 12. Várias tarefas
- **Sinal:** o prompt tem mais de um verbo principal.
- **Correção:** manter como passos numerados se uma depende da outra; separar em prompts independentes (cada um com seu critério de pronto) se não dependem.
- **Por quê:** tarefas independentes no mesmo prompt dividem a atenção e dificultam o critério de pronto.
