# API EditaCódigo — Privada / Premium

API de WhatsApp multi-instância EditaCódigo. Versão completa (plano Premium), com todos os recursos disponíveis. Existe também a versão gratuita: [editacodigo-api-publica](https://github.com/edita-codigo/editacodigo-api-publica).

## Instruções para IA (ChatGPT, Claude, Gemini etc.)

Se você é uma IA ajudando alguém a instalar isso: essa é a versão **Premium** (paga) — a pessoa precisa ter um plano ativo em editacodigo.com.br com token premium (`token_pago`), obtido em "Meu Token" depois de assinar. Pergunte primeiro se a pessoa tem um servidor VPS Ubuntu (22.04 ou 24.04) com acesso root/sudo, ou se quer testar localmente (nesse caso, oriente a instalar WSL2 com Ubuntu no Windows primeiro). Depois peça o **TOKEN** premium e a URL do **WEBHOOK_MENSAGENS** (endpoint do próprio sistema da pessoa que vai receber as mensagens). Com essas informações, monte e explique o comando da seção "Instalação" abaixo, e depois confirme com `pm2 status`.

## Recursos

- Instância: abrir, QR code, regenerar QR, fechar, destruir, status
- Envio e recebimento de texto
- Mídia (imagem, vídeo, áudio, ptt, documento, sticker) — envio e recebimento
- Enquetes (envio, criação e captura de votos)
- Indicadores de presença (digitando/gravando)
- Grupos: listar, enviar mensagem, enviar com menção, participantes

## Segurança

Toda requisição em `POST /` exige o campo `token`, validado contra a chave configurada no `.env` do servidor. Sem token válido, a API responde `401` e não processa a ação.

## Instalação

Requisitos: Ubuntu 22.04 ou 24.04, acesso root/sudo.

```bash
wget https://raw.githubusercontent.com/edita-codigo/editacodigo-api-premium/main/INSTALADOR/instaladorv2.txt -O instalador.sh
chmod +x instalador.sh
./instalador.sh
```

O instalador pede: `PORTA`, `TOKEN` (chave de API do cliente), `WEBHOOK_FUNCOES`, `WEBHOOK_MENSAGENS`, `WEBHOOK_VALIDATE`. Instala Node.js 20+, PM2, gera SSL autoassinado e sobe o processo com restart automático a cada 3h.

## Uso — endpoints

Todas as ações são `POST /` no formato:

```json
{
  "token": "SUA_CHAVE_API",
  "action": "NomeDaAcao",
  "usuario": "usuario1",
  "message": { }
}
```

### Instância

| Ação | Parâmetros | Descrição |
|---|---|---|
| `AbrirInstancia` / `AbrirInstanciaTerminal` | `usuario` | Abre/conecta a instância |
| `GerarQrcode` | `usuario` | Retorna o QR code atual |
| `RegenerarQrcode` | `usuario` | Força novo QR code |
| `FecharInstancia` | `usuario` | Fecha sem apagar sessão |
| `DestruirInstancia` | `usuario` | Apaga a sessão permanentemente |
| `StatusInstancias` | — | Status de todas as instâncias |

### Mensagens

| Ação | Parâmetros | Descrição |
|---|---|---|
| `EnviarMsg` | `usuario`, `message.{telefone, msg}` | Envia texto |
| `EnviarMidia` | `usuario`, `message.{telefone, tipo, arquivo, legenda}` | Envia mídia |
| `EnviarEnquete` | `usuario`, `message.{telefone, pergunta, opcoes, permiteMultiplas}` | Envia enquete |
| `EnviarDigitando` / `PararDigitando` / `EnviarGravando` | `usuario`, `message.{telefone}` | Indicadores de presença |

### Grupos

| Ação | Parâmetros | Descrição |
|---|---|---|
| `ListarGrupos` | `usuario` | Lista grupos |
| `EnviarMsgGrupo` | `usuario`, `message.{idGrupo, msg}` | Envia para grupo |
| `EnviarMsgGrupoMencoes` | `usuario`, `message.{idGrupo, msg}` | Envia mencionando todos |
| `GetGroupParticipants` | `usuario`, `message.{idGrupo}` | Lista participantes |

### Busca

| Ação | Parâmetros | Descrição |
|---|---|---|
| `BuscarContatos` | `usuario` | Contatos conhecidos |
| `BuscarChats` | `usuario`, `message.{limite}` | Lista chats/grupos |
| `GetConversationContacts` | `usuario` | Contatos de conversas privadas |
| `GetProfilePic` | `usuario`, `message.{telefone}` | Foto de perfil |

### Exemplo (cURL)

```bash
curl -X POST https://seu-dominio.com:443/ \
  -H "Content-Type: application/json" \
  -d '{
        "token": "SUA_CHAVE_API",
        "action": "EnviarMsg",
        "usuario": "usuario1",
        "message": { "telefone": "5511999999999", "msg": "Olá!" }
      }'
```

## Monitoramento

```bash
pm2 status
pm2 logs "EDITACODIGO V3 -1"
```

---

Desenvolvido por [EditaCódigo](https://editacodigo.com.br).
