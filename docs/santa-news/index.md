# 📄 Projeto Santa News

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
O **Projeto Santa News** é uma plataforma web voltada aos colaboradores do hospital, com foco na **disseminação de conteúdo institucional**, **exibição de vídeos de capacitação**, e **integração com a API de Treinamento**.
A solução foi desenvolvida com **Next.js**, garantindo alta performance, SSR e escalabilidade. O objetivo é aproximar o colaborador da cultura institucional e promover treinamentos contínuos por meio de uma interface amigável, responsiva e segura.

### Objetivo do Software
- Prover uma interface moderna para exibição de conteúdos internos.  
- Oferecer acesso a vídeos, manuais e comunicados institucionais.  
- Facilitar a consulta e o consumo de treinamentos disponíveis na **API de Treinamento**.

### Escopo
- Exibição de notícias publicadas pela área de comunicação.  
- Integração com API externa para consulta de vídeos e treinamentos.  
- Interface moderna e acessível para dispositivos desktop e mobile.  
- Recursos visuais com carrosséis, animações e vídeos embutidos.  

---

## 2. Stakeholders {#stakeholders}

| Grupo                        | Responsabilidade                                                             |
|------------------------------|------------------------------------------------------------------------------|
| **Área de Inovação**         | Desenvolvimento, manutenção e deploy da interface.                          |
| **Departamento Pessoal**     | Cadastro e curadoria dos conteúdos de treinamento e capacitação.            |
| **Colaboradores do Hospital**| Usuários finais: consumo dos conteúdos, notícias e vídeos da plataforma.    |
| **Comunicação Interna**      | Publicação de comunicados, avisos e conteúdos institucionais.               |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
A aplicação adota uma arquitetura baseada em **Next.js** com **React**, seguindo os princípios de componentização e modularidade.

- **Frontend:** aplicação desenvolvida com **Next.js (v13+)**.
- **Estilização**: UI construída com **TailwindCSS**, **Radix UI** e componentes personalizados.  
- **Integração:**  comunicação com a **API de Treinamento** para listagem de vídeos e metadados.  

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Página Inicial:**  destaques, vídeos e notícias.
- **Visualização de Notícias:**  layout responsivo para leitura de comunicados, com suporte a imagens e links embutidos.

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. Colaborador acessa o portal via navegador.  
2. Visualiza a área principal com notícias e destaques.  
3. Escolhe um vídeo ou notícia, consome diretamente na interface.  
4. Recebe alertas ou confirmações ao completar ou iniciar um conteúdo.

---

## 6. Requisitos Técnicos {#requisitos-técnicos}
- **Linguagens:** TypeScript, JavaScript  
- **Framework:** Next.js (v13+), React  
- **Bibliotecas de UI:** TailwindCSS, Radix UI, clsx, lucide-react  
- **Gerenciamento de Estado:** React Query  
- **Validação de Dados:** Zod  
- **Formulários:** React Hook Form  
- **Internacionalização:** next-intl  
- **Manipulação de datas:** date-fns  
- **Player de Vídeo:** video.js, videojs-contrib-quality-levels  

---
📌 **Observação:** Esta documentação deve ser atualizada a cada nova versão do projeto para garantir rastreabilidade e alinhamento com os objetivos clínicos e técnicos.