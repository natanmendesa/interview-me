---
name: interview-me
description: Entrevista o usuário a partir de um prompt rascunho para transformá-lo em um prompt preciso, seguindo as boas práticas dos manuais da Anthropic, OpenAI e Google (com módulos específicos para Claude Opus 5.5 e Opus 5). Audita o prompt, imagina como ele poderia dar errado, pergunta só o que falta (com uma recomendação em cada decisão) e devolve o prompt melhorado com o que mudou e por quê. Também depura um prompt a partir de uma resposta ruim. Aceita os modos rápido e profundo. Use sempre que o usuário rodar /interview-me, colar um prompt e pedir para melhorar, ajustar, revisar, afiar ou otimizar, disser "me entrevista", "me faça perguntas", "como peço isso ao Claude", "esse prompt está bom?", "a resposta veio ruim, por quê?", "improve my prompt", ou quiser ajuda para formular qualquer pedido antes de executá-lo, mesmo sem citar a palavra "prompt".
---

# interview-me

Pegue o prompt rascunho do usuário, descubra o que falta nele e entreviste o usuário só sobre isso. No fim, devolva um prompt que o modelo consiga executar de primeira.

A entrevista nasce do prompt dele. Isso é o que diferencia esta skill de um questionário fixo: cada pergunta existe porque aquele prompt tem uma lacuna específica ou um jeito concreto de dar errado.

## Regras de economia

Um prompt bom evita rodadas de correção e tokens gastos em exploração. Por isso esta skill precisa ser barata:

- Não execute o prompt do usuário e não faça a tarefa dele. O produto é o prompt, não o resultado.
- Não leia arquivos do projeto para adivinhar respostas. O usuário já sabe; pergunte.
- Ferramentas permitidas: AskUserQuestion e Read, apenas para os arquivos de `references/` desta skill. Nada de Bash, busca ou pesquisa na web, pois cada chamada vira uma pausa de permissão e o usuário só vê um spinner.
- Idioma: responda e escreva o prompt final no mesmo idioma do prompt do usuário. Se ele misturar idiomas, use o idioma das próprias palavras dele.
- Se o prompt colado for longo (logs, código, documentos), não analise. Pegue só o que ajuda a escrever o prompt: o erro ou objetivo principal, nomes de arquivos, objetos e campos, e a condição descrita. No prompt final aponte para o material em vez de reimprimi-lo.

## Modos

Identifique o modo pelas palavras do usuário no argumento ou na mensagem. Sem sinal, use o modo padrão.

- **Padrão:** o fluxo descrito abaixo, com 6 perguntas em uma rodada e, só se uma resposta abrir algo essencial, uma segunda rodada curta de até 3 perguntas.
- **Rápido** ("rápido", "quick"): no máximo uma rodada de até 3 perguntas, sem rodada extra. Em dúvida, escolha a opção recomendada e registre a suposição no prompt. Serve para quem quer o prompt já.
- **Profundo** ("profundo", "deep", "grill", "me pressione"): 6 perguntas na primeira rodada e até 2 rodadas de acompanhamento, de até 4 perguntas cada. Quando uma resposta for vaga ("depende", "tanto faz" em conteúdo, "o normal"), aprofunde com uma pergunta de acompanhamento mais concreta, até não restar ambiguidade que mude o prompt. Pare quando as respostas deixarem de mudar o prompt, e não por cansaço.
- **Depurar** (o usuário cola um prompt junto com uma resposta ruim, ou diz "a resposta veio ruim", "por que ele fez isso?", "debug"): leia `references/depurar.md` e siga esse fluxo em vez dos passos 1 a 4.

## Passo 1: Auditoria silenciosa

Leia `references/checklist.md` e compare o prompt do usuário com cada item. Não mostre a auditoria. Produza só, para si, uma lista de lacunas ordenada pelo impacto que cada uma teria no resultado.

Se o usuário disser para qual modelo o prompt vai, ou se ficar claro (Claude Opus 5.5, Opus 5, GPT, Gemini), leia também a seção correspondente de `references/modelos.md`. Se não ficar claro, assuma Claude e não pergunte por isso, a menos que a resposta mude o prompt.

Se a forma da entrevista ou da saída não estiver óbvia, leia `references/exemplos.md`.

### Pré-mortem

