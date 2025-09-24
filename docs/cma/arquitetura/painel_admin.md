# Documentação do Centro de Monitoramento Assistencial - Web

📅 **Última atualização:** 23/09/2025

---

## Sumário
1. [Visão Geral](#visao-geral)  
2. [Arquitetura da Aplicação](#arquitetura-da-aplicacao)  
3. [Tecnologias e Dependências](#tecnologias-e-dependencias)  
4. [Estrutura de Pastas](#estrutura-de-pastas)  
5. [Fluxo de Autenticação](#fluxo-de-autenticacao)  
6. [Integração com Backend](#integracao-com-backend)  
7. [Gerenciamento de Estado](#gerenciamento-de-estado)  
8. [Estilo e UI](#estilo-e-ui)  
9. [Boas Práticas e Padronizações](#boas-praticas-e-padronizacoes)  
10. [Configuração e Execução](#configuracao-e-execucao)  

---

## 1. Visão Geral {#visao-geral}
A aplicação web do **Centro de Monitoramento Assistencial** foi desenvolvida com **Next.js** e **TypeScript**, com foco em centralizar as principais informações assistenciais e operacionais do hospital.
Através de uma interface baseada em ícones e dashboards, os usuários (com acesso via Tasy) podem navegar entre as telas de monitoramento: monitoramento clínico, altas hospitalares, dados do app Santa Health, ocupação de leitos e painel de gestão de usuários.

---

## 2. Arquitetura da Aplicação {#arquitetura-da-aplicacao}
A arquitetura é baseada em **cliente-servidor** com separação de camadas e foco em escalabilidade:

- **Camada de Apresentação (UI):** interface em React com layout baseado em ícones e painéis.  
- **Camada de Lógica:** hooks, contextos, serviços e utils para orquestração da lógica de negócio.  
- **Camada de Dados:** consumo de API REST externa com autenticação JWT, integração com o EMR Tasy e API Helix.  
- **Renderização:** suporte a renderização no servidor (SSR) via Next.js.

---

## 3. Tecnologias e Dependências {#tecnologias-e-dependencias}
- **Linguagem:** TypeScript  
- **Framework:** Next.js 14+  
- **Rotas e Navegação:** App Router (`/app`) com layouts e módulos separados  
- **Estado Global:** Context API  
- **Comunicação HTTP:** Axios com interceptors  
- **Estilização:** Material UI (MUI) e Tailwind  
- **Autenticação:** JWT (Access + Refresh Token)  

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```
public/
├── images/
│   ├── icons/
│   │   ├── ages/
│   │   └── patient-alerts/
├── logos/
└── lotties/

src/
├── app/
│   ├── admin-panel/
│   ├── application/
│   │   └── alertas/
│   ├── authentication/
│   │   ├── auth/
│   │   └── entrar/
│   ├── contexts/
│   ├── home/
│   ├── hospital-discharge/
│   ├── open-pages/
│   │   ├── dashboards/
│   │   │   ├── hospital-discharge/
│   │   │   ├── occupancy/
│   │   │   └── santa-health/
│   │   ├── hemodialysis/
│   │   │   ├── form/
│   │   │   ├── scan-client/
│   │   │   └── weight-scan/
│   │   └── monitoramento-clinico/
│   └── style-guide/
├── components/
│   ├── AdminPanel/
│   │   ├── Applications/
│   │   ├── Permissions/
│   │   ├── Roles/
│   │   ├── Sectors/
│   │   └── Users/
│   ├── Alerts/
│   │   ├── Card/
│   │   │   └── Header/
│   │   ├── Columns/
│   │   ├── Modal/
│   │   └── Tag/
│   ├── ClinicalMonitoring/
│   │   ├── Delayed/
│   │   │   ├── Card/
│   │   │   ├── Carousel/
│   │   │   └── Content/
│   │   └── Deterioration/
│   │       ├── Card/
│   │       ├── Carousel/
│   │       └── Content/
│   ├── container/
│   ├── Dashboards/
│   │   ├── BarChartSkeleton/
│   │   ├── DonutChartSkeleton/
│   │   ├── HospitalDischarge/
│   │   ├── Occupancy/
│   │   └── SantaHealthApp/
│   │       ├── Deterioration/
│   │       │   ├── Card/
│   │       │   └── DonutChart/
│   │       ├── NursingNotes/
│   │       │   ├── BarCharts/
│   │       │   └── Card/
│   │       └── VitalSigns/
│   │           ├── BarCharts/
│   │           └── Card/
│   ├── forms/
│   ├── Hemodialysis/
│   │   ├── Form/
│   │   ├── ScanClient/
│   │   └── WeightScan/
│   ├── Home/
│   ├── HospitalDischarge/
│   ├── Icons/
│   ├── Layout/
│   │   ├── Content/
│   │   │   └── Header/
│   │   ├── Loading/
│   │   └── Sidebar/
│   ├── shared/
│   ├── Skeletons/
│   │   └── Text/
│   ├── style-guide/
│   └── ui/
│       └── tremor-charts/
├── config/
│   └── navigation/
├── hooks/
├── lib/
├── provider/
│   ├── dashboard/
│   │   └── santa-health/
│   └── hemodialysis/
├── service/
│   ├── alerts/
│   ├── auth/
│   ├── clinical-monitoring/
│   │   ├── delayed/
│   │   └── deterioration/
│   ├── dashboard/
│   │   └── santa-health/
│   ├── hemodialysis/
│   ├── socketio/
│   └── vital-signs/
├── types/
│   ├── Alerts/
│   ├── ClinicalMonitoring/
│   │   ├── Delayed/
│   │   └── Deterioration/
│   ├── Dashboard/
│   │   ├── Discharge/
│   │   ├── Occupancy/
│   │   └── SantaHealth/
│   ├── Hemodialysis/
│   └── User/
└── validations/

```

---

## 5. Fluxo de Autenticação {#fluxo-de-autenticacao}
1. O usuário (Tasy) acessa a tela de login e envia credenciais.  
2. O frontend se conecta a uma API externa para fazer o login.
3. Tokens são armazenados nos `Cookies`.  
4. O token é renovado automaticamente via refresh token.

---

## 6. Integração com Backend {#integracao-com-backend}
- **Base URL**: definida via variável `NEXT_PUBLIC_AUTH_API_URL` no arquivo `.env`.
- **Axios**: utilizado para realizar chamadas HTTP.
- **Socket.IO**: utilizado para comunicação em tempo real com o backend, especialmente para atualizações ao vivo.
- **Autenticação**: o token é lido via `js-cookie` e adicionado automaticamente nos headers com `Axios Interceptors`.
- **Tratamento de Erros**: interceptores também lidam com respostas como 401, disparando ações como logout ou renovação de token.
- **Endpoints REST**: o frontend consome os serviços expostos pela API do backend.

---

## 7. Gerenciamento de Estado {#gerenciamento-de-estado}
- **AuthContext:** login, logout, tokens e sessão ativa.  
- **DashboardContext:** controle dos dados dos painéis (clínico, leitos, altas, etc).  
- **UserContext:** dados do usuário autenticado e permissões (admin ou padrão).  
- **SocketProvider:** gerencia a conexão WebSocket e distribui os dados em tempo real para os contextos relevantes.
- **Hooks Personalizados:** para ações como `useUser()`, `useDashboard()`, etc.

---

## 8. Estilo e UI {#estilo-e-ui}
- **Design System:** baseado em Material UI (MUI).  
- **Componentização:** layout padronizado com botões, ícones e modais reutilizáveis.  
- **Responsividade:** otimizado para desktop, com fallback para tablets e celulares.  
- **Acessibilidade:** uso de `aria-label`, contraste adequado e foco visível.

---

## 9. Boas Práticas e Padronizações {#boas-praticas-e-padronizacoes}
- Estrutura modular com separação clara entre lógica, dados e UI.  
- Tipagem obrigatória em todos os componentes.  
- Reutilização de lógica via hooks e contextos.  
- Commits seguindo o padrão **Conventional Commits**.  
- Componentes e páginas documentados com JSDoc.

---

## 10. Configuração e Execução {#configuracao-e-execucao}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/cma-client)

---

## 📌 Observações
- Variáveis sensíveis estão armazenadas no `.env`.
- Os componentes seguem padrão reutilizável para consistência visual.
- O layout base está em `app/application/layout.tsx`.

---