# Groq — Documentação de Referência

Tudo que você precisa saber sobre a Groq para usar neste projeto e além.

---

## O que é a Groq

Groq é uma empresa de hardware e software de IA que desenvolveu o **LPU (Language Processing Unit)**, um chip projetado especificamente para inferência de modelos de linguagem. O resultado prático é velocidade de resposta significativamente superior à de GPUs convencionais.

Na prática para desenvolvedores: a API da Groq oferece acesso gratuito a modelos de linguagem de código aberto com latência muito baixa.

---

## Por que usar a Groq neste projeto

- **Gratuita** — tier gratuito generoso, sem cartão de crédito
- **Rápida** — respostas em menos de 1 segundo na maioria dos casos
- **Modelos potentes** — acesso ao LLaMA 3.3 70B, um dos melhores modelos open source
- **API compatível com OpenAI** — mesmo formato de requisição, fácil de integrar

---

## Como criar uma conta e obter a API key

1. Acesse [console.groq.com](https://console.groq.com)
2. Crie uma conta com Google ou GitHub
3. No menu lateral, clique em **API Keys**
4. Clique em **Create API Key**
5. Dê um nome descritivo (ex: `assistente-estudos`)
6. Copie a chave imediatamente — ela só aparece uma vez

**Formato da chave:** `gsk_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

---

## Modelos disponíveis (principais)

| Modelo | Contexto | Uso recomendado |
|---|---|---|
| `llama-3.3-70b-versatile` | 128k tokens | Tarefas complexas, raciocínio, agentes — **usado neste projeto** |
| `llama-3.1-8b-instant` | 128k tokens | Respostas rápidas, tarefas simples |
| `llama-3.2-11b-vision-preview` | 128k tokens | Análise de imagens |
| `gemma2-9b-it` | 8k tokens | Conversas gerais, instrução |
| `mixtral-8x7b-32768` | 32k tokens | Tarefas que precisam de contexto longo |

**Por que `llama-3.3-70b-versatile` neste projeto:**
É o modelo mais capaz disponível gratuitamente na Groq. O tamanho 70B garante melhor compreensão de instruções complexas, seguimento de formato estruturado e respostas mais precisas — essencial para um agente que precisa seguir um fluxo de conversa específico.

---

## Limites do tier gratuito

Os limites são por modelo e se renovam por minuto/dia:

| Limite | Valor aproximado |
|---|---|
| Requisições por minuto | 30 RPM |
| Tokens por minuto | 6.000 TPM |
| Tokens por dia | 500.000 TPD |

Para um bot de estudos individual, esses limites são mais do que suficientes. Para uso em produção com muitos usuários simultâneos, pode ser necessário o plano pago.

> Os limites exatos podem variar. Consulte [console.groq.com/settings/limits](https://console.groq.com/settings/limits) para valores atualizados.

---

## Formato da requisição (referência)

A API da Groq segue o mesmo formato da OpenAI:

```
POST https://api.groq.com/openai/v1/chat/completions

Headers:
  Authorization: Bearer gsk_SUA_CHAVE
  Content-Type: application/json

Body:
{
  "model": "llama-3.3-70b-versatile",
  "messages": [
    {
      "role": "system",
      "content": "Você é um assistente de estudos."
    },
    {
      "role": "user",
      "content": "Quero aprender Python"
    }
  ]
}
```

No n8n, isso é configurado automaticamente pelo nó **Groq Chat Model** — não é necessário fazer a requisição manualmente.

---

## Integração com o n8n

O n8n tem suporte nativo à Groq via nó **Groq Chat Model**. Para configurar:

1. Adicione o nó **Groq Chat Model** ao workflow
2. Clique no ícone de credencial → **Create new**
3. Cole a API key no campo correspondente
4. Selecione o modelo desejado
5. Conecte ao nó **AI Agent** pela porta **Chat Model**

---

## Modelos descontinuados

Ao longo do desenvolvimento, o modelo `llama3-8b-8192` foi descontinuado pela Groq.

Se encontrar o erro:
```
The model llama3-8b-8192 has been decommissioned
```

Substitua pelo modelo `llama-3.3-70b-versatile` ou consulte a lista atual em [console.groq.com/docs/models](https://console.groq.com/docs/models).

---

## Links úteis

- Console: [console.groq.com](https://console.groq.com)
- Documentação: [console.groq.com/docs](https://console.groq.com/docs)
- Lista de modelos: [console.groq.com/docs/models](https://console.groq.com/docs/models)
- Limites de uso: [console.groq.com/settings/limits](https://console.groq.com/settings/limits)
- Status da API: [groqstatus.com](https://groqstatus.com)