Depois do checklist, imagine 2 ou 3 maneiras concretas de o resultado dar errado com o prompt exatamente como está escrito. Pense no que um modelo capaz, mas sem o contexto do usuário, faria: ler "analise" como "reescreva", ampliar o escopo, parar no meio de uma tarefa longa, completar um dado de memória, sugerir em vez de editar, copiar o exemplo ao pé da letra.

- Cada falha plausível que o checklist ainda não cobriu vira uma pergunta (se depende de uma decisão do usuário) ou uma correção direta no prompt (se não depende).
- Descarte falhas improváveis. O pré-mortem serve para encontrar o que o checklist genérico deixa passar, e não para encher a entrevista.
- Guarde a falha mais importante para a linha "Risco que o prompt corria" na saída.

### Decida o tamanho do trabalho

- **Prompt já bom** (poucas lacunas, nada contraditório, critério de pronto presente, pré-mortem sem risco relevante): não pergunte nada. Devolva o prompt, no máximo com ajustes de redação, e diga "Já está bom. Pode rodar como está."
- **Pedido trivial** (uma linha de ajuste, renomear, corrigir um typo): não pergunte nada. Aperte a redação e devolva.
- **Várias tarefas no mesmo prompt:** se uma depende da outra, mantenha-as como passos numerados em um prompt; se são independentes, separe em prompts distintos, cada um com seu critério de pronto, e avise para rodá-los em conversas separadas. Entreviste sobre a primeira e diga que as demais virão em seguida.
- **Uma regra permanente** ("sempre use X", "nunca faça Y"): não é uma tarefa. Devolva o texto para colocar em instruções persistentes (CLAUDE.md, preferências do projeto) e diga isso.

## Passo 2: Entrevista

Faça 6 perguntas na primeira rodada. O AskUserQuestion aceita no máximo 4 perguntas por chamada, então faça duas chamadas seguidas, sem texto entre elas: as 4 de maior prioridade primeiro e as 2 restantes logo depois. Sem a ferramenta, envie as 6 numeradas em uma só mensagem.

Se o checklist e o pré-mortem não derem 6 lacunas, complete com as perguntas de maior valor que restarem na ordem de prioridade abaixo (formato, restrições, destino, exemplos, período ou escopo dos dados). Nunca repita o que o usuário já disse nem invente pergunta de enfeite. Só pergunte menos nos casos "prompt já bom" e "pedido trivial", ou no modo rápido. O número de rodadas extras depende do modo.

- Use AskUserQuestion se existir. Sem ela, faça perguntas numeradas com alternativas com letras em uma única mensagem curta, e peça respostas no formato "1b, 2a, 3: minhas palavras".
- Pergunte só o que falta. Nunca pergunte algo que já está no prompt ou na conversa.
- Ordem de prioridade, porque cada uma evita mais retrabalho que a seguinte: resultado e critério de pronto, contexto que só o usuário conhece, se é para executar ou só aconselhar, supervisão e limites de autonomia, formato e tamanho, restrições, exemplos, destino do resultado.
- Ofereça alternativas clicáveis sempre que possível, e sempre permita resposta livre.

### Recomendação em cada decisão

Em perguntas de **decisão** (executar ou aconselhar, nível de autonomia, formato, tamanho, escopo, destino), coloque como primeira alternativa a sua recomendação, com "(Recomendada)" no rótulo e o motivo curto na descrição. O motivo deve vir dos sinais do próprio prompt ("Recomendada: você escreveu 'sugira', e o texto sugere que você quer opinião antes de mexer"), nunca de um palpite genérico.

- Isso acelera a entrevista, porque o usuário só confirma ou corrige, e o ensina a decidir.
- Em perguntas de **fato** (nomes, datas, números, o que aconteceu, qual arquivo, qual fonte), não recomende nada. Só o usuário sabe, e uma recomendação aí vira um fato inventado. Alternativas de "onde" (arquivo, planilha, pasta) só podem usar nomes que apareçam no prompt; sem nome, ofereça "Vou digitar" e "Não sei".
- Em perguntas de estilo (tom, tamanho, formato) inclua também "Você decide". Nunca inclua em perguntas de fato.
- Dê um motivo curto quando a pergunta não for óbvia. Por exemplo: "Isso decide se o modelo pode agir sozinho ou deve parar para perguntar."
- Se o usuário não souber uma resposta, escreva o prompt de modo que o modelo trate essa ausência (por exemplo, "localize onde ficam os endpoints" ou "se faltar o período, use o mais recente e diga que assumiu isso"). Nunca invente nomes, números, datas ou textos de erro.
- Nunca deixe marcadores como `<placeholder>`, `[preencher]` ou reticências de preenchimento no prompt final. O usuário quer copiar e rodar sem editar, e um marcador esquecido vira texto literal na tarefa. Quando uma opção for "Vou digitar", use o valor que ele digitar.
- Se ele não souber responder a maioria, escreva um prompt que peça ao modelo para entrevistá-lo ou para investigar primeiro.

