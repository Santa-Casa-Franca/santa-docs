# Projeto de Gestão de Pacientes Oncológicos

>**Data de Emissão:** 12/08/2025

>**Autor:** Kenidy Corrêa

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
O **Projeto de Gestão de Pacientes Oncológicos** é uma plataforma digital voltada para acompanhar, monitorar e otimizar o atendimento de pacientes em tratamento contra o câncer.  
A aplicação busca facilitar a comunicação entre médicos, equipe de enfermagem e pacientes, garantindo maior segurança no tratamento e acesso rápido às informações clínicas.

### Objetivo do Software
- Proporcionar um ambiente seguro e centralizado para acompanhamento de pacientes oncológicos.  
- Melhorar a adesão ao tratamento através de notificações e lembretes personalizados.  

### Escopo
- Cadastro e atualização de dados do paciente.
- Registro de agendas, conteúdos e formulários.  
- Chat seguro entre paciente e equipe médica.  
- Painel de indicadores clínicos.  

---

## 2. Stakeholders {#stakeholders}

| Grupo                  | Responsabilidade                                                                 |
|------------------------|----------------------------------------------------------------------------------|
| **Equipe de Inovação** | Criar, manter e evoluir a aplicação seguindo requisitos técnicos e clínicos. |
| **Médicos Oncologistas**       | Alimentar o sistema com dados de conhecimentos clínicos.     |
| **Enfermeiros e Técnicos**     | Telemonitorar os sintomas, avaliando, confirmando análise clínica e acionando paciente quando necessário.   |
| **Pacientes**                  | Acessar agendamentos, registrar sintomas e interagir com a equipe hospitalar.   |
| **Administradores** | Atendimento a pacientes.      |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
A aplicação segue arquitetura **cliente-servidor**:

- **Frontend:** [Aplicativo mobile](arquitetura/app_mobile.md) para pacientes e [portal web](arquitetura/painel_admin.md) para equipe hospitalar.  
- **Backend:** API REST com autenticação JWT, integração com banco de dados e serviços de notificação.  
- **Banco de Dados:** PostgreSQL para informações da aplicação, Redis para cache.  
- **Monitoramento:** Grafana e Prometheus para métricas e logs.

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Cadastro de Pacientes**: registro com dados pessoais, diagnóstico e histórico de sintomas.
- **Agendamentos**: consultas, sessões de quimioterapia e radioterapia, equipe multi.  
- **Painel Clínico**: evolução dos sintomas e indicadores de saúde.  
- **Comunicação Segura**: chat para interação entre paciente e equipe.  
- **Notificações**: lembretes de consultas e alertas de sintomas críticos.  

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. Paciente realiza login com CPF.
2. Visualiza agendas.
3. Registra sintomas no diário.
4. Aciona equipe hospitalar via Chat.
5. Consome conteúdos referente ao contexto hospitalar.  

---

## 6. Requisitos Técnicos {#requisitos-tecnicos}
- **Linguagens:** TypeScript, Node.js, React Native, React.js
- **Frameworks:** NestJS, Next.js, Expo
- **Banco de Dados:** PostgreSQL, Redis
- **Autenticação:** JWT
- **Hospedagem:** Docker
- **Monitoramento:** Grafana, Prometheus
- **Integrações:** Integração com EMR Tasy

---
📌 **Observação:** Esta documentação deve ser atualizada a cada nova versão do projeto para garantir rastreabilidade e alinhamento com os objetivos clínicos e técnicos.
