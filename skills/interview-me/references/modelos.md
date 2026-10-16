# Ajustes por modelo

Leia só a seção do modelo para o qual o prompt vai. As recomendações foram medidas em modelos específicos; avise o usuário que vale testar no caso dele.

Fontes: documentação da Anthropic (Prompting best practices, Prompting Claude Opus 5, Prompting Claude Opus 5.5, Effective context engineering), guia de prompting da OpenAI e estratégias de prompt da Gemini API.

## Escolha de modelo e esforço para a linha "Rodar com:"

Sugira com base no tipo de tarefa, e diga que o esforço é o controle principal de raciocínio, custo e latência (baixar o esforço reduz o raciocínio de forma mais confiável que pedir por texto).

- Texto curto, reescrita, tradução, pergunta simples: um modelo menor, esforço baixo.
- Trabalho normal de código, análise ou escrita com julgamento: Claude Opus 5.5 no esforço padrão (`medium`). Segundo a Anthropic, ele iguala ou supera o Opus 5 em `high` nesse nível.
- Trabalho longo e autônomo, varredura de muitos arquivos ou auditoria: Opus 5.5 em `medium` ou acima, com condição de conclusão no prompt.
- `xhigh` e `max` só onde o usuário já mediu ganho de qualidade; senão custam mais sem garantia.

## Claude Opus 5.5

- **Esforço:** o padrão é `medium`. Em vez de "pense com cuidado", ajuste o esforço. Em chat, essa frase só atrasa o início da resposta.
- **Tarefas longas e sem supervisão:** o modelo atualiza o usuário no meio do trabalho e pode encerrar o turno com um relatório. Declare no começo a condição de conclusão e as paradas desejadas (precisa de algo do usuário; ação irreversível), e peça notas de status junto com a próxima ação em vez de parar para relatar.
- **Vários apps:** em tarefas pouco especificadas, peça para explorar antes de agir: abrir e-mails, documentos, abas e registros que possam ser relevantes, inclusive os que o pedido não cita, e usar o que achar. Vale manter conteúdo não confiável fora dos registros pesquisados.
- **Vários agentes:** um orçamento de tempo ajuda a paralelizar ("termine em X minutos"). Sem estimativa, "tempo importa: quanto antes o resultado correto, melhor".
- **Texto colado:** marque o que o usuário colou de outro lugar com tags, para o modelo não obedecer instruções escondidas ali.
- **Conversas multi-turno:** se o usuário não quer que o modelo reabra respostas anteriores, "trate o que já respondeu como resolvido e foque no que está sendo pedido agora." Evite em análises longas, onde reexaminar é útil.
- **Raciocínio escrito:** pedir para escrever o raciocínio na resposta pode ser recusado. Peça uma explicação curta ou um resumo das ações.
- **Imagens densas (gráficos, diagramas, desenhos técnicos):** ele lê melhor que modelos anteriores; para as mais densas, ajudam resolução maior e ferramentas de recorte e zoom.
- **Frontend sem direção de design:** o resultado cai em estilos padrão. Nomeie padrões específicos a evitar (fundos creme, rótulos monoespaçados, botões em pílula, seções numeradas "01/02/03") em vez de "evite o visual genérico de IA", e itere conforme o resultado.

## Claude Opus 5 e modelos Claude anteriores

- **Verbosidade:** o Opus 5 responde mais longo por padrão, e o esforço não controla isso. Peça concisão explicitamente, com um lembrete curto perto do fim de prompts longos.
- **Verificação:** remova pedidos como "verifique de novo" e "use um subagente para conferir". Ele já se corrige, e o excesso só gasta tokens.
- **Subagentes:** ele delega mais. Diga que só delegue trabalho grande, independente e paralelizável, e que não use subagentes para verificar o próprio trabalho.
- **Escopo:** em tarefas estreitas, peça para entregar o que foi pedido no escopo pedido, decidir sozinho os julgamentos de rotina e perguntar só quando leituras diferentes levariam a trabalhos muito diferentes.
- **Revisão de código:** "reporte só o que for grave" é seguido à risca e esconde achados. Peça para reportar tudo e filtre depois.
- **Autocorreção:** para limitar narrações de pequenos erros, "corrija afirmações anteriores só quando mudarem o código, a conclusão ou a decisão do usuário."
- **Thinking desativado:** prefira manter ligado e baixar o esforço.
- **Em geral (Claude 4.x e mais novos):** instruções literais, então "sugira" não edita; linguagem agressiva ("CRITICAL: você DEVE usar...") faz a ferramenta ser usada demais; prefill no último turno retorna erro nos modelos recentes (use instruções de formato).

## OpenAI (GPT)

- Instruções contraditórias custam raciocínio: ordene as regras e libere exceções explicitamente.
- Nos modelos novos, prompts menores e orientados a resultado funcionam melhor: descreva resultado, critérios de sucesso, restrições e formato; remova instruções de processo que não sejam necessárias; reserve "sempre/nunca" para segurança e campos obrigatórios; dê regras de decisão para julgamento.
- Controle a proatividade com condições de parada (quanta evidência basta), orçamento de buscas e limiares de confirmação por risco (baixo para pagar, alto para pesquisar).
- Tamanho da resposta é separado do raciocínio: use o parâmetro de verbosidade ou limites explícitos.
- Metaprompting: peça ao modelo quais frases acrescentar ou remover para obter o comportamento desejado, preservando o máximo do texto original.

## Google (Gemini)

- Seja direto; defina termos ambíguos; use um estilo único de delimitadores por prompt.
- Papel, restrições e formato no início ou na instrução de sistema; em contexto longo, material primeiro e a pergunta no final com uma frase de transição.
- Poucos exemplos, com formatação idêntica; muitos causam overfitting.
- Evite linguagem persuasiva e pedir raciocínio detalhado desnecessário; os modelos já raciocinam internamente.
- Não presuma informação ausente: forneça ou peça.