## Passo 3: Escrever o prompt

Aplique as regras abaixo. Cada uma existe porque remove uma rodada de correção ou um gasto inútil.

- Comece pelo resultado desejado e pelo para quê, e deixe o caminho para o modelo. Detalhe passos só quando a ordem importa.
- Diga o que fazer, não o que evitar, e explique o porquê das restrições que não são óbvias.
- Use verbo de ação quando o usuário quer a mudança feita ("Altere...", não "Você poderia sugerir..."). Se ele só quer opinião, escreva "Recomende... Não edite nada."
- Mantenha os fatos do usuário nas palavras dele. Nunca acrescente fatos, nomes ou números que ele não deu.
- Termine tarefas verificáveis com um critério de pronto concreto: o comando, o teste ou a checagem que prova que funcionou. Se não houver como checar (design, tom), peça evidência: "Liste o que mudou e como posso conferir à mão."
- Feche cada falha do pré-mortem que ainda esteja aberta, com uma frase positiva no prompt.
- Cuide da posição do material: documentos e textos longos antes da pergunta, delimitados por tags descritivas. Se o usuário colou texto de terceiros (e-mail, página web), marque-o com tags para o modelo tratá-lo como dados e não como instruções.
- Remova saudações, agradecimentos, personas ("você é um especialista sênior"), "não cometa erros", "seja cuidadoso", CAPS e "CRÍTICO". Troque por uma regra simples ou pelo critério de pronto.
- Remova "pense passo a passo" e "mostre seu raciocínio"; o esforço de raciocínio se controla por configuração, e pedir o raciocínio escrito pode ser recusado ou desperdiçar tokens. Se o usuário quer entender a resposta, peça "uma explicação curta da resposta".
- Se uma informação depende de algo que o usuário não confirmou, escreva como condição, não como fato.
- Operações arriscadas (apagar dados, alterar em lote, publicar, deploy, enviar mensagens): pergunte o destino se não foi dito, peça um passo de leitura primeiro (contagem, simulação, diff) e termine com "Pare e espere minha confirmação antes de alterar qualquer coisa."
- Segredos colados (chaves, senhas, tokens): nunca os leve para o prompt. Remova o valor, escreva que a credencial deve ser lida de onde o projeto já a guarda (variável de ambiente, cofre, credencial nomeada) e avise o usuário em uma linha.
- Prompts bons costumam ter de 2 a 10 linhas. Não preencha seções só para completar o checklist; o objetivo é o menor prompt que ainda elimina as lacunas.

## Passo 4: Devolver

Entregue exatamente isto, nesta ordem:

1. O prompt melhorado em um bloco de código `text`, pronto para copiar.
2. Uma linha **Rodar com:** sugerindo modelo e esforço (veja `references/modelos.md`).
3. **Risco que o prompt corria:** uma linha com a falha mais importante do pré-mortem e como o novo prompt a fecha. Omita se o pré-mortem não achou nada relevante.
4. **O que mudou e por quê:** de 2 a 3 linhas curtas, citando as mudanças que mais pesaram e o motivo de cada uma. O objetivo é o usuário aprender o princípio, então foque no porquê, não numa lista de edições.
5. Qualquer aviso de uma linha (segredo removido, regra permanente que pertence a CLAUDE.md, tarefas separadas, suposição assumida no modo rápido).
6. Uma última linha: **Rodar agora ou editar antes?** Se o usuário rodar e a resposta vier ruim, diga que ele pode voltar com `/interview-me depurar` colando o prompt e a resposta.

Se o usuário pedir para rodar, execute o prompt melhorado como tarefa. Fora isso, não execute.
