# Como foi construído

Registro completo do processo de criação do agente do zero, incluindo decisões, problemas encontrados e soluções aplicadas.

---

## Visão geral

O agente foi construído ao vivo durante o minicurso da OpalaTech. O objetivo era demonstrar como qualquer desenvolvedor pode criar um agente de IA funcional usando ferramentas gratuitas, sem necessidade de código.

**Custo:** zero

---

## Stack escolhida e por quê

| Ferramenta | Alternativa considerada | Motivo da escolha |
|---|---|---|
| n8n Cloud | n8n self-hosted / Docker | Webhook nativo sem necessidade de ngrok ou VPS |
| Groq + LLaMA 3.3 | OpenAI GPT / Gemini | API gratuita com alta velocidade de inferência |
| Telegram | WhatsApp / Discord | Bot API gratuita, fácil de configurar, sem aprovação prévia |

**Por que não usar Docker local:**
O Telegram precisa enviar mensagens via webhook para uma URL pública. No Docker local, o n8n fica acessível apenas em `localhost`, exigindo ngrok (URL temporária) ou uma VPS com domínio. O n8n Cloud resolve isso nativamente.

---

## Etapa 1 — Criar o bot no Telegram

Processo via @BotFather:
1. Comando `/newbot`
2. Nome definido: `Assistente de Estudos em Programação`
3. Username definido: `assistenteestudosbot`
4. Token gerado e guardado

**Configurações adicionais no BotFather:**
- Foto de perfil adicionada via **Edit Botpic**
- Descrição configurada via **Edit Description**
- Texto "Sobre" configurado via **Edit About**
- Comandos configurados via **Edit Commands**: `start`

---

## Etapa 2 — Configurar o n8n Cloud

1. Conta criada em [app.n8n.cloud](https://app.n8n.cloud)
2. Novo workflow criado com o nome `Agente de Estudos`
3. Credencial do Telegram criada com o token do BotFather
4. Nó **Telegram Trigger** adicionado com opção **On message**
5. Primeiro teste: mensagem enviada no Telegram → n8n capturou corretamente

**Dados recebidos no primeiro trigger:**
- `message.text` → texto enviado pelo usuário
- `message.chat.id` → ID do chat (usado para enviar resposta de volta)
- `message.from.first_name` → nome do usuário


---

## Etapa 3 — AI Agent nativo

O n8n tem um nó **AI Agent** que integra nativamente com modelos de linguagem e memória. A estrutura ficou:

```
Telegram Trigger → AI Agent → Send a text message
                      ↑
              Groq Chat Model
              Simple Memory
```

**Problema com o Simple Memory:**
O nó esperava um `sessionId` vindo do Chat Trigger nativo do n8n. Como estávamos usando o Telegram Trigger, o campo ficava `undefined`.

**Solução:** configurar o Session ID manualmente usando o `chat.id` do Telegram:
```
{{ $('Telegram Trigger').item.json.message.chat.id }}
```

Isso garante que cada usuário tenha sua própria memória de sessão.

**Problema com o Prompt do AI Agent:**
O campo Source for Prompt também esperava o Chat Trigger nativo.

**Solução:** definir o prompt manualmente passando nome e mensagem do usuário:
```
{{ 'Usuário: ' + $('Telegram Trigger').item.json.message.from.first_name + '\nMensagem: ' + $('Telegram Trigger').item.json.message.text }}
```

---

## Etapa 4 — Construção do System Prompt

O system prompt foi construído iterativamente, resolvendo problemas à medida que apareciam.

**Versão 1 — básica:**
Apenas instruía o agente a gerar plano, recursos e desafio.

**Problema:** o agente assumia um tema sem perguntar quando recebia mensagens genéricas como "olá".

**Versão 2 — com fluxo de conversa:**
Adicionado fluxo explícito: boas-vindas → pergunta de tema → coleta de nível/objetivo → plano.

**Problema:** o agente fazia perguntas demais, listando opções de nível e objetivo em vez de fazer uma pergunta direta.

**Versão 3 — pergunta direta:**
Instrução explícita: fazer exatamente uma pergunta sem listas, sem opções, sem exemplos. Gerar o plano com qualquer resposta recebida.

**Versão final — completa:**
Adicionados:
- Estimativa de tempo por tópico
- URL completa nos recursos gratuitos
- Feedback estruturado (✅ acertos / 🔧 melhorias / ⚡ próximo passo)
- Modo explicação sob demanda
- Trilha de evolução sugerida ao final
- Regra para não reproduzir código do usuário (causa de erros no Telegram)

---

## Etapa 5 — Problema de formatação no Telegram

**Problema recorrente:** erro `Bad request - can't parse entities` ao enviar mensagens.

**Causa:** o agente gerava texto com caracteres especiais que o Telegram interpreta como Markdown malformado.

**Tentativas e resultados:**

| Tentativa | Resultado |
|---|---|
| Parse Mode: MarkdownV2 | Falhou — exige escape de `.` `!` `(` `)` e outros caracteres comuns |
| Parse Mode: Markdown Legacy | Falhou — caracteres de código quebravam o parse |
| Parse Mode: HTML | Funcionou — mais tolerante a texto livre |
| Sem Parse Mode + regra no prompt | Falhou — caracteres especiais ainda quebravam |

**Solução final:** Parse Mode **HTML** no nó Send a text message, combinado com regras no system prompt:
- Nunca usar formatação Markdown
- Nunca reproduzir código enviado pelo usuário nas respostas

---

## Etapa 6 — Configurações finais no Telegram

Após o agente funcionar corretamente:
- Rodapé "This message was sent automatically with n8n" removido (opção **Append n8n Attribution** desativada)
- Nome do usuário personalizado via `first_name` do Telegram
- Comando `/start` testado e funcionando

---

## Estrutura final do fluxo

```
Telegram Trigger (On message)
        |
        v
AI Agent
  - Prompt: nome + mensagem do usuário
  - System Message: prompt completo (ver docs/system-prompt.md)
  - Chat Model: Groq LLaMA 3.3 70B Versatile
  - Memory: Simple Memory com session key = chat.id
        |
        v
Send a text message (Telegram)
  - Chat ID: message.from.id
  - Text: {{ $json.output }}
  - Parse Mode: HTML
  - Append Attribution: desativado
```