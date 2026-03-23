# HealthPay — Painel Web (`healthpay`)

>**Repositório:** `healthpay`

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Stack Tecnológica](#stack-tecnologica)
3. [Estrutura de Rotas](#estrutura-de-rotas)
4. [Páginas e Funcionalidades](#paginas-e-funcionalidades)
5. [Autenticação e Contextos](#autenticacao-e-contextos)

---

## 1. Visão Geral {#visao-geral}

Interface web para operadores da clínica Santa Casa Mais gerenciarem contratos de planos, processarem pagamentos e acompanharem recorrências. Também serve a página pública de pagamento que os clientes acessam via link externo, sem necessidade de login.

**Porta (produção):** `4000`
**Porta (desenvolvimento):** `8080`

---

## 2. Stack Tecnológica {#stack-tecnologica}

| Tecnologia          | Uso                                                       |
|---------------------|-----------------------------------------------------------|
| React + Vite        | Framework e bundler da aplicação                          |
| TypeScript          | Linguagem principal                                       |
| React Router DOM    | Gerenciamento de rotas client-side                        |
| TanStack Query      | Cache e gerenciamento de estado de requisições HTTP       |
| shadcn/ui           | Componentes de interface (Button, Dialog, Table, etc.)    |
| Tailwind CSS        | Estilização utilitária                                    |
| Sonner / Toaster    | Notificações toast                                        |

---

## 3. Estrutura de Rotas {#estrutura-de-rotas}

```
/login              → Tela de login (pública, redireciona para /dashboard se logado)
/pay/:paymentId     → Página de pagamento externo (totalmente pública)
/expired            → Página de link expirado (pública)
/                   → Redireciona para /dashboard
/dashboard          → Dashboard principal (protegida)
/contracts          → Gestão de contratos (protegida)
/recurrences        → Gestão de recorrências (protegida)
*                   → Página 404
```

As rotas protegidas usam o componente `ProtectedRoute`, que redireciona para `/login` caso o usuário não esteja autenticado. As rotas públicas usam `PublicRoute`, que redireciona para `/dashboard` caso o usuário já esteja logado.

---

## 4. Páginas e Funcionalidades {#paginas-e-funcionalidades}

### Login (`/login`)
- Formulário de usuário e senha.
- Chama `POST /auth/login` e armazena o JWT no contexto de autenticação.

### Dashboard (`/dashboard`)
- Resumo financeiro geral: totais de contratos, contratos pendentes de pagamento.
- Cards com métricas obtidas via `GET /contracts/summary`.
- Visão rápida dos contratos pendentes de pagamento para ação imediata.

### Contratos (`/contracts`)
- Tabela paginada de contratos com filtros:
  - Busca por nome, CPF ou número de contrato.
  - Filtro por status e por período (`today / week / month / year`).
  - Toggle "apenas pendentes de pagamento".
- Ao selecionar um contrato, abre modal/drawer com:
  - Dados do contrato, cliente e plano.
  - Opções de pagamento: **cartão à vista**, **recorrência**, **PIX**, **link externo**.
  - Para cartão: formulário com número, nome, validade, CVV + BIN lookup automático.
  - Para PIX: exibe QR Code gerado e aguarda confirmação.
  - Para link externo: gera e exibe o link para envio ao cliente.

### Recorrências (`/recurrences`)
- Lista assinaturas ativas buscadas via `GET /subscription`.
- Para cada assinatura: detalhe das cobranças (`GET /subscription/:sub_id`) e projeção futura (`GET /subscription/projection/:sub_id`).
- Histórico de cobranças via `GET /subscription/charges`.

### Pagamento Externo (`/pay/:paymentId`)
- Página pública acessada pelo cliente via link enviado pelo operador.
- Carrega os dados do contrato via `GET /payments/external/:paymentId`.
- Permite ao cliente escolher entre **cartão** (à vista ou recorrência) e **PIX**.
- Chama os endpoints `/payments/external/*` (sem autenticação).
- Se o link estiver expirado, redireciona para `/expired`.

---

## 5. Autenticação e Contextos {#autenticacao-e-contextos}

### `AuthContext`
- Armazena o usuário autenticado e o JWT.
- Expõe `login()` e `logout()`.
- Persiste o estado de sessão entre recarregamentos de página.

### `ContractContext`
- Compartilha o estado do contrato selecionado entre as páginas protegidas.
- Evita repetição de chamadas à API ao navegar entre modal e listagem.

### Tema
- Suporte a tema claro/escuro via `ThemeProvider` com persistência no `localStorage` (`payservice-theme`).
