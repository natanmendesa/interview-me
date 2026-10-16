# Exemplos

Leia só se a forma de uma entrevista ou da saída não estiver óbvia. Em todos os exemplos o prompt final sai pronto para rodar, sem marcadores a preencher: os valores vêm das respostas do usuário, e o que ele não souber vira uma instrução para o modelo tratar.

## Exemplo 1: prompt vago de análise

**Prompt do usuário:** `analisa essa planilha de vendas e me diz o que tá errado, pensa bem e não erra`

**Auditoria (silenciosa):** falta o resultado esperado, qual dado está errado e o critério de pronto; "pensa bem e não erra" deve sair; não está claro se é para corrigir ou só relatar.

**Pré-mortem (silencioso):** o modelo corrige a planilha em vez de só relatar; devolve uma lista genérica ("verifique os totais") sem apontar células.

**Seis perguntas, em duas chamadas (4 + 2).** Note a recomendação nas decisões e a ausência dela nos fatos:
1. O que você quer receber? (Lista dos problemas, com célula e motivo **(Recomendada: "me diz o que tá errado" pede um diagnóstico, não uma planilha nova)** / Planilha corrigida / Os dois / Outro)
2. Pode corrigir direto ou só relatar? (Só relatar **(Recomendada: você não pediu correção)** / Corrigir e listar o que mudou)
3. Qual o nome do arquivo e da aba? (fato, sem recomendação: Vou digitar / Não sei)
4. O que já te parece errado? (fato: Totais / Datas / Duplicados / Não sei)
5. Como prefere a resposta? (Tabela **(Recomendada: erros por célula ficam mais fáceis de conferir em tabela)** / Lista / Você decide)
6. De onde vêm as regras de "certo"? (decisão: Só do que está na própria planilha **(Recomendada: você não citou outra fonte)** / Vou explicar as regras)

**Respostas do usuário (exemplo):** lista dos problemas, só relatar, `vendas_q3.xlsx` aba `Vendas`, totais e duplicados, tabela, só a planilha.

**Saída:**

```text
Na planilha vendas_q3.xlsx, aba Vendas, encontre os erros nos totais e nos duplicados e liste cada um com a célula e o motivo. Não altere o arquivo. Use só o que está na própria planilha para decidir o que é um erro.
Pronto quando: cada erro tiver a célula indicada e a conta que mostra por que está errado.
Responda com uma tabela: célula, problema, valor esperado.
```

**Rodar com:** Opus 5.5, esforço `medium`.
**Risco que o prompt corria:** o modelo corrigir a planilha por conta própria; agora o prompt diz que não altera o arquivo.
**O que mudou e por quê:** troquei "me diz o que tá errado" por um resultado concreto (lista com célula e motivo), porque o modelo para quando o critério é atendido. Tirei "pensa bem e não erra": o raciocínio é calibrado pelo esforço, e a conta ao lado de cada erro faz o papel de verificação.
**Rodar agora ou editar antes?**

## Exemplo 2: prompt já bom

**Prompt do usuário:** `Traduza o texto abaixo para o inglês, em tom formal, mantendo os nomes próprios e os números como estão. Devolva só a tradução.`

**Auditoria:** resultado, tom, restrições e formato presentes; nada contraditório; pré-mortem sem risco relevante.

**Saída:** nenhuma pergunta. `Já está bom. Pode rodar como está.` Mais uma linha de **Rodar com:** (modelo menor, esforço baixo).

## Exemplo 3: tarefa autônoma

**Prompt do usuário:** `migra todos os endpoints antigos pro novo formato e atualiza os testes`

**Lacunas:** quais endpoints e onde; critério de pronto; supervisão; o que pode fazer sozinho; o que fazer com os bloqueados; escopo dos arquivos.

**Pré-mortem:** o modelo para depois de alguns endpoints com um relatório; apaga arquivos antigos sem pedir; declara pronto sem rodar os testes.

**Seis perguntas:** onde estão os endpoints (fato: "Vou digitar" ou "Não sei"); comando que prova que terminou (fato); roda sozinho ou acompanhado (decisão, recomendada: sozinho, porque a tarefa é mecânica e longa); o que exige aprovação (decisão, recomendada: apagar código e mexer fora da pasta); o que fazer com um endpoint bloqueado (decisão, recomendada: dizer o que bloqueia e seguir com os demais); como o resultado final deve vir (estilo, com "Você decide").

**Respostas (exemplo):** `src/api/v1`, `npm test -- api-v1`, sozinho, aprovar apagar e mexer fora da pasta, seguir com os demais, resumo curto.

**Saída:**

```text
Migre todos os endpoints de src/api/v1 para o novo formato e atualize os testes de cada um.
Pronto quando: npm test -- api-v1 passa e nenhum endpoint antigo resta em src/api/v1.
Rode até terminar tudo. Se um endpoint estiver bloqueado, diga o que o bloqueia e siga com os demais. Peça confirmação antes de apagar arquivos ou de mexer fora de src/api/v1.
Ao terminar, abra com o resultado em uma frase e liste só os endpoints que ficaram bloqueados.
```

**Rodar com:** Opus 5.5, esforço `medium`.
**Risco que o prompt corria:** parar no meio com um relatório de progresso; agora há condição de conclusão e a regra do que fazer com endpoints bloqueados.

**Se o usuário respondesse "Não sei" onde ficam os endpoints:** escreva "Localize os endpoints antigos do projeto e migre todos..." em vez de deixar um campo em branco.

## Exemplo 4: depurar

**Entrada do usuário:** `/interview-me depurar` com o prompt `resuma esse contrato para mim` e a resposta, que veio com 900 palavras e listas aninhadas.

**Causa:** "resuma" não diz tamanho nem destino, então o modelo escolheu cobrir todas as cláusulas, e o formato em listas veio do padrão dele.

**Correção mínima:**

```text
Resuma o contrato que vem a seguir, entre as tags <contrato>, em até 150 palavras, em parágrafos corridos, destacando prazo, valor e multas. Vou ler no celular.
```

Instrução ao usuário na mesma resposta: cole o contrato logo depois da instrução, dentro de `<contrato>` e `</contrato>`.

**Como confirmar:** a resposta deve vir com no máximo 150 palavras e sem listas.
**O que mudou e por quê:** o modelo só limita o tamanho quando você diz o limite; o destino ("no celular") ajuda a escolher o formato. O contrato vai depois da instrução e entre tags para ser tratado como dado.
**Rodar de novo?**
