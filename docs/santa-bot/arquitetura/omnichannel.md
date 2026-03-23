# Documentação Técnica — Gateway de Mensagens (omnichannel-service)

📅 **Última atualização:** 23/03/2026

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Arquitetura do Sistema](#arquitetura-do-sistema)
3. [Módulos e Responsabilidades](#modulos)
4. [Estrutura de Pastas](#estrutura-de-pastas)
5. [Multi-Tenancy](#multi-tenancy)
6. [Canais de Mensagem Suportados](#canais)
7. [Endpoints da API](#endpoints)
8. [Segurança](#seguranca)
9. [Configuração e Execução](#configuracao)
10. [Tecnologias Utilizadas](#tecnologias)

---

## 1. Visão Geral {#visao-geral}
O **omnichannel-service** é o gateway centralizado de envio de mensagens do SantaBot. Ele abstrai os diferentes canais de comunicação (WhatsApp via Meta API, ChatGuru, Telegram e E-mail) em uma única interface, com suporte a **multi-tenancy**: cada tenant (estabelecimento) possui suas próprias credenciais de canal, isoladas e criptografadas.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS
- **Banco de Dados:** PostgreSQL (tenants, credenciais, logs)
- **Cache:** Redis (credenciais descryptografadas em cache)
- **Autenticação:** JWT (Bearer Token) para chamadas internas
- **Multi-Tenancy:** resolvido via middleware (`TenantResolverMiddleware`) a partir do header `x-tenant-id`
- **Documentação de API:** Swagger

---

## 3. Módulos e Responsabilidades {#modulos}

| Módulo               | Responsabilidade                                                                    |
|----------------------|-------------------------------------------------------------------------------------|
| `auth`               | Autenticação JWT (pública e interna).                                               |
| `tenants`            | Gestão de tenants (estabelecimentos que usam o serviço).                            |
| `tenant_credentials` | Armazenamento das credenciais de canal por tenant (criptografadas).                 |
| `suppliers`          | Cadastro de fornecedores/provedores de mensagens.                                   |
| `wpp`                | Envio de mensagens, templates e mídias via **WhatsApp Business API (Meta)**.        |
| `chatguru`           | Envio de mensagens e execução de diálogos via **ChatGuru**.                         |
| `telegram`           | Envio de mensagens via **Telegram Bot API**.                                        |
| `mail`               | Envio de e-mails.                                                                   |
| `webhook`            | Recebimento e processamento de eventos de retorno (ex.: leitura, resposta do WPP). |
| `redis`              | Cache de tokens e credenciais para reduzir descriptografias repetidas.              |

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```plaintext
src/
├── app.module.ts
├── app.controller.ts
├── app.service.ts
├── main.ts
├── auth/
│   ├── auth.module.ts
│   ├── auth.service.ts
│   ├── dto/
│   ├── guards/
│   │   ├── internal-auth.guard.ts
│   │   └── jwt-auth.guard.ts
│   ├── internal/                   # Autenticação para chamadas internas
│   ├── public/                     # Autenticação pública (login)
│   └── strategies/
├── decorators/
│   └── tenant.decorator.ts
├── helper/
│   ├── crypto.helper.ts            # AES-256-GCM encrypt/decrypt
│   └── vault.loader.ts
├── middleware/
│   └── tenant-solver.middleware.ts # Resolve credenciais do tenant por x-tenant-id
├── migrations/
├── modules/
│   ├── chatguru/
│   │   ├── chatguru.controller.ts
│   │   ├── chatguru.service.ts
│   │   ├── dtos/
│   │   └── entities/
│   ├── mail/
│   ├── telegram/
│   └── wpp/
│       ├── wpp.controller.ts
│       ├── wpp.service.ts
│       ├── meta.client.ts          # Cliente HTTP para a Meta API
│       ├── meta-api-log.service.ts
│       ├── dtos/
│       └── entities/
├── queue/
│   └── queue.service.ts
├── redis/
│   ├── redis.module.ts
│   └── redis.service.ts
├── suppliers/
├── tenant_credentials/
├── tenants/
│   ├── tenants.controller.ts
│   ├── tenants.service.ts
│   └── entities/
├── webhook/
└── swagger/
    └── swagger.factory.ts
```

---

## 5. Multi-Tenancy {#multi-tenancy}

O serviço é **multi-tenant**: cada estabelecimento (tenant) que utiliza o omnichannel possui credenciais isoladas por canal.

### Como funciona
1. O cliente (ex.: `santa-bot-worker`) realiza a chamada HTTP incluindo o header `x-tenant-id`.
2. O `TenantResolverMiddleware` intercepta a requisição e busca as credenciais daquele tenant no Redis (cache) ou banco.
3. As credenciais descriptografadas são injetadas em `req.credentials` e ficam disponíveis para os controllers e services.

### Entidades de Multi-Tenancy

**Tenant**

| Campo            | Descrição                                        |
|------------------|--------------------------------------------------|
| `idTenant`       | UUID único do tenant.                            |
| `name`           | Nome do estabelecimento.                         |
| `clientId`       | UUID único para identificação externa.           |
| `clientSecretHash`| Hash do secret de autenticação.                 |

**TenantCredentials**

Armazena as credenciais de cada canal por tenant (ex.: token Meta API, chave ChatGuru), sempre criptografadas com AES-256-GCM.

---

## 6. Canais de Mensagem Suportados {#canais}

### WhatsApp (Meta Business API)
- Envio de mensagens de texto.
- Envio de templates pré-aprovados (com variáveis).
- Envio de mídias (imagem, vídeo, áudio, documento).
- Listagem e criação de templates.
- Download de mídias recebidas.
- Consulta de dados da conta WhatsApp.

### ChatGuru
- Envio de mensagens de texto.
- Execução de diálogos (fluxos automatizados do ChatGuru).
- Registro de logs de cada requisição.

### Telegram
- Envio de mensagens via Bot API.

### E-mail
- Envio de e-mails transacionais.

---

## 7. Endpoints da API {#endpoints}

### WhatsApp
| Método | Rota                     | Descrição                                       |
|--------|--------------------------|-------------------------------------------------|
| POST   | `/whatsapp/send`         | Envia mensagem de texto simples.                |
| POST   | `/whatsapp/send-template`| Envia um template pré-aprovado.                 |
| POST   | `/whatsapp/templates`    | Cria um novo template na Meta API.              |
| GET    | `/whatsapp/templates`    | Lista todos os templates aprovados.             |
| POST   | `/whatsapp/send-media`   | Upload e envio de mídia (multipart/form-data).  |
| GET    | `/whatsapp/media`        | Download de mídia pelo URL.                     |
| GET    | `/whatsapp/me`           | Dados da conta WhatsApp.                        |

### ChatGuru
| Método | Rota                      | Descrição                          |
|--------|---------------------------|------------------------------------|
| POST   | `/chatguru/send`          | Envia mensagem de texto.           |
| POST   | `/chatguru/dialog`        | Executa um diálogo.                |

### Telegram
| Método | Rota               | Descrição                   |
|--------|--------------------|-----------------------------|
| POST   | `/telegram/send`   | Envia mensagem via Telegram. |

### Webhook
| Método | Rota         | Descrição                                    |
|--------|--------------|----------------------------------------------|
| POST   | `/webhook/*` | Recebe eventos de retorno dos canais.         |

---

## 8. Segurança {#seguranca}
- **Autenticação:** JWT Bearer Token em todas as rotas protegidas.
- **Credenciais:** armazenadas com **AES-256-GCM** (payload com `iv`, `tag` e `value`).
- **Cache:** credenciais descriptografadas são mantidas no Redis para evitar repetição de operações criptográficas.
- **Rotas públicas:** apenas login e webhook ficam fora da autenticação JWT.
- **Logs:** todas as requisições ao ChatGuru são registradas na tabela `ChatGuruRequestLog` para auditoria.

---

## 9. Configuração e Execução {#configuracao}
Para detalhes de setup e variáveis de ambiente, consulte o README no repositório.

### Variáveis de Ambiente necessárias
```
DB_HOST, DB_PORT, DB_USERNAME, DB_PASSWORD, DB_DATABASE
REDIS_HOST, REDIS_PORT
JWT_SECRET
CHATGURU_BASE_URL
PORT
```

---

## 10. Tecnologias Utilizadas {#tecnologias}
- **Node.js / NestJS**
- **TypeScript**
- **PostgreSQL / TypeORM**
- **Redis**
- **Axios** (cliente HTTP para Meta API e ChatGuru)
- **Docker**
- **Swagger** (documentação de endpoints)

---

📌 **Observação:** Para adicionar um novo canal de mensagens, crie um módulo dedicado seguindo o padrão dos módulos `wpp`, `chatguru` ou `telegram`.
