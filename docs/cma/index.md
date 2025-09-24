# Projeto de Monitoramento Assistencial

>**Data de Emissão:** 23/09/2025  
>**Autor:** Felipe Ferreira

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
O **Projeto de Monitoramento Assistencial** é uma plataforma digital desenvolvida para centralizar o acesso a dados críticos do ambiente hospitalar, permitindo a visualização de informações operacionais e assistenciais em tempo real.
Voltada para profissionais da saúde e gestores, a solução facilita a tomada de decisões estratégicas, oferecendo visibilidade sobre o estado atual do hospital por meio de indicadores e painéis.

### Objetivo do Software
- Criar um centro de comando que integre as principais telas de monitoramento hospitalar.  
- Facilitar o acesso a informações assistenciais e operacionais em um único ambiente.  
- Garantir o acesso seguro, com controle de permissões por perfil de usuário.

### Escopo
- Painel de **monitoramento clínico**.  
- Acompanhamento das **altas hospitalares**. 
- Visualização de dados do app **Santa Health**.  
- Tela de **ocupação de leitos**.  
- **Gestão de usuários** com permissões administrativas.  

---

## 2. Stakeholders {#stakeholders}

| Grupo                     | Responsabilidade                                                                 |
|---------------------------|----------------------------------------------------------------------------------|
| **Equipe de Inovação**    | Projetar, implementar e manter a aplicação com base nos requisitos técnicos.     |
| **Gestores Assistenciais**| Consultar indicadores operacionais e clínicos para tomada de decisão.            |
| **Administradores**       | Gerenciar usuários e acessar recursos restritos do sistema.                      |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
A aplicação segue arquitetura **cliente-servidor**:

- **Frontend:** Tela centralizada (centro de comando) com ícones para cada painel de monitoramento. Desenvolvido em React.js com Next.js.  

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Monitoramento Clínico**: indicadores em tempo real sobre a condição dos pacientes e sinais vitais.  
- **Altas Hospitalares**: visualização de previsões e confirmação de altas.  
- **Santa Health**: dados sincronizados do aplicativo oficial de acompanhamento do paciente. 
- **Ocupação de Leitos**: painel com status e disponibilidade de leitos.   
- **Gestão de Usuários**: criação, edição e controle de permissões dos usuários do sistema.  

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. O **usuário do Tasy** acessa a plataforma e realiza o login.  
2. Após autenticação, é direcionado para a **página HOME**, com ícones representando cada painel disponível.  
3. O sistema verifica as **permissões do usuário**:
   - Se for um usuário padrão, acessa apenas as telas autorizadas.  
   - Se for administrador, tem acesso completo a todos os recursos da aplicação.  

---

## 6. Requisitos Técnicos {#requisitos-tecnicos}
- **Linguagens:** TypeScript, Node.js, React.js  
- **Frameworks:** Next.js  
- **Autenticação:** JWT  
- **Integrações:** Integração com API de Autenticação e Helix

---
📌 **Observação:** Esta documentação deve ser atualizada a cada nova versão do projeto para garantir rastreabilidade e alinhamento com os objetivos clínicos e técnicos.
