<div align="center">

# ZapHook MCP Server

**Conecte o Claude, o Cursor e outros assistentes de IA ao WhatsApp Business pelo [ZapHook](https://zaphook.cloud).**
Leia conversas, envie mensagens e templates, gerencie canais e webhooks e investigue entregas, pedindo em português.

[![Licença: MIT](https://img.shields.io/badge/licen%C3%A7a-MIT-blue.svg)](LICENSE)
[![MCP: Streamable HTTP](https://img.shields.io/badge/MCP-Streamable%20HTTP-6f42c1)](https://modelcontextprotocol.io)
[![Hospedado](https://img.shields.io/badge/servidor-hospedado-25D366)](#início-rápido)

[English](README.md) · **Português**

</div>

---

## O que é

O ZapHook MCP Server é um servidor [Model Context Protocol](https://modelcontextprotocol.io) **hospedado**. Você adiciona uma URL e a sua API key do ZapHook no seu cliente de IA, e o assistente passa a trabalhar com a sua conta de WhatsApp Business:

- "Quais conversas estão esperando resposta hoje?"
- "Leia a última conversa com o +55 11 99999-8888 e sugira uma resposta."
- "Envie o nosso template aprovado `boas_vindas` para este cliente."
- "Por que as mensagens não chegaram ao meu webhook ontem?"

Não há nada para instalar ou rodar. O servidor funciona com canais **standard** e de **coexistência** (app WhatsApp Business + API).

Este repositório reúne a documentação, os exemplos de configuração e o manifesto de registro. O servidor roda na infraestrutura do ZapHook.

## Início rápido

| | |
|---|---|
| **URL do servidor** | `https://api.zaphook.cloud/mcp` |
| **Transporte** | Streamable HTTP |
| **Autenticação** | Cabeçalho `Authorization: Bearer SUA_API_KEY` (ou `X-API-Key: SUA_API_KEY`) |

1. **Crie sua conta** em [zaphook.cloud](https://zaphook.cloud/login) e conecte pelo menos um canal de WhatsApp.
2. **Crie uma API key** no painel: *Configurações → Chaves de API → Criar chave da API* ([passo a passo com imagens](docs/authentication.md)).
3. **Adicione o servidor no seu cliente** com uma das configurações abaixo.

### Claude (claude.ai e Claude Desktop)

1. Abra **Configurações → Conectores → Adicionar conector personalizado**.
2. **Nome:** `ZapHook` · **URL do servidor MCP:** `https://api.zaphook.cloud/mcp`
3. **Autenticação:** escolha **Sem login** (a key vai num cabeçalho, não por OAuth).
4. **Cabeçalhos de requisição → Adicionar cabeçalho:** nome `Authorization`, valor `Bearer SUA_API_KEY`.
5. Salve, abra uma conversa nova e peça: *"Liste meus canais do ZapHook."*

Detalhes: [examples/claude-ai.md](examples/claude-ai.md).

### Claude Code

```bash
claude mcp add --transport http zaphook https://api.zaphook.cloud/mcp \
  --scope user --header "Authorization: Bearer SUA_API_KEY"
```

PowerShell no Windows e outros detalhes: [examples/claude-code.md](examples/claude-code.md).

### Cursor

`~/.cursor/mcp.json` (ou `.cursor/mcp.json` no projeto):

```json
{
  "mcpServers": {
    "zaphook": {
      "url": "https://api.zaphook.cloud/mcp",
      "headers": { "Authorization": "Bearer SUA_API_KEY" }
    }
  }
}
```

### Outros clientes

| Cliente | Exemplo |
|---|---|
| VS Code (GitHub Copilot) | [examples/vscode.json](examples/vscode.json) |
| Kiro | [examples/kiro.json](examples/kiro.json) |
| Clientes que só aceitam stdio (pela ponte `mcp-remote`) | [examples/mcp-remote.json](examples/mcp-remote.json) |

> **Nunca faça commit de uma API key real.** Guarde a key no cofre de segredos do cliente ou numa variável de ambiente. Se ela vazar, revogue no painel e crie outra.

## Ferramentas

São 31 ferramentas, agrupadas por tema. **📤 envia uma mensagem real de WhatsApp** (não tem como desfazer). **🗑️ apaga dados de forma permanente.** Referência completa com parâmetros: [docs/tools.md](docs/tools.md) (em inglês).

### Inbox (somente leitura)

| Ferramenta | O que faz |
|---|---|
| `zaphook_list_conversations` | Lista as conversas, mais recentes primeiro, com contato, mensagens não lidas, prévia curta da última mensagem e se a janela de 24 h está aberta. Filtros: canal, status, não lidas, busca por telefone ou nome, datas. |
| `zaphook_get_conversation` | Uma conversa, incluindo a situação da janela de 24 h. |
| `zaphook_list_conversation_messages` | Histórico completo da conversa (recebidas e enviadas), paginado. |

### Mensagens

| Ferramenta | O que faz |
|---|---|
| `zaphook_send_text` 📤 | Mensagem de texto. |
| `zaphook_send_template` 📤 | Template aprovado. Obrigatório para iniciar conversa ou escrever fora da janela de 24 h. |
| `zaphook_send_image` 📤 | Imagem (link HTTPS público ou ID de mídia). |
| `zaphook_send_video` 📤 | Vídeo. |
| `zaphook_send_audio` 📤 | Áudio. |
| `zaphook_send_document` 📤 | Documento (PDF, planilha etc.). |
| `zaphook_send_sticker` 📤 | Figurinha (.webp). |
| `zaphook_send_location` 📤 | Localização. |
| `zaphook_request_location` 📤 | Pede ao contato que compartilhe a localização. |
| `zaphook_send_contact` 📤 | Cartão(ões) de contato. |
| `zaphook_send_buttons` 📤 | Até 3 botões de resposta rápida. |
| `zaphook_send_list` 📤 | Menu interativo em lista. |
| `zaphook_send_reaction` 📤 | Reação com emoji a uma mensagem. |
| `zaphook_mark_as_read` | Marca uma mensagem recebida como lida (vistos azuis). Não envia conteúdo. |
| `zaphook_send_typing` | Mostra "digitando…" ao contato por cerca de 25 segundos e marca a mensagem como lida. Não envia conteúdo. |

### Canais e templates

| Ferramenta | O que faz |
|---|---|
| `zaphook_list_channels` | Lista seus canais de WhatsApp (standard e coexistência). Comece por aqui. |
| `zaphook_get_channel` | Um canal pelo ID. |
| `zaphook_get_channel_health` | Saúde do número em tempo real na Meta: conexão, qualidade, nível de limite de mensagens, nome verificado. |
| `zaphook_list_channel_templates` | Templates com status de aprovação, motivo de recusa e variáveis. |
| `zaphook_create_channel` | Cria um canal standard com URL de webhook gerada automaticamente. |
| `zaphook_update_channel` | Renomeia, pausa/ativa ou troca o segredo do webhook de um canal standard. |
| `zaphook_delete_channel` 🗑️ | Apaga de forma permanente um canal standard, com destinos e logs de eventos. |

### Destinos (webhooks)

| Ferramenta | O que faz |
|---|---|
| `zaphook_list_destinations` | URLs que recebem os eventos de um canal. Valores de cabeçalhos e autenticação vêm mascarados. |
| `zaphook_create_destination` | Adiciona um destino a um canal standard. |
| `zaphook_update_destination` | Atualiza, pausa ou ativa um destino. |
| `zaphook_delete_destination` 🗑️ | Remove um destino. |

### Eventos (investigar entregas)

| Ferramenta | O que faz |
|---|---|
| `zaphook_list_events` | Eventos recebidos e entregues, com filtro por canal, tipo, status de entrega, erros e datas. |
| `zaphook_get_event` | Um evento em detalhe: payload original e processado, resposta do destino, tentativas e erros. |

## Exemplos de pedidos

- "Quais conversas do canal Suporte estão não lidas? Resuma o que cada cliente quer."
- "A janela de 24 h com esse contato ainda está aberta? Se não, qual template eu uso?"
- "Responda à Maria: *Seu pedido foi enviado e chega amanhã.*"
- "Mostre os eventos que falharam nas últimas 24 horas e explique os erros."
- "Verifique a saúde de todos os meus números."

Mais exemplos: [examples/prompts.md](examples/prompts.md).

## Segurança desde o projeto

- **Confirmação antes de enviar.** Toda ferramenta de envio orienta o assistente a mostrar o destinatário e o conteúdo exato e esperar a sua confirmação. A maioria dos clientes também pergunta antes de rodar uma ferramenta. Revise cada confirmação: mensagem enviada não volta.
- **Sem repetição automática de envio.** Se um envio der timeout, o assistente é orientado a conferir os eventos antes de tentar de novo, para não mandar a mesma mensagem duas vezes.
- **Conteúdo recebido é dado, não instrução.** Mensagens de contatos são marcadas como conteúdo de terceiros, para o assistente nunca seguir instruções escritas dentro de uma mensagem de cliente.
- **Segredos mascarados.** Segredos de webhook, tokens e credenciais de destinos voltam como `***`. O servidor nunca pede o seu token de acesso da Meta.
- **Sua key, sua conta.** Cada requisição usa só a API key enviada nela. Nada é compartilhado entre usuários.
- **Somente HTTPS**, com limite de requisições por key.

## Regras do WhatsApp que valem saber

| Situação | O que fazer |
|---|---|
| O contato não escreveu nas últimas 24 horas | A Meta só permite mensagem livre dentro da janela de atendimento de 24 horas. Use `zaphook_send_template` com um template aprovado. |
| Enviar mídia | Use um link HTTPS público que abra direto no arquivo, ou um ID de mídia. Arquivos do seu computador não são enviados direto; veja [Troubleshooting](docs/troubleshooting.md#sending-media). |
| Formato do telefone | DDI + número, só dígitos. Exemplo: `5511999998888`. |
| Um envio deu timeout | Confira `zaphook_list_events` antes de repetir. |
| Template não encontrado | Liste os templates e use o `name` e o `language` exatos. |

Você é responsável por seguir a [Política de Mensagens do WhatsApp Business](https://business.whatsapp.com/policy), incluindo o opt-in dos contatos antes de enviar mensagens. Mais respostas: [docs/troubleshooting.md](docs/troubleshooting.md).

## Pré-requisitos

- Conta no [ZapHook](https://zaphook.cloud) com pelo menos um canal de WhatsApp.
- Uma API key do ZapHook.
- Um cliente MCP com suporte a Streamable HTTP e cabeçalhos personalizados, ou a ponte `mcp-remote`.

## Relacionados

- [ZapHook](https://zaphook.cloud): site e painel.
- [Documentação do ZapHook](https://zaphook.gitbook.io/docs): API REST pública.
- [ZapHook Agent Skills](https://github.com/zaphook/zaphook-agent-skills): os mesmos recursos como skills para Claude Code, Kiro, Gemini CLI e Codex CLI.

## Suporte e contribuição

- Bugs e sugestões: [GitHub Issues](https://github.com/zaphook/zaphook-mcp-server/issues).
- Falhas de segurança: veja o [SECURITY.md](SECURITY.md). Não abra issue pública para vulnerabilidades.
- Melhorias na documentação e nos exemplos são bem-vindas: [CONTRIBUTING.md](CONTRIBUTING.md).

## Licença

A documentação e os exemplos deste repositório estão sob a [Licença MIT](LICENSE).

O ZapHook é um serviço independente e não é afiliado, endossado nem patrocinado pela Meta Platforms, Inc. ou pelo WhatsApp. WhatsApp é marca registrada da Meta Platforms, Inc.
