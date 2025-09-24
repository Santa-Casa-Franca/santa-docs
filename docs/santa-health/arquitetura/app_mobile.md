# Aplicativo Mobile - Santa Health

📅 **Última atualização:** 24/09/2025

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Arquitetura do Aplicativo](#arquitetura-do-aplicativo)  
3. [Tecnologias e Dependências](#tecnologias-e-dependências)  
4. [Estrutura de Pastas](#estrutura-de-pastas)  
5. [Fluxo de Autenticação](#fluxo-de-autenticação)  
6. [Navegação](#navegação)  
7. [Integração com Backend](#integração-com-backend)  
8. [Gerenciamento de Estado](#gerenciamento-de-estado)  
9. [Estilo e UI](#estilo-e-ui)  
10. [Boas Práticas e Padronizações](#boas-práticas-e-padronizações)  
11. [Configuração e Execução](#configs)

---

## 1. Visão Geral {#visão-geral}
O **Santa Health** é um aplicativo mobile desenvolvido para auxiliar médicos, enfermeiros e demais profissionais de saúde no atendimento aos pacientes do **Hospital Geral** e do **Hospital do Coração**. Ele permite acesso a informações clínicas, organização de tarefas e acompanhamento da ocupação de leitos.
A aplicação se comunica com o sistema hospitalar **Tasy** e segue boas práticas de desenvolvimento mobile com foco em performance e segurança.

---

## 2. Arquitetura do Aplicativo {#arquitetura-do-aplicativo}
A arquitetura adota o padrão **modular**, com separação clara entre UI, lógica e dados.

- **Apresentação (UI):** Telas e componentes visuais.
- **Lógica de Negócio:** Hooks, contextos e serviços de API.
- **Persistência de Dados:** Armazenamento local e sincronização com Tasy.
- **Integração com Sistemas Hospitalares:** API REST para dados clínicos e operacionais.

---

## 3. Tecnologias e Dependências {#tecnologias-e-dependências}
- **Linguagens:** TypeScript, JavaScript  
- **Frameworks:** React Native + Expo  
- **Navegação:** React Navigation  
- **Formulários:** React Hook Form + Zod  
- **Validação:** Zod  
- **Estado Global:** Context API  
- **Estilização:** NativeWind (Tailwind CSS adaptado para RN)  
- **Ambiente:** Configurado via `.env`  
- **Comunicação HTTP:** Axios  
- **Integração com EMR:** Sistema Tasy via REST API  

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```
florence-health-app/
├── assets/
├── patches/
├── src/
│ ├── assets/
│ ├── components/
│ ├── context/
│ ├── data/
│ ├── enum/
│ ├── hooks/
│ ├── routes/
│ ├── screens/
│ ├── services/
│ ├── types/
│ ├── utils/
│ ├── validations/
│ └── styles.css
├── .editorconfig
├── .env.credentials
├── .eslintrc.json
├── .gitignore
├── app.json
├── App.tsx
├── babel.config.js
├── eas.json
├── google-services.json
├── package.json
├── postcss.config.js
├── README.md
├── tailwind.config.js
├── tsconfig.js
├── webpack.config.js
└── yarn.lock
```

---

## 5. Fluxo de Autenticação {#fluxo-de-autenticação}
1. Usuário insere suas credenciais (login/senha institucional).
2. App envia autenticação para o sistema **Tasy**.
3. Se válido, o backend retorna um token de sessão.
4. App armazena o token localmente e inicia a sessão autenticada.
5. A cada nova sessão, o token é validado ou renovado automaticamente.

---

## 6. Navegação {#navegação}
A navegação é composta por:

- **Stack Inicial:** Login, seleção de setor.  
- **Stack Principal:** Telas de pacientes, tarefas, censo hospitalar, perfil.  
- **Tabs Inferiores:** Navegação rápida entre setores, pacientes e tarefas.  
- **Modais:** Visualização de detalhes clínicos e registros.

---

## 7. Integração com Backend {#integração-com-backend}
- **Base URL:** Definida no `.env`  
- **Autenticação:** Realizada com token JWT retornado pelo sistema Tasy  
- **Requisições:** API REST para consultas e atualizações de dados clínicos  
- **Tratamento de Erros:** Interceptadores via Axios e fallback para mensagens amigáveis  
- **Segurança:** Todas as requisições autenticadas via header `Authorization: Bearer <token>`  

---

## 8. Gerenciamento de Estado {#gerenciamento-de-estado}
- **AuthContext:** Autenticação, login, logout e gerenciamento de token  
- **NotificationsDeviceContext:** Dispara notificações e mensagens

---

## 9. Estilo e UI {#estilo-e-ui}
- **Design System:** Utiliza NativeWind com classes personalizadas  
- **Componentização:** Inputs, botões, cards e ícones reutilizáveis  

---

## 10. Boas Práticas e Padronizações {#boas-práticas-e-padronizações}
- Uso de **TypeScript** para segurança de tipos  
- Organização modular e separação de responsabilidades  
- Utilização de **eslint** para padronização de código  
- Commits no padrão **Conventional Commits**

---

## 11. Configuração e Execução {#configs}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/Florence-Health-App)

---

📌 **Observação:** Este documento deve ser revisado a cada nova versão do aplicativo para refletir mudanças de arquitetura, dependências ou funcionalidades.

