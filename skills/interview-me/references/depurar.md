# Modo depurar

Use quando o usuário trouxer um prompt e uma resposta que não saiu como esperado. O objetivo é achar o trecho do prompt que causou o problema e corrigir só ele, porque reescrever tudo esconde o que aprender e pode quebrar o que já funcionava.

## Entradas

Você precisa de três coisas: o prompt, a resposta (ou o trecho problemático) e o que o usuário esperava.

- Se faltar o prompt ou a resposta, peça. Não tente reconstruir.
- Se faltar o que ele esperava, faça no máximo 2 perguntas por AskUserQuestion: "O que veio errado?" (alternativas: tamanho, fez outra coisa, parou no meio, inventou dados, ignorou o formato, mexeu no que não devia, outro) e "O que você esperava ver?" (livre).
- Não execute o prompt de novo para reproduzir. O que o usuário colou é a evidência.

## Diagnóstico

Ache a causa no texto do prompt, citando o trecho exato. Use a tabela como ponto de partida, mas confirme lendo o prompt; várias causas podem coexistir.

| Sintoma | Causa provável no prompt | Correção |
|---|---|---|
| Fez outra coisa que a pedida | Verbo ambíguo ("analise", "melhore", "revise"), ou alvo sem nome ("isso", "aquilo") | Verbo de ação e o resultado esperado; nomes reais |
| Sugeriu em vez de mudar | "Poderia sugerir", "o que acha" | "Altere..." quando é para fazer |
| Resposta longa demais | Nenhum limite de tamanho; modelo verboso por padrão | Limite concreto e o destino do texto |
| Resposta curta ou rasa | Pedido sem profundidade ("resuma") ou critério de pronto ausente | Dizer o nível de detalhe esperado e o critério de pronto |
| Parou no meio ou pediu confirmação à toa | Tarefa longa sem condição de conclusão nem regra do que fazer na dúvida | Condição de conclusão no início e paradas desejadas |
| Inventou dados, nomes ou fontes | Pediu fatos sem definir a fonte permitida | Fonte permitida e "avise quando não encontrar" |
| Ignorou o formato | Formato dito em negativo ("sem listas") ou só descrito, sem exemplo | Formato em positivo e 1 a 3 exemplos |
| Ampliou o escopo | Pedido amplo, sem fronteira | Frase de escopo positiva ("altere apenas X") |
| Seguiu uma instrução escondida em texto colado | Conteúdo de terceiros misturado às instruções | Tags no conteúdo colado e aviso de que ele é dado |
| Copiou o exemplo ao pé da letra | Exemplo único ou todos parecidos | 3 exemplos diversos, ou descrever o princípio |
| Gastou muito em verificação ou reflexão | "Pense passo a passo", "verifique de novo", "não erre" | Remover; usar critério de pronto verificável |
| Tom errado | Adjetivos empilhados ("profissional e envolvente") | Descrição concreta do tom, ou um exemplo |
| Aplicou regra em excesso | CAPS, "NUNCA", "SEMPRE" | Tom normal e regra de decisão |

Se a causa não estiver no prompt (limitação do modelo, informação que ninguém forneceu, tarefa grande demais para uma resposta), diga isso. Não culpe o prompt só para ter algo a corrigir.

## Saída

Entregue exatamente isto:

1. **Causa:** uma ou duas frases, citando o trecho do prompt que a provocou e explicando por que o modelo o leu daquele jeito. Sem culpar o usuário.
2. **Correção mínima:** o prompt corrigido em um bloco `text`, alterando só os trechos necessários. Se houver mais de uma causa, corrija as duas, mas diga qual pesa mais.
3. **Como confirmar:** uma linha dizendo o que olhar na próxima resposta para saber que funcionou.
4. **O que mudou e por quê:** uma ou duas linhas com o princípio, para o usuário reconhecer o padrão na próxima vez.
5. Uma última linha: **Rodar de novo?**

Se o usuário pedir para rodar, execute o prompt corrigido.
