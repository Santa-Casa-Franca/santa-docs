# Ambientes e Deploy — SantaBot

📅 **Última atualização:** 23/03/2026

---

## Sumário
1. [Ambientes Disponíveis](#ambientes)
2. [Serviços e Portas](#servicos)
3. [Deploy com Docker](#docker)
4. [Variáveis de Ambiente](#variaveis)
5. [Dependências de Infraestrutura](#infra)

---

## 1. Ambientes Disponíveis {#ambientes}

| Ambiente       | Descrição                                                         |
|----------------|-------------------------------------------------------------------|
| **Desenvolvimento** | Ambiente local de cada desenvolvedor. Configurado via `.env`. |
| **Produção**   | Ambiente em execução nos servidores da Santa Casa. Containers Docker gerenciados via Docker Compose. |

---

## 2. Serviços e Portas {#servicos}

| Serviço                   | Repositório                  | Porta padrão |
|---------------------------|------------------------------|--------------|
| Backend Principal         | `santa-bot-automation-api`   | `3000`       |
| Gerenciador de Filas      | `santa-bot-tasks`            | `3001`       |
| Worker                    | `santa-bot-worker`           | `3002`       |
| Gateway de Mensagens      | `omnichannel-service`        | `3003`       |
| Painel Web                | `wpp-automation-client`      | `3004`       |
| Redis                     | —                            | `6379`       |
| PostgreSQL                | —                            | `5432`       |

---

## 3. Deploy com Docker {#docker}
Todos os serviços possuem `Dockerfile` e podem ser orquestrados via **Docker Compose**. O fuso horário dos containers deve ser configurado para `America/Sao_Paulo` (variável `TZ`).

### Exemplo de execução local
```bash
# Em cada repositório:
npm install
npm run start:dev

# Ou via Docker:
docker compose up -d
```

Para detalhes específicos de build e configuração de cada serviço, consulte o `README.md` do respectivo repositório.

---

## 4. Variáveis de Ambiente {#variaveis}

### santa-bot-automation-api
```env
DB_HOST=
DB_PORT=
DB_USERNAME=
DB_PASSWORD=
DB_DATABASE=
REDIS_HOST=
REDIS_PORT=
JWT_SECRET=
JOB_API_URL=         # URL do santa-bot-tasks
OMNI_API_URL=        # URL do omnichannel-service
TZ=America/Sao_Paulo
```

### santa-bot-tasks
```env
DB_HOST=
DB_PORT=
DB_USERNAME=
DB_PASSWORD=
DB_DATABASE=
REDIS_HOST=
REDIS_PORT=
PORT=3001
TZ=America/Sao_Paulo
```

### santa-bot-worker
```env
REDIS_HOST=
REDIS_PORT=
DB_HOST=
DB_PORT=
DB_USERNAME=
DB_PASSWORD=
DB_DATABASE=
MAIN_API_URL=        # URL do santa-bot-automation-api
MAIN_API_KEY=        # Chave de autenticação interna
OMNI_API_URL=        # URL do omnichannel-service
JOB_API_URL=         # URL do santa-bot-tasks
TZ=America/Sao_Paulo
```

### omnichannel-service
```env
DB_HOST=
DB_PORT=
DB_USERNAME=
DB_PASSWORD=
DB_DATABASE=
REDIS_HOST=
REDIS_PORT=
JWT_SECRET=
CHATGURU_BASE_URL=
PORT=3003
TZ=America/Sao_Paulo
```

### wpp-automation-client
```env
NEXT_PUBLIC_API_URL=     # URL do santa-bot-automation-api
NEXT_PUBLIC_TASKS_URL=   # URL do santa-bot-tasks
```

---

## 5. Dependências de Infraestrutura {#infra}

| Dependência  | Uso                                                               |
|--------------|-------------------------------------------------------------------|
| **PostgreSQL** | Banco de dados principal de todos os serviços backend.          |
| **Redis**    | Broker de mensagens BullMQ, cache de credenciais e sessões.       |
| **Docker**   | Containerização de todos os serviços.                             |
| **WhatsApp Business API (Meta)** | Canal principal de envio de mensagens.        |
| **SIRESP**   | Sistema externo acessado via web scraping para coleta de agendamentos. |

---

📌 **Observação:** Nunca versionar arquivos `.env` ou `.env.local`. Utilize gerenciadores de segredos (ex.: Vault) para ambientes de produção.
