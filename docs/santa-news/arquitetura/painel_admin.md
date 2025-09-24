# Documentação do Santa News - Web

📅 **Última atualização:** 24/09/2025

---

## Sumário
1. [Visão Geral](#visao-geral)  
2. [Arquitetura da Aplicação](#arquitetura-da-aplicacao)  
3. [Tecnologias e Dependências](#tecnologias-e-dependencias)  
4. [Estrutura de Pastas](#estrutura-de-pastas)  
5. [Integração com Backend](#integracao-com-backend)  
6. [Gerenciamento de Estado](#gerenciamento-de-estado)  
7. [Estilo e UI](#estilo-e-ui)  
8. [Boas Práticas e Padronizações](#boas-praticas-e-padronizacoes)  
9. [Configuração e Execução](#configs)  

---

## 1. Visão Geral {#visao-geral}
A aplicação web foi desenvolvida com **Next.js** e **TypeScript**, seguindo uma arquitetura modular.  
Ela se comunica com o backend via **API REST** para funcionalidades em tempo real.

---

## 2. Arquitetura da Aplicação {#arquitetura-da-aplicacao}
A arquitetura é baseada em **Modular + Context API**:

- **Camada de Apresentação (UI):** páginas, componentes e layouts reutilizáveis.  
- **Camada de Lógica:** hooks, services, utils e providers para integração com APIs e lógica de negócio.  
- **Camada de Dados:** cache local e Hooks React para sincronização com backend.  

---

## 3. Tecnologias e Dependências {#tecnologias-e-dependencias}
- **Linguagem:** TypeScript  
- **Framework:** Next.js 14+  
- **Rotas e Navegação:** Next.js App Router (`app/`) + layouts e páginas  
- **Estado Global:** Context API  
- **Comunicação HTTP:** Axios com interceptors  
- **Estilização:** Tailwind e Radix UI 
- **Autenticação:** JWT (access + refresh token)

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}

```
.
├── app/                              # Roteamento principal (Next.js App Router)
│   ├── locale/                       
│   └── api/                          # Endpoints da API interna
├── components/
│   ├── admin/                        # Área administrativa (sensível)
│   ├── layout/                       # Layouts globais
│   ├── login/                        # Autenticação
│   ├── panel-control/                # Painel principal
│   └── ui/
├── config/                           # Configurações críticas
├── hooks/
├── lib/
│   ├── api/                          # Cliente HTTP/configurações de API
│   └── validations/                  # Validações de segurança
├── messages/
├── providers/
│   └── auth/                         # Gerenciamento de sessão
├── public/
└── types/
```

---

## 5. Integração com Backend {#integracao-com-backend}
- **Base URL:** definida em `.env`.  
- **Axios Interceptors:** tratamento de erros e renovação automática de token.  
- **SSR:** algumas chamadas podem ser feitas no server para SEO e pré-carregamento.

---

## 6. Gerenciamento de Estado {#gerenciamento-de-estado}
- **AuthContext:** login, logout, refresh token, verificação de sessão.  
- **Formulários e Conteúdo:** hooks React.

---

## 7. Estilo e UI {#estilo-e-ui}
- **Componentização:** botões, inputs, cards e modais padronizados.  
- **Acessibilidade:** boas práticas de aria-labels e contraste.

---

## 8. Boas Práticas e Padronizações {#boas-praticas-e-padronizacoes}
- Estrutura modular e separação de responsabilidades.  
- TypeScript em todos os arquivos.  
- Uso de hooks e providers para lógica reutilizável.  
- Commits seguindo **Conventional Commits**.

---

## 9. Configuração e Execução {#configs}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub  
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/SantaNews-Client)

---

## 📌 Observações
- Variáveis sensíveis estão armazenadas no `.env`.  
- Os componentes seguem padrão reutilizável para consistência visual.  
