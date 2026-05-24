# Agente de Estudos em Programação

Agente de IA funcional para Telegram, construído com n8n, Groq e LLaMA 3.3.

O agente recebe um tema de estudo, monta um plano personalizado com 5 tópicos, indica recursos gratuitos, gera um desafio prático e dá feedback automático sobre a solução do usuário.

Projeto desenvolvido ao vivo durante o minicurso apresentado na OpalaTech.

---

## Stack

| Ferramenta | Função | Custo |
|---|---|---|
| [n8n](https://n8n.io) | Plataforma de automação e orquestração do agente | Gratuito (cloud trial / self-hosted) |
| [Groq](https://groq.com) | API de LLM com LLaMA 3.3 70B | Gratuito |
| [Telegram Bot API](https://core.telegram.org/bots/api) | Interface de conversa com o usuário | Gratuito |

---

## Como funciona

```
Usuário envia mensagem no Telegram
        ↓
Telegram Trigger (n8n)
        ↓
AI Agent (n8n) ←→ Groq LLaMA 3.3 + Simple Memory
        ↓
Send Message (Telegram)
```

**Fluxo da conversa:**
1. Boas-vindas pelo nome do usuário
2. Pergunta sobre tema de estudo
3. Coleta nível e objetivo em uma única pergunta
4. Gera plano com 5 tópicos + estimativas de tempo + recursos gratuitos + desafio
5. Recebe resposta do desafio e dá feedback estruturado
6. Sugere próximo tema para continuar estudando

---

## Como usar

### Pré-requisitos

- Conta no [n8n Cloud](https://app.n8n.cloud) ou n8n self-hosted com URL pública
- Conta no [Groq](https://console.groq.com) para obter a API key gratuita
- Bot do Telegram criado via [@BotFather](https://t.me/BotFather)

### Importar o fluxo

1. No n8n, acesse **Workflows** → **Import from file**
2. Selecione o arquivo `fluxo/Agente_de_Estudos.json`
3. Configure as credenciais do Telegram e da Groq
4. Ative o workflow com o botão **Publish**

Veja o passo a passo completo em [docs/configuracao.md](docs/configuracao.md).

---

## Estrutura do repositório

```
agente-estudos-n8n/
├── fluxo/
│   └── Agente_de_Estudos.json     # Fluxo completo para importar no n8n
├── docs/
│   ├── configuracao.md            # Passo a passo detalhado de configuração
│   ├── como-foi-construido.md     # Processo completo de construção do agente
│   ├── system-prompt.md           # System prompt completo documentado
│   └── groq.md                    # Documentação sobre a Groq e seus modelos
└── README.md
```

---

## Licença

MIT — livre para usar, modificar e distribuir.
