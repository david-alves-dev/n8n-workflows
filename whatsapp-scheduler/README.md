# WhatsApp Scheduler

<div align="left">

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/davidalves.dev)

</div>

Este workflow agenda e envia mensagens de WhatsApp automaticamente com base em uma tabela.

A ideia é simples: você cadastra cobranças, lembretes ou avisos na base de dados com data e hora. O n8n roda a cada 1 minuto, verifica o que está pendente e dispara no horário certo.

## Como funciona

1. O nó `Schedule` dispara o fluxo a cada 1 minuto.
2. O nó `Data e Hora` + `Parâmetros iniciais` monta a data/hora atual no fuso `America/Sao_Paulo`.
3. O nó `Filtra registros` busca no NocoDB apenas registros com:
   - `Data` igual ao dia atual
   - `Hora` igual ao minuto atual
   - `Status de Envio` igual a `Pendente`
4. O nó `Loop` processa cada registro encontrado.
5. O nó `Formata texto json` ajusta quebra de linha da mensagem.
6. O nó `Envia mensagem` chama a Evolution API para enviar o texto no WhatsApp.
7. Se enviar com sucesso: `Status enviado` atualiza para `Enviado`.
8. Se der erro: `Status erro` atualiza para `Erro`.
9. O nó `Envia e-mail` envia um e-mail de notificação ao final da execução.

Se não existir mensagem para o minuto atual, o fluxo não envia nada e finaliza.

## Pré-requisitos

- n8n ativo (self-hosted ou cloud)
- Evolution API instalada e funcionando no seu servidor
- Uma base de dados para agendamento (NocoDB no fluxo original)
- Credenciais configuradas no n8n:
  - `nocoDbApiToken`
  - `gmailOAuth2` (opcional, se quiser manter notificação por e-mail)

## Base de dados (NocoDB)

O fluxo original usa NocoDB, mas pode ser adaptado para Google Sheets sem problema.

Estrutura da tabela (conforme `tabela-agendamento-exemplo.csv`):

- `Data` no formato `yyyy-MM-dd` (ex.: `2026-03-04`)
- `Hora` no formato `HH:mm` (ex.: `14:30`)
- `Contato / Grupo` (número do WhatsApp ou ID do grupo)
- `Tipo da Mensagem` (ex.: `Texto`)
- `Mensagem` (texto que será enviado)
- `Status de Envio` (`Pendente`, `Enviado`, `Erro`)

Observação:

- No JSON atual, o destino da mensagem está vindo de um relacionamento do NocoDB (`_nc_m2m_Agendamento_Contatos[0].Contatos['WhatsApp / ID Grupo']`).
- Se você usar a coluna direta `Contato / Grupo` do CSV, ajuste o nó `Envia mensagem` para ler esse campo em vez do relacionamento.

## Configuração rápida

No nó `Parâmetros iniciais`, ajuste:

- `evo_url` = URL base da sua Evolution API
- `evo_instancia` = nome da instância
- `evo_apikey` = API Key da Evolution API

No nó `Filtra registros`, confira:

- `projectId`
- `table`
- campo de status (`Status de Envio`)

No nó `Envia e-mail` (opcional):

- `sendTo`
- `LINK_DA_SUA_TABELA`

## Capturar ID de grupos (fluxo auxiliar)

Este workflow também tem um trecho manual para ajudar a descobrir grupos:

- `Manual` -> `Listar grupos` -> `Filtra grupo`

Você pode usar esse caminho para listar grupos da API e filtrar pelo nome (`subject`), facilitando preencher o campo `WhatsApp / ID Grupo` na base.

Com esse ID em mãos, também é possível agendar mensagens para grupos de WhatsApp (não apenas para contatos individuais).

## Status usados

- `Pendente`: aguardando horário
- `Enviado`: disparo concluído com sucesso
- `Erro`: tentativa falhou

## Adaptação para Google Sheets

Se quiser usar Google Planilhas:

- Troque os nós NocoDB (`Filtra registros`, `Status enviado`, `Status erro`) por nós de Google Sheets equivalentes.
- Mantenha a mesma lógica de filtros por data/hora/status.
- Mantenha a atualização de status após envio.

## Observações importantes

- O agendamento é por minuto. A `Hora` deve bater exatamente com o minuto atual.
- O fuso usado no fluxo é `America/Sao_Paulo`.
- Se quiser evitar e-mail a cada execução, remova ou ajuste o nó `Envia e-mail`.
- O workflow está com `active: false` no JSON exportado. Depois de importar, ative no n8n.
