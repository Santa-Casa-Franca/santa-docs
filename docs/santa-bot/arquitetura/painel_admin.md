# Documentação Técnica — Painel Web (wpp-automation-client)

📅 **Última atualização:** 23/03/2026

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Arquitetura da Aplicação](#arquitetura)
3. [Tecnologias e Dependências](#tecnologias)
4. [Perfis de Acesso](#perfis)
5. [Estrutura de Pastas](#estrutura-de-pastas)
6. [Principais Módulos e Páginas](#modulos)
7. [Fluxo de Autenticação](#autenticacao)
8. [Integração com Backend](#integracao)
9. [Configuração e Execução](#configuracao)

---

## 1. Visão Geral {#visao-geral}
O **wpp-automation-client** é a interface web do SantaBot. Desenvolvido em **Next.js** com **TypeScript** e **Material UI**, oferece um painel para dois tipos de usuários: administradores (**Master**) e operadores dos estabelecimentos (**AME**). Através dele é possível gerenciar automações, filas, credenciais, templates, agendamentos e acompanhar o dashboard de mensagens enviadas.

---

## 2. Arquitetura da Aplicação {#arquitetura}
A arquitetura segue o modelo **App Router do Next.js 14+**:
- **Camada de Apresentação:** páginas, componentes e UI Components reutilizáveis.
- **Camada de Lógica:** hooks, services (Axios) e utils.
- **Camada de Dados:** integração com `santa-bot-automation-api` via requisições HTTP.
- **Gerenciamento de Estado:** Context API para autenticação e estado global de componentes.

---

## 3. Tecnologias e Dependências {#tecnologias}
- **Linguagem:** TypeScript
- **Framework:** Next.js 14+ (App Router)
- **UI:** Material UI (MUI)
- **Gráficos:** ECharts (`echarts`, `echarts-for-react`)
- **HTTP:** Axios
- **Estilização:** Tailwind CSS + MUI Emotion
- **Calendário:** react-day-picker
- **Autenticação:** JWT (armazenado em localStorage)

---

## 4. Perfis de Acesso {#perfis}

### Master (Administrador Global)
Acessa o grupo de rotas `(master)/`. Tem visão e controle completo do sistema:
- Dashboard consolidado de todos os estabelecimentos.
- Gestão de estabelecimentos (AMEs): cadastro, edição, vinculação de automações.
- Gestão de automações disponíveis no sistema.
- Gestão de filas BullMQ.
- Configuração de credenciais SIRESP por estabelecimento.
- Configuração de credenciais Tasy por estabelecimento.
- Gestão de templates WhatsApp.
- Gestão de usuários.

### Estabelecimento (Operador do AME)
Acessa o grupo de rotas `(automations)/`. Tem visão restrita ao seu próprio estabelecimento:
- Dashboard com mensagens enviadas e status dos agendamentos.
- Calendário de agendamentos.
- Agendamentos pendentes e confirmados.
- Preparações de exames.
- Transferências e cancelamentos.
- Chats com pacientes.
- Upload de planilhas de agendamentos.
- Configurações do estabelecimento.

---

## 5. Estrutura de Pastas {#estrutura-de-pastas}
```plaintext
src/
├── app/
│   ├── (authentication)/
│   │   └── login/                  # Página de login
│   ├── (automations)/              # Rotas do perfil Estabelecimento (AME)
│   │   ├── calendar/               # Calendário de agendamentos
│   │   ├── cancellation/           # Cancelamentos
│   │   ├── chats/                  # Chats com pacientes
│   │   ├── preparations/           # Preparações de exames
│   │   ├── scheduling/             # Agendamentos
│   │   ├── settings/               # Configurações
│   │   ├── sync/                   # Sincronização de dados
│   │   ├── templates/              # Templates WhatsApp
│   │   ├── transfer/               # Transferências
│   │   ├── upload/                 # Upload de planilhas
│   │   ├── (tasy)/                 # Módulo Tasy
│   │   │   ├── tasy-calendar/
│   │   │   ├── tasy-settings/
│   │   │   ├── tasy-templates/
│   │   │   └── tasy-upload/
│   ├── (master)/                   # Rotas do perfil Master (Admin)
│   │   ├── home-master/            # Dashboard Master
│   │   ├── automations-master/     # Gestão de automações
│   │   ├── establishment-master/   # Gestão de estabelecimentos
│   │   ├── queues-master/          # Gestão de filas
│   │   ├── siresp-settings-master/ # Configurações SIRESP por AME
│   │   ├── tasy-settings-master/   # Configurações Tasy por AME
│   │   ├── templates-master/       # Gestão de templates
│   │   └── users-master/           # Gestão de usuários
│   ├── agenda/                     # Agenda geral
│   ├── credentials/                # Configuração de credenciais de canal
│   ├── home/                       # Dashboard do estabelecimento
│   ├── schedulings/                # Listagem de agendamentos
│   ├── users/                      # Usuários
│   ├── layout.tsx                  # Layout raiz
│   ├── ThemeRegistry.tsx           # Configuração do tema MUI
│   └── page.tsx                    # Página inicial (redirect)
├── components/
│   ├── SideBar/                    # Sidebar de navegação
│   ├── automations/                # Componentes de automações (Upload, Templates, CallCenter, etc.)
│   ├── home/                       # Componentes do dashboard (gráficos, métricas)
│   ├── login/                      # Formulários de autenticação
│   └── master/                     # Componentes do painel Master
│       ├── MasterAutomations/
│       ├── MasterEstablishments/
│       ├── MasterHome/
│       ├── MasterQueue/
│       ├── MasterSirespSettings/
│       ├── MasterTasySettings/
│       └── MasterTemplates/
├── hooks/
│   └── use-breakpoint.ts           # Hook de responsividade
├── types/                          # Tipos TypeScript globais
│   ├── Doctors.ts
│   ├── Queue.ts
│   ├── Schedulings.ts
│   ├── SirespProfessionals.ts
│   └── TasyProfessionals.ts
├── ui/                             # Componentes de UI genéricos
│   ├── button.tsx
│   ├── calendar.tsx
│   ├── DateRangePicker.tsx
│   ├── PieChart.tsx
│   └── GradientCircularProgress.tsx
└── utils/                          # Funções utilitárias
    ├── Capitalize.ts
    ├── TextUtils.tsx
    └── getAutomation.tsx
```

---

## 6. Principais Módulos e Páginas {#modulos}

### Dashboard Master (`home-master`)
- Cards com total de automações, estabelecimentos e filas ativas.
- Gráfico de pizza com distribuição de eventos por tipo.
- Lista das últimas mensagens enviadas.
- Tempo médio de confirmação dos pacientes.

### Gestão de Estabelecimentos (`establishment-master`)
- Tabela de todos os AMEs cadastrados.
- Modal de cadastro com dados do estabelecimento (nome, CNPJ, endereço, e-mail).
- Vinculação de automações ao estabelecimento.
- Gestão de credenciais de canal (WhatsApp, etc.).

### Configuração SIRESP (`siresp-settings-master`)
- Formulário de credenciais SIRESP por estabelecimento:
  - Usuário, senha.
  - Primeiros/últimos 3 dígitos do CPF e RG.
  - Código do estabelecimento no SIRESP.

### Gestão de Filas (`queues-master`)
- Tabela de filas registradas no `santa-bot-tasks`.
- Criação e edição de filas com configurações de concorrência, retry e janela de tempo.
- Configuração de `QueueConfig` por fila.

### Dashboard Estabelecimento (`home`)
- Gráfico de barras com volume de mensagens por dia.
- Gráfico de pizza com status dos agendamentos.
- Métricas de confirmação, ausência e transferência.

### Calendário (`calendar`)
- Visão de agendamentos por data.
- Filtros por especialidade e status.

### Chats (`chats`)
- Histórico de conversas com pacientes por estabelecimento.

---

## 7. Fluxo de Autenticação {#autenticacao}
1. Usuário acessa a tela de login e informa suas credenciais.
2. Frontend envia `POST /auth/login`.
3. Backend retorna `access_token` (JWT) e dados do usuário.
4. Token armazenado no **localStorage**.
5. Todas as requisições subsequentes incluem `Authorization: Bearer <token>`.
6. Em caso de expiração, usuário é redirecionado para o login.

---

## 8. Integração com Backend {#integracao}
- **API Principal:** `santa-bot-automation-api` — gestão de dados (pacientes, agendamentos, estabelecimentos, automações).
- **API de Filas:** `santa-bot-tasks` — gestão de filas e jobs (acessada via painel Master).
- **Axios:** cliente HTTP com interceptors para tratamento de erros e injeção do token JWT.
- **Base URL:** configurada via variável de ambiente `NEXT_PUBLIC_API_URL`.

---

## 9. Configuração e Execução {#configuracao}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no repositório.

### Variáveis de Ambiente necessárias
```
NEXT_PUBLIC_API_URL     # URL da santa-bot-automation-api
NEXT_PUBLIC_TASKS_URL   # URL do santa-bot-tasks
```

---

📌 **Observação:** Variáveis sensíveis ficam no `.env.local`. Nunca versionar este arquivo.
