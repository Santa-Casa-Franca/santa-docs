# HealthPay — Backend API (`healthpay-api`)

>**Repositório:** `healthpay-api`

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Estrutura de Módulos](#estrutura-de-modulos)
3. [Schemas (MongoDB)](#schemas-mongodb)
4. [Endpoints](#endpoints)
5. [Sincronização com Tasy](#sincronizacao-com-tasy)
6. [Integração Getnet](#integracao-getnet)
7. [Autenticação e Perfis](#autenticacao-e-perfis)

---

## 1. Visão Geral {#visao-geral}

API REST construída em **NestJS** responsável por:
- Sincronizar contratos e clientes do Tasy (Oracle) para o MongoDB.
- Processar pagamentos via Getnet (cartão, PIX, recorrência).
- Gerar links de pagamento públicos para envio ao cliente.
- Expor dados para o painel web e registrar logs de todas as chamadas à Getnet.

**Porta padrão:** `8500`

---

## 2. Estrutura de Módulos {#estrutura-de-modulos}

| Módulo              | Responsabilidade                                                             |
|---------------------|------------------------------------------------------------------------------|
| `AuthModule`        | Login por usuário/senha, emissão de JWT.                                    |
| `ContractsModule`   | Listagem paginada e detalhe de contratos, resumo financeiro por perfil.     |
| `CustomerModule`    | Busca de clientes e atualização de endereço de cobrança.                    |
| `SignatureModule`   | Gerenciamento de assinaturas/planos sincronizados do Tasy.                  |
| `PaymentsModule`    | Pagamento interno (autenticado): cartão, PIX, recorrência, link externo.    |
| `SyncModule`        | Cron de sincronização com Oracle e endpoints para disparo/status manual.    |
| `GetnetModule`      | Client HTTP para a API Getnet (OAuth2, token cache, log de requisições).    |
| `LogsModule`        | Registro de todas as requisições enviadas à Getnet para auditoria.          |

---

## 3. Schemas (MongoDB) {#schemas-mongodb}

### Client
| Campo                | Tipo     | Descrição                                          |
|----------------------|----------|----------------------------------------------------|
| `name`               | String   | Nome do cliente                                    |
| `document`           | String   | CPF/CNPJ (único)                                   |
| `tel`                | String   | Telefone                                           |
| `getnet_customer_id` | String   | ID do cliente na Getnet                            |
| `getnet_card_id`     | String   | ID do cartão tokenizado na Getnet                  |
| `billing_address`    | Object   | Endereço de cobrança (rua, número, cidade, CEP…)   |

### Contract
| Campo             | Tipo       | Descrição                                         |
|-------------------|------------|---------------------------------------------------|
| `sequence_number` | Number     | Número de sequência único (chave de sincronização)|
| `contract_number` | String     | Número do contrato                                |
| `status`          | String     | Status do contrato                                |
| `status_code`     | String     | Código de status                                  |
| `plan`            | String     | Plano contratado                                  |
| `seller`          | String     | Vendedor responsável                              |
| `client`          | ObjectId   | Referência ao documento `Client`                  |
| `payment`         | ObjectId   | Referência ao documento `Payment`                 |
| `signature`       | ObjectId   | Referência ao documento `Signature`               |

### Payment
| Campo              | Tipo     | Descrição                                           |
|--------------------|----------|-----------------------------------------------------|
| `getnet_payment_id`| String   | ID do pagamento na Getnet                           |
| `status`           | String   | Status do pagamento (`pending`, `finished`, etc.)   |
| `method`           | String   | Método: `card`, `pix`, `recurrence`, `external`     |
| `pix`              | Object   | QR Code, datas de criação/expiração e status        |
| `card`             | Object   | transaction_id, authorization_code, status          |
| `recurrence`       | Object   | Dados da cobrança recorrente                        |
| `contract`         | ObjectId | Referência ao documento `Contract`                  |

### Signature
| Campo             | Tipo     | Descrição                                          |
|-------------------|----------|----------------------------------------------------|
| `signature_seq`   | Number   | Sequência única do plano (sincronizado do Tasy)    |
| `table`           | String   | Nome da tabela/plano                               |
| `signature_price` | Number   | Valor mensal do plano                              |
| `getnet_plan_id`  | String   | ID do plano de recorrência na Getnet               |

---

## 4. Endpoints {#endpoints}

### Autenticação — `/auth`
| Método | Rota          | Auth | Descrição                        |
|--------|---------------|------|----------------------------------|
| POST   | `/auth/login` | —    | Login por usuário/senha → JWT    |

### Contratos — `/contracts`
> Requer JWT. Perfil `vendedor` vê apenas seus contratos; `admin` vê todos.

| Método | Rota                        | Descrição                                           |
|--------|-----------------------------|-----------------------------------------------------|
| GET    | `/contracts`                | Listagem paginada com filtros (busca, status, datas)|
| GET    | `/contracts/summary`        | Resumo financeiro (totais, pendências)              |
| GET    | `/contracts/:contract_id`   | Detalhe de um contrato                              |

**Query params de `/contracts`:**
- `page`, `limit` — paginação
- `search` — nome, CPF ou número de contrato
- `status` — filtra por status do contrato
- `pendingPayment` — `true` para mostrar apenas pendentes
- `dateRange` — `today | week | month | year`

### Clientes — `/customer`
| Método | Rota                    | Descrição                         |
|--------|-------------------------|-----------------------------------|
| GET    | `/customer/search`      | Busca por nome ou documento       |
| PATCH  | `/customer/:id/address` | Atualiza endereço de cobrança     |

### Assinaturas — `/subscription`
| Método | Rota                             | Descrição                                              |
|--------|----------------------------------|--------------------------------------------------------|
| GET    | `/subscription`                  | Lista assinaturas ativas na Getnet                     |
| GET    | `/subscription/charges`          | Lista cobranças (filtros disponíveis)                  |
| GET    | `/subscription/projection/:sub_id` | Projeção de cobranças futuras de uma assinatura      |
| GET    | `/subscription/:sub_id`          | Detalhe de cobranças de uma assinatura                 |

### Pagamentos (autenticado) — `/payments`
| Método | Rota                               | Descrição                                          |
|--------|------------------------------------|----------------------------------------------------|
| POST   | `/payments/normal/card`            | Pagamento à vista com cartão                       |
| POST   | `/payments/recurrence/card`        | Configuração de recorrência mensal com cartão      |
| GET    | `/payments/pix/:contractId`        | Gera QR Code PIX para um contrato                  |
| GET    | `/payments/confirm/pix/:id`        | Confirma status de pagamento PIX                   |
| GET    | `/payments/external-link/:contractId` | Gera link público de pagamento externo          |
| GET    | `/payments/bin/:bin`               | BIN lookup (bandeira do cartão pelos 6 primeiros dígitos) |

### Pagamentos Externos (público) — `/payments/external`
> Sem autenticação — usados pela página pública `/pay/:paymentId`.

| Método | Rota                                | Descrição                                    |
|--------|-------------------------------------|----------------------------------------------|
| GET    | `/payments/external/:paymentId`     | Retorna dados do contrato pelo ID de pagamento|
| GET    | `/payments/external/qr-code/:paymentId` | Gera QR Code PIX para pagamento externo  |
| POST   | `/payments/external/normal/card`    | Pagamento à vista com cartão (sem login)     |
| POST   | `/payments/external/recurrence/card`| Recorrência com cartão (sem login)           |
| GET    | `/payments/external/bin/:bin`       | BIN lookup (sem login)                       |

### Sincronização — `/sync`
> Requer JWT.

| Método | Rota                     | Descrição                                          |
|--------|--------------------------|----------------------------------------------------|
| GET    | `/sync`                  | Dispara sincronização manual com o Oracle          |
| GET    | `/sync/status`           | Status da última sincronização                     |
| GET    | `/sync/:contract_number` | Consulta um contrato específico no Oracle          |

---

## 5. Sincronização com Tasy {#sincronizacao-com-tasy}

O `SyncService` executa um **cron a cada 30 minutos** (`EVERY_30_MINUTES`) que:

1. Conecta ao Oracle e consulta a view `SCF_CMD_CONTRATO_INFO`.
2. Para cada contrato retornado:
   - **Upsert** do cliente (por CPF/documento).
   - **Upsert** da assinatura (por `signature_seq`).
   - **Upsert** do contrato (por `sequence_number`).
   - Se o contrato já possui pagamento registrado no Tasy, cria o `Payment` com status `finished`.
3. **Detecção de diferenças**: compara os dados retornados com o estado atual no MongoDB e ignora contratos sem alterações para evitar writes desnecessários.

A sincronização também pode ser acionada manualmente via `GET /sync` (requer autenticação).

---

## 6. Integração Getnet {#integracao-getnet}

O `GetnetService` centraliza todas as chamadas à API Getnet:

- **OAuth2 client_credentials**: obtém token de acesso e faz cache até a expiração para reutilização.
- **Suporte a ambientes**: variáveis separadas para produção (`GETNET_*`) e desenvolvimento (`DEV_GETNET_*`).
- **Log completo**: toda requisição enviada à Getnet é registrada no MongoDB (módulo `LogsModule`), incluindo body, headers e resposta.
- **Mascaramento de dados sensíveis**: números de cartão e CVV são mascarados antes de salvar no log.

Operações suportadas:
| Operação          | Descrição                                                 |
|-------------------|-----------------------------------------------------------|
| Pagamento à vista | `POST /v1/payments/credit` na Getnet                     |
| Recorrência       | Criação de assinatura recorrente na Getnet                |
| PIX               | Geração de QR Code com controle de expiração             |
| BIN Lookup        | Consulta de bandeira pelo BIN do cartão                   |
| Token de cartão   | Tokenização do cartão do cliente                          |

---

## 7. Autenticação e Perfis {#autenticacao-e-perfis}

- **JWT** gerado no login (`POST /auth/login`).
- Dois perfis de acesso:

| Perfil      | Acesso                                                          |
|-------------|-----------------------------------------------------------------|
| `admin`     | Todos os contratos, resumo global, disparo de sync manual       |
| `vendedor`  | Apenas contratos onde `seller === username` do token            |

- As rotas dos módulos de Contratos e Sync exigem `JwtAuthGuard`.
- As rotas de pagamento externo (`/payments/external/*`) são **públicas** (sem autenticação).
