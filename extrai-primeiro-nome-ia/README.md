# 🤖 Extrai Primeiro Nome (IA)

<div align="left">

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)
[![Acessar Site](https://img.shields.io/badge/Acessar%20Site-00A846?style=for-the-badge&logo=robotframework&logoColor=white)](https://davidalves.dev/)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/davidalves.dev)

</div>

Este projeto foi criado para ser usado com **Evolution API** ou **Typebot**.

## ⚙️ O que ele faz

O fluxo recebe a mensagem de resposta do lead e identifica somente o **primeiro nome**, retornando apenas esse valor tratado.

Ele resolve cenários em que o lead:

- envia o nome completo
- envia só o primeiro nome
- envia saudação junto com o nome na mesma mensagem

Exemplos de entrada:

- `Oi, sou João da Silva`
- `Meu nome é Maria Fernanda`
- `Carlos`

Exemplos de saída:

- `João`
- `Maria`
- `Carlos`

## 🎯 Para que usar

Com esse retorno padronizado, você pode:

- personalizar mensagens em fluxos do Typebot
- tratar dados de formulários com mais consistência
- salvar apenas o primeiro nome do lead para uso em CRM, automações e follow-ups
