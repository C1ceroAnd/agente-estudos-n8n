# Guia de Configuração

Passo a passo completo para colocar o agente em funcionamento do zero.

---

## 1. Criar o bot no Telegram

1. Abra o Telegram e pesquise por **@BotFather**
2. Clique em **Start**
3. Digite `/newbot`
4. Informe o nome de exibição do bot (ex: `Assistente de Estudos`)
5. Informe o username (deve terminar em `bot`, ex: `assistenteestudosbot`)
6. Guarde o token retornado — formato: `1234567890:AAFxxxxxxxxxxxxxxxx`

**Configurações recomendadas no BotFather:**

Após criar o bot, acesse **Edit Bot** para configurar:

- **Edit Name:** nome que aparece no perfil
- **Edit About:** texto curto (máx 120 caracteres) que aparece no perfil
- **Edit Description:** texto que aparece antes do usuário clicar em Start
- **Edit Commands:** lista de comandos visíveis no menu

Comandos sugeridos:
```
start - Iniciar o assistente
```

---

## 2. Obter a API key da Groq

1. Acesse [console.groq.com](https://console.groq.com)
2. Crie uma conta (Google ou GitHub)
3. No menu lateral, clique em **API Keys**
4. Clique em **Create API Key**
5. Nomeie como `assistente-estudos`
6. Copie a chave — ela aparece uma única vez

---

## 3. Configurar credenciais no n8n

### Credencial do Telegram

1. No n8n, acesse **Credentials** → **Add Credential**
2. Pesquise por `Telegram`
3. No campo **Access Token**, cole o token do BotFather
4. Salve com o nome: `Telegram - Assistente de Estudos`

### Credencial da Groq

1. No n8n, acesse **Credentials** → **Add Credential**
2. Pesquise por `Groq`
3. No campo **API Key**, cole a chave da Groq
4. Salve com o nome: `Groq - Assistente de Estudos`

---

## 4. Importar o fluxo

1. No n8n, acesse **Workflows** → menu `...` → **Import from file**
2. Selecione o arquivo `fluxo/Agente_de_Estudos.json`
3. O fluxo será importado com todos os nós configurados
4. Vincule as credenciais criadas nos nós **Telegram Trigger**, **Send a text message** e **Groq Chat Model**
5. Clique em **Publish** para ativar

---

## 5. Testar o agente

1. Abra o Telegram e pesquise pelo username do seu bot
2. Digite `/start`
3. O agente deve responder com boas-vindas e perguntar o tema

**Roteiro de teste completo:**

```
1. /start
2. Quero aprender JavaScript
3. Sou iniciante. Quero usar para um projeto pessoal
4. (aguardar o plano completo)
5. Me explica melhor o tópico 1
6. Ficou claro, vou tentar o desafio
7. (enviar uma resposta ao desafio)
8. Quero continuar
```

---

## Problemas comuns

**Bot não responde após ativar o workflow**
- Verifique se o workflow está publicado (botão Publish ativo)
- Confirme que a credencial do Telegram está corretamente vinculada ao nó Telegram Trigger

**Erro "Bad request" ao enviar mensagem**
- Verifique se o Parse Mode está configurado como HTML no nó Send a text message

**Modelo Groq retorna erro**
- Confirme que o modelo `llama-3.3-70b-versatile` está selecionado no nó Groq Chat Model
- Verifique se a API key da Groq é válida
