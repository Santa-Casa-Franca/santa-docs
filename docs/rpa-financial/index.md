# Projeto RPA Financeiro

>**Data de Emissão:** 25/09/2025

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
O **Projeto RPA Financeiro** é uma solução voltada para automatizar o preenchimento e processamento de dados financeiros a partir de arquivos enviados manualmente por colaboradores do setor financeiro.  
Seu principal objetivo é reduzir o retrabalho humano, mitigar erros e acelerar a integração de dados com sistemas internos.

### Objetivo do Software
- Automatizar o preenchimento de informações financeiras com base em templates padronizados.  
- Aumentar a eficiência operacional e garantir conformidade com os padrões internos de controle.  

### Escopo
- Upload e validação de planilhas financeiras.  
- Processamento automático com base em templates cadastrados.  
- Registro de logs de tarefas e extração de dados.  
- Visualização do status das automações e arquivos processados.  

---

## 2. Stakeholders {#stakeholders}

| Grupo                     | Responsabilidade                                                                |
|---------------------------|---------------------------------------------------------------------------------|
| **Equipe de Inovação**    | Desenvolver, manter e evoluir a plataforma conforme as demandas de automação. |
| **Equipe Financeira**     | Enviar arquivos e monitorar o status das automações.                          |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
A aplicação segue arquitetura **cliente-servidor**, com foco em automações via API:

- **Frontend:** Interface web para upload de arquivos, visualização de status e logs de execução.  
- **Backend:** API REST com motor de automação, processamento de arquivos e integração com serviços externos.  
- **Banco de Dados:**  PostgreSQL.

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Upload de Arquivos**: envio de planilhas financeiras no formato esperado.  
- **Templates Personalizados**: definição de mapeamento para diferentes tipos de arquivos.  
- **Processamento Automático**: extração e preenchimento automatizado dos dados.  

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. Colaborador realiza o upload de um arquivo no sistema.  
2. O sistema identifica o template e processa o arquivo.  
3. O colaborador acompanha o status da automação.  
4. Os dados extraídos são armazenados.  

---

## 6. Requisitos Técnicos {#requisitos-tecnicos}
- **Linguagens:** TypeScript 
- **Frameworks Frontend:** Next.js 15, React
- **Frameworks Backend:** NestJS, Sequelize ORM
- **Bibliotecas de UI:** Material UI (MUI), Emotion
- **Banco de Dados:** PostgreSQL
- **Hospedagem e Containerização:** Docker

---

📌 **Observação:** Esta documentação deverá ser mantida atualizada a cada modificação estrutural ou de integração na API para garantir a rastreabilidade e conformidade com os requisitos institucionais de segurança.
