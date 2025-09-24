# 📄 Projeto Santa Health

> **Data de Emissão:** 24/09/2025  
> **Autor:** Felipe Ferreira

---

## 📚 Sumário
1. [Visão Geral](#1-visão-geral)  
2. [Stakeholders](#2-stakeholders)  
3. [Arquitetura do Sistema](#3-arquitetura-do-sistema)  
4. [Funcionalidades Principais](#4-funcionalidades-principais)  
5. [Fluxo de Uso](#5-fluxo-de-uso)  
6. [Requisitos Técnicos](#6-requisitos-técnicos)

---

## 1. Visão Geral
O **Projeto Santa Health** é um aplicativo móvel voltado para otimizar o trabalho da equipe de saúde no **Hospital Geral** e no **Hospital do Coração**. Seu principal objetivo é apoiar médicos, enfermeiros, técnicos e equipes multidisciplinares no acompanhamento de pacientes, por meio da integração com o sistema hospitalar **Tasy** e seguindo as melhores práticas de desenvolvimento mobile.

### 🎯 Objetivos do Software
- Apoiar o atendimento clínico e a tomada de decisão no cuidado ao paciente.
- Acompanhar os sinais vitais do paciente em tempo real.

### 📌 Escopo Funcional
- Autenticação integrada ao sistema **Tasy**.  
- Visualização de dados clínicos detalhados dos pacientes.  
- Registro de informações e evolução clínica.  
- Acompanhamento do censo hospitalar.  

---

## 2. Stakeholders

| Grupo                        | Responsabilidades                                                                 |
|------------------------------|-----------------------------------------------------------------------------------|
| **Equipe de Inovação**       | Desenvolvimento, manutenção e evolução contínua da aplicação.                     |
| **Equipe Assistencial**      | Uso da plataforma para acompanhamento e registro clínico de pacientes.            |
| **Administração Hospitalar** | Monitoramento de dados estratégicos, como ocupação e fluxo de pacientes.          |

---

## 3. Arquitetura do Sistema
O sistema foi projetado com uma arquitetura moderna, escalável e de fácil manutenção.

- **📱 Frontend (Mobile):** Desenvolvido em **React Native**, com foco em performance e experiência do usuário.  
- **🎨 Estilização:** Utiliza **NativeWind**, que adapta os conceitos do Tailwind CSS ao ambiente mobile.  
- **📝 Formulários:** Criados com **React Hook Form** e validados por **Zod** para maior confiabilidade.  
- **🔐 Autenticação:** Integração direta com o sistema hospitalar **Tasy** para login unificado.  

---

## 4. Funcionalidades Principais
- **🔐 Autenticação Integrada:** Login seguro e unificado com o sistema Tasy.  
- **📋 Visualização de Pacientes:** Lista de pacientes por setor.  
- **🗂️ Detalhamento Clínico:** Exibição de informações como sinais vitais, medicamentos, diagnósticos e exames.  
- **✅ Tarefas e Checklists:** Visualização e marcação de atividades a serem realizadas por tipo de profissional.  
- **📍 Alocação por Setor:** Permite selecionar o setor de trabalho no início do turno.  
- **📊 Censo Hospitalar:** Acompanhamento em tempo real da ocupação de leitos por unidade.  
- **✍️ Registro de Evoluções:** Inserção de informações clínicas diretamente pelo app, conforme permissões.  

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. Usuário acessa o aplicativo e insere suas credenciais.
2. Sistema realiza autenticação integrada via Tasy.
3. Usuário seleciona o setor em que está alocado.
4. Aplicativo carrega os dados dos pacientes daquele setor.
5. Usuário visualiza informações clínicas, registra evoluções e acompanha tarefas relacionadas ao seu cargo.

---

## 6. Requisitos Técnicos {#requisitos-tecnicos}
- **Linguagens:** JavaScript, TypeScript  
- **Frameworks:** React Native  
- **Estilização:** NativeWind (Tailwind CSS para React Native)  
- **Formulários:** React Hook Form, Zod  
- **Gerenciadores de Pacotes:** Yarn, npm  
- **Autenticação:** Integração com sistema Tasy   
- **Integrações:** Integração com API Helix

---
📌 **Observação:** Esta documentação deve ser atualizada a cada nova versão do projeto para garantir rastreabilidade e alinhamento com os objetivos clínicos e técnicos.