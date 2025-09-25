# Documentação do Projeto RPA Financeiro - Web

📅 **Última atualização:** 25/09/2025

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
A aplicação web do **RPA Financeiro** foi construída com **Next.js** e **TypeScript**, oferecendo uma interface simples e direta para que colaboradores do setor financeiro façam upload de arquivos.  
Esses arquivos são processados automaticamente via backend, com preenchimento de planilhas e execução de rotinas automatizadas.

---

## 2. Arquitetura da Aplicação {#arquitetura-da-aplicacao}
A arquitetura segue o padrão **modular baseado em hooks e contextos**:

- **Camada de UI:** componentes e páginas construídos com Material UI.  
- **Camada de Lógica:** hooks customizados, services e contextos.  
- **Integração com API:** via Axios e React Query.  
- **Upload e Processamento:** suporte a arquivos CSV, Excel, entre outros.

---

## 3. Tecnologias e Dependências {#tecnologias-e-dependencias}
- **Linguagem:** TypeScript  
- **Framework:** Next.js 15+  
- **Gerenciamento de Estado:** React Query  
- **Estilização:** Material UI (MUI)  
- **Comunicação HTTP:** Axios  
- **Controle de Ambiente:** Variáveis em `.env`

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```
public/                      # Arquivos estáticos acessíveis publicamente
src/
├── app/                     # Raiz da aplicação Next.js 
│   ├── components/          # Componentes reutilizáveis da interface
│   ├── modules/             # Módulos da aplicação
│   │   ├── financial/
│   │   └── regulation/
│   └── utils/               # Funções utilitárias
├── connection/              # Configuração de conexão 
.env                         # Variáveis de ambiente
package.json                 # Dependências e scripts do projeto
tsconfig.json                # Configuração do TypeScript
next.config.ts               # Configuração do Next.js
```

---

## 5. Integração com Backend {#integracao-com-backend}
- **Base URL:** definida via `.env`.  
- **Axios Interceptors:** utilizados para inserir o token automaticamente e tratar erros globais.  
- **Recebimento de resultados:** a resposta da API pode incluir status, logs ou arquivos para download.  

---

## 6. Gerenciamento de Estado {#gerenciamento-de-estado}
- **React Query:** gerenciamento de cache, refetch automático e controle de loading.  

---

## 7. Estilo e UI {#estilo-e-ui}
- **MUI:** utilizado para os componentes de interface.  
- **Componentização:** todos os elementos seguem padrão reutilizável com tema customizado.  

---

## 8. Boas Práticas e Padronizações {#boas-praticas-e-padronizacoes}
- Separação clara entre UI, lógica e integração.  
- Tipagem forte com TypeScript.  
- Nomeação semântica de variáveis, componentes e funções.  
- Versionamento usando Git com mensagens padronizadas.  

---

## 9. Configuração e Execução {#configs}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub  
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/task-flow-client){:target="_blank"}

---

## 📌 Observações
- As variáveis de ambiente (.env) devem ser definidas corretamente antes de executar o projeto.  
- A estrutura é pensada para escalar com novos módulos de automação e relatórios.  
- O frontend depende do backend estar em execução para enviar arquivos e receber os resultados.

