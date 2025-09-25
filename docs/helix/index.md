# Projeto Helix

> **Data de Emissão:** 24/09/2025  
> **Autor:** Felipe Ferreira

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Stakeholders](#stakeholders)  
3. [Arquitetura do Sistema](#arquitetura-do-sistema)  
4. [Funcionalidades Principais](#funcionalidades-principais)  
5. [Fluxo de Uso](#fluxo-de-uso)  
6. [Requisitos Técnicos](#requisitos-técnicos)

---

## 1. Visão Geral {#visão-geral}
A **API Helix** é uma solução backend desenvolvida para dar suporte às aplicações desenvolvidas pela equipe de inovação. Ela permite a entrada e visualização de sinais vitais de pacientes internados, além de consultar dados clínicos via integração com um banco Oracle externo. O sistema também conta com mecanismos de contingência para garantir funcionamento em casos de falhas de sistemas terceiros.

### Objetivo do Software
- Fornecer suporte à coleta de sinais vitais por dispositivos móveis.  
- Disponibilizar informações de pacientes internados em tempo real.  
- Integrar com o banco de dados Oracle (Tasy) para enriquecimento dos dados.  
- Oferecer suporte a aplicações hospitalares.  
- Garantir continuidade com sistema de fila em casos de falha na comunicação com o Tasy.

### Escopo
- Coleta e visualização de sinais vitais de pacientes.  
- Consulta a informações clínicas de pacientes internados.  
- Integração com base Oracle para dados complementares.  
- Suporte a rotinas de limpeza de dados e tokens expirados.  
- Sistema de fila para contingência.

---

## 2. Stakeholders {#stakeholders}

| Grupo                     | Responsabilidade                                                                 |
|---------------------------|----------------------------------------------------------------------------------|
| **Equipe de Inovação**   | Desenvolvimento, manutenção e versionamento da API.                            |
| **Sistemas Integrados**  | Consumo da API para suporte aos cuidados com o paciente e acesso a dados.                   |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
A API segue arquitetura **RESTful**, desenvolvida com **NestJS**, utilizando **Sequelize** como ORM e banco de dados **PostgreSQL**. Há integração adicional com **OracleDB** para consultas ao sistema Tasy.

- **Backend:** API em NestJS com Sequelize.  
- **Banco de Dados:** PostgreSQL (principal) e integração com Oracle (Tasy).  
- **ORM:** Sequelize para modelagem e migração de dados.  
- **Containers:** Docker para orquestração do ambiente.  
- **Fila:** Serviço de fila para contingência e desacoplamento de processos.  
- **Agendamentos (Schedules):** tarefas automatizadas para limpeza de dados.

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Entrada de Sinais Vitais:** usuários do app móvel registram dados como pressão, temperatura, etc.  
- **Consulta de Pacientes Internados:** busca de informações detalhadas de pacientes em tempo real.  
- **Integração com OracleDB:** complementação de dados clínicos via banco do Tasy.  
- **Centro de Comando:** apoio à supervisão e monitoramento hospitalar.  
- **Fila de Contingência:** persistência temporária de dados quando o Tasy estiver indisponível.  
- **Rotinas Agendadas:** exclusão de tokens expirados e remoção de dados antigos de pacientes.

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. Aplicativo móvel envia dados de sinais vitais via endpoint específico.  
2. API armazena os dados no PostgreSQL.  
3. Usuário solicita informações de um paciente internado.  
4. API consulta a base local e, se necessário, o Oracle (Tasy) para complementar os dados.  
5. Dados são retornados à aplicação para visualização.  
6. Em caso de falha no Tasy, os dados são enfileirados e processados posteriormente.  
7. Rotinas automatizadas executam a limpeza de tokens expirados e dados antigos conforme agendamento.

---

## 6. Requisitos Técnicos {#requisitos-técnicos}
- **Linguagem:** TypeScript  
- **Framework:** NestJS  
- **ORM:** Sequelize  
- **Banco de Dados:** PostgreSQL (principal), integração com OracleDB (Tasy)  
- **Hospedagem:** Docker  
- **Arquitetura:** RESTful  
- **Ferramentas de Agendamento:** Schedules internos do NestJS  
- **Ferramentas de Fila:** sistema de fila para contingência  

---

📌 **Observação:** Esta documentação deverá ser mantida atualizada a cada modificação estrutural ou de integração na API para garantir a rastreabilidade e conformidade com os requisitos institucionais de segurança.