# HealthPay — Ambientes

---

## Serviços e Portas

| Serviço         | Repositório      | Porta (dev) | Porta (produção) |
|-----------------|------------------|-------------|------------------|
| Backend API     | `healthpay-api`  | 8500        | 8500             |
| Painel Web      | `healthpay`      | 8080        | 4000             |

---

## Docker

Cada repositório possui seu próprio `docker-compose.yml`.

### `healthpay-api`
```yaml
services:
  api:
    build: .
    container_name: healthpay-api
    restart: always
    ports:
      - "8500:8500"
    environment:
      VAULT_ADDR: ${VAULT_ADDR}
      VAULT_TOKEN: ${VAULT_TOKEN}
```

### `healthpay`
```yaml
services:
  vite:
    build: .
    container_name: healthpay
    ports:
      - "4000:4000"
    environment:
      VAULT_ADDR: ${VAULT_ADDR}
      VAULT_TOKEN: ${VAULT_TOKEN}
    restart: always
```

> As variáveis de ambiente são injetadas via **Vault** em produção.

---

## Variáveis de Ambiente — `healthpay-api`

| Variável                | Descrição                                              |
|-------------------------|--------------------------------------------------------|
| `JWT_SECRET`            | Chave secreta para assinatura do JWT                   |
| `JWT_REFRESH_SECRET`    | Chave secreta para refresh token                       |
| `APP_PORT`              | Porta da API (padrão: `8500`)                          |
| `MONGO_URI`             | URI de conexão com o MongoDB                           |
| `BASE_URL_TASY`         | URL base da API Tasy (ex: `http://192.168.116.7:7070`) |
| `DB_USER_TASY`          | Usuário do banco Oracle (Tasy)                         |
| `DB_PASSWORD_TASY`      | Senha do banco Oracle (Tasy)                           |
| `DB_DSN`                | DSN de conexão Oracle                                  |
| `ORACLE_CLIENT_PATH`    | Caminho do Oracle Instant Client no servidor           |
| `GETNET_CLIENT_ID`      | Client ID da Getnet (produção)                         |
| `GETNET_CLIENT_SECRET`  | Client Secret da Getnet (produção)                     |
| `GETNET_BASE_URL`       | URL base da API Getnet (produção)                      |
| `SELLER_ID`             | ID do estabelecimento na Getnet (produção)             |
| `DEV_GETNET_CLIENT_ID`  | Client ID da Getnet (desenvolvimento/sandbox)          |
| `DEV_GETNET_CLIENT_SECRET` | Client Secret da Getnet (desenvolvimento/sandbox)   |
| `DEV_GETNET_BASE_URL`   | URL base da API Getnet (desenvolvimento/sandbox)       |
| `DEV_SELLER_ID`         | ID do estabelecimento na Getnet (desenvolvimento)      |

---

## Dependências de Infraestrutura

| Dependência | Uso                                              |
|-------------|--------------------------------------------------|
| MongoDB     | Banco principal da aplicação                     |
| Oracle DB   | Banco do Tasy (somente leitura via view)         |
| Getnet API  | Gateway de pagamentos (cartão, PIX, recorrência) |

> O Oracle requer o **Oracle Instant Client** instalado no servidor onde a API roda, com o caminho configurado em `ORACLE_CLIENT_PATH`.
