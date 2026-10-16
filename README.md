# interview-me

Uma skill para Claude que **entrevista você a partir de um prompt rascunho** e devolve um prompt preciso, pronto para rodar, seguindo as boas práticas de prompting da Anthropic, OpenAI e Google.

Diferente de um reescritor de prompts, ela não responde às cegas: audita o prompt, imagina como ele poderia dar errado e faz só as perguntas que aquele prompt realmente precisa.

## O que ela faz

1. **Audita** o prompt contra um checklist de boas práticas (resultado e critério de pronto, contexto, escopo, formato, autonomia, fontes, contradições, exemplos e mais).
2. **Faz um pré-mortem**: imagina 2 ou 3 jeitos concretos de o modelo errar com o prompt do jeito que está escrito.
3. **Entrevista** com 6 perguntas, em duas chamadas seguidas. Em cada decisão, a primeira opção é a recomendação da skill, com o motivo. Em perguntas de fato (nomes, datas, arquivos) ela não recomenda nada, para não inventar dados.
4. **Devolve** o prompt melhorado em um bloco de código, sem marcadores a preencher, junto com o modelo e esforço sugeridos, o risco que o prompt corria e o que mudou e por quê.

## Modos

| Uso | O que acontece |
| --- | --- |
| `/interview-me <prompt>` | Padrão: 6 perguntas e, se preciso, uma segunda rodada curta |
| `/interview-me rápido <prompt>` | Uma rodada de até 3 perguntas; as dúvidas viram a opção recomendada |
| `/interview-me profundo <prompt>` | 6 perguntas mais até 2 rodadas de acompanhamento nas respostas vagas |
| `/interview-me depurar` | Você cola o prompt e a resposta ruim; ela acha o trecho que causou o problema e faz a correção mínima |

## Exemplo

Entrada:

```text
/interview-me quero analisar a empresa Hapvida
```

Saída, depois das perguntas (trecho):

```text
Faça uma análise fundamentalista da Hapvida, no estilo de um relatório de equity research, para um trabalho de faculdade, com cerca de 3.000 palavras.
Estruture em etapas, na ordem do método: negócio e posição no setor, resultados e indicadores operacionais, balanço e endividamento, comparação com concorrentes, riscos e valuation por DCF e por múltiplos de pares. Termine cada etapa com a conclusão dela em uma ou duas frases.
...
Pronto quando: todas as etapas estiverem completas, cada número tiver fonte e data, e a conclusão final resumir pontos fortes, pontos fracos e o valuation.
```

## Instalação

### Claude Code, como plugin (recomendado)

```bash
/plugin marketplace add natanmendesa/interview-me
/plugin install interview-me@interview-me
```

### Claude Code, manualmente

```bash
git clone https://github.com/natanmendesa/interview-me.git
mkdir -p ~/.claude/skills
cp -r interview-me/skills/interview-me ~/.claude/skills/
```

Para usar só em um projeto, copie a pasta para `.claude/skills/` dentro dele.

### Claude.ai e app do Claude

1. Baixe o arquivo `interview-me.skill` na [página de Releases](../../releases).
2. Abra o arquivo no Claude e use o botão **Save skill**, ou envie o `.skill` em Configurações, na área de skills.

## Estrutura

```text
interview-me/
├── .claude-plugin/
│   ├── plugin.json         # manifesto do plugin
│   └── marketplace.json    # permite instalar pelo /plugin marketplace
├── .github/workflows/
│   └── release.yml         # gera o interview-me.skill a cada tag v*
├── skills/interview-me/
│   ├── SKILL.md            # fluxo, modos e regras
│   └── references/
│       ├── checklist.md    # checklist da auditoria
│       ├── modelos.md      # ajustes por modelo (Opus 5.5, Opus 5, GPT, Gemini)
│       ├── depurar.md      # modo depurar
│       └── exemplos.md     # exemplos de entrevista e de saída
├── CHANGELOG.md
├── LICENSE
└── README.md
```

O `SKILL.md` fica enxuto e os detalhes ficam em `references/`, que a skill só lê quando precisa. Assim ela gasta poucos tokens.

## Publicando uma nova versão

1. Altere os arquivos em `skills/interview-me/` e atualize o `CHANGELOG.md`.
2. Atualize `version` em `.claude-plugin/plugin.json`.
3. Crie e envie uma tag:

```bash
git tag v3.0.1
git push origin v3.0.1
```

O workflow gera o `interview-me.skill` e o anexa à release.

## Fontes das boas práticas

- [Prompting best practices (Claude)](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5)
- [Prompting Claude Opus 5.5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5-5)
- [Effective context engineering for AI agents](https://anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [GPT-5 prompting guide (OpenAI)](https://developers.openai.com/cookbook/examples/gpt-5/gpt-5_prompting_guide)
- [Prompting strategies (Gemini API)](https://ai.google.dev/gemini-api/docs/prompting-strategies)

As recomendações foram medidas pelos autores em modelos específicos. Teste no seu caso antes de assumir que valem para outro modelo.

## Licença

MIT. Veja [LICENSE](LICENSE).
