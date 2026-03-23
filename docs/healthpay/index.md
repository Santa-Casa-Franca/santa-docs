# HealthPay — Sistema de Pagamentos Santa Casa Mais

>**Data de Emissão:** 23/03/2026

>**Autor:** Kenidy Corrêa

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Stakeholders](#stakeholders)
3. [Arquitetura do Sistema](#arquitetura-do-sistema)
4. [Funcionalidades Principais](#funcionalidades-principais)
5. [Fluxo de Uso](#fluxo-de-uso)
6. [Requisitos Técnicos](#requisitos-tecnicos)

---

## 1. Visão Geral {#visao-geral}
O **HealthPay** é o sistema de gestão e processamento de pagamentos da **Santa Casa Mais**, clínica multidisciplinar da Santa Casa de Franca. Ele centraliza os contratos de planos firmados pelos pacientes, integra automaticamente com o **Tasy** (prontuário eletrônico) para manter os dados sincronizados, e processa os pagamentos através da **Getnet**, possibilitando cobrança por **cartão de crédito** (pagamento único ou recorrente) e **PIX**.

### Objetivo do Software
- Prover uma interface de gestão de contratos de planos de saúde da Santa Casa Mais.
- Automatizar a sincronização de contratos e clientes a partir do Tasy (Oracle).
- Processar pagamentos via cartão de crédito e PIX integrado à Getnet.
- Permitir o envio de links de pagamento externos diretamente ao cliente.

### Escopo
- Sincronização automática de contratos e clientes do banco Oracle (Tasy) a cada 30 minutos.
- Processamento de pagamentos: cartão crédito (à vista e recorrente), PIX e link externo.
- Gerenciamento de assinaturas/recorrências na Getnet.
- Painel administrativo para operadores da clínica.

---

## 2. Stakeholders {#stakeholders}

| Grupo                  | Responsabilidade                                                                        |
|------------------------|-----------------------------------------------------------------------------------------|
| **Time de Inovação**   | Desenvolver, manter e evoluir a plataforma.                                             |
| **Operadores da Clínica** | Acessar o painel para visualizar contratos pendentes e processar pagamentos.         |
| **Pacientes/Clientes** | Receber links de pagamento e efetuar o pagamento dos planos contratados.                |
| **Financeiro**         | Acompanhar recorrências, cobranças e status de pagamentos na Getnet.                    |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
O HealthPay é composto por **2 serviços**:

| Serviço            | Repositório      | Responsabilidade                                                          |
|--------------------|------------------|---------------------------------------------------------------------------|
| **Backend API**    | `healthpay-api`  | API REST: sincronização com Tasy, processamento de pagamentos via Getnet. |
| **Painel Web**     | `healthpay`      | Interface React para gestão de contratos, pagamentos e recorrências.      |

### Diagrama de Fluxo

```
[Tasy — Oracle DB]
        |
        | (cron a cada 30 min)
        v
[healthpay-api]  <-----> [MongoDB]
        |
        | (REST/JWT)
        v
[healthpay (frontend)]
        |
        | (pagamento)
        v
[Getnet API]
    |       |
    |       | (PIX / cartão)
    v       v
[Pix QR]  [Cartão crédito / recorrência]
```

Para documentação detalhada de cada serviço, consulte:
- [Backend API](arquitetura/backend_api.md)
- [Painel Web](arquitetura/painel_admin.md)

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Sincronização com Tasy**: cron de 30 em 30 minutos busca contratos, clientes e assinaturas no Oracle e atualiza o MongoDB. Inclui detecção de diferenças para evitar writes desnecessários.
- **Pagamento via Cartão**: cobrança única ou configuração de recorrência mensal na Getnet.
- **Pagamento via PIX**: geração de QR Code via Getnet com controle de expiração.
- **Link de Pagamento Externo**: geração de link público para o cliente pagar sem precisar de login no painel.
- **Gestão de Recorrências**: listagem de assinaturas ativas, projeção de cobranças futuras e histórico de cobranças via Getnet.
- **Dashboard de Contratos**: visualização de contratos pendentes, status de sincronização e resumo financeiro.
- **Bin Lookup**: consulta de informações do cartão pelo BIN (primeiros 6 dígitos) para exibir a bandeira.
- **Log completo de requisições**: todas as chamadas à Getnet são registradas no MongoDB para auditoria.

---

## 5. Fluxo de Uso {#fluxo-de-uso}

### Fluxo de Sincronização (Automático)
1. A cada 30 minutos o `SyncService` consulta a view `SCF_CMD_CONTRATO_INFO` no Oracle (Tasy).
2. Para cada contrato retornado:
   - Cria ou atualiza o **cliente** no MongoDB.
   - Cria ou atualiza a **assinatura** (plano/tabela de preço).
   - Cria ou atualiza o **contrato** com referência ao cliente e assinatura.
   - Se o contrato já tem pagamento no Tasy, registra o pagamento com status `finished`.
3. Contratos sem alterações são ignorados para não gerar writes desnecessários.

### Fluxo de Pagamento (Operador)
1. Operador faz login no painel web.
2. Visualiza contratos com pagamento pendente no dashboard.
3. Seleciona um contrato e escolhe o método:
   - **Cartão à vista:** insere os dados do cartão → Getnet processa e retorna confirmação.
   - **Recorrência:** configura cobrança mensal recorrente na Getnet com o cartão do cliente.
   - **PIX:** gera QR Code → operador ou cliente realiza pagamento → sistema confirma.
   - **Link externo:** gera link público `/pay/:paymentId` e envia ao cliente.
4. Status do pagamento é atualizado no MongoDB.

### Fluxo de Link Externo (Cliente)
1. Operador gera o link e envia ao cliente (WhatsApp, e-mail, etc.).
2. Cliente acessa o link público sem necessidade de login.
3. Cliente escolhe pagar via cartão ou PIX.
4. Pagamento processado pela Getnet e status atualizado.

---

## 6. Requisitos Técnicos {#requisitos-tecnicos}
- **Linguagens:** TypeScript, Node.js
- **Frameworks:** NestJS (backend), React + Vite (frontend)
- **Banco de Dados:** MongoDB (dados da aplicação), Oracle (Tasy — somente leitura)
- **Autenticação:** JWT
- **Hospedagem:** Docker
- **Integrações:** Tasy via Oracle DB (oracledb), Getnet API (cartão, PIX, recorrência)

---

📌 **Observação:** Esta documentação deve ser atualizada sempre que novos métodos de pagamento ou integrações forem adicionados.
