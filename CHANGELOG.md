# Changelog

## 3.0.0
- A entrevista passa a ter 6 perguntas por rodada, feitas em duas chamadas seguidas (4 + 2), porque o AskUserQuestion aceita no máximo 4 por chamada.
- O prompt final nunca sai com placeholders (`<campo>`, `[preencher]`). Quando o usuário não sabe uma resposta, o prompt manda o modelo tratar a ausência.

## 2.0.0
- Recomendação em cada pergunta de decisão, com o motivo tirado do próprio prompt.
- Pré-mortem: a skill imagina como o prompt poderia dar errado e fecha essas falhas.
- Modo `depurar`: corrige um prompt a partir de uma resposta ruim.
- Modos `rápido` e `profundo`.

## 1.0.0
- Primeira versão: auditoria do prompt, entrevista dirigida e prompt melhorado com resumo do que mudou e por quê.
