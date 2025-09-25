# Documentação da Balança Inteligente - Web

📅 **Última atualização:** 24/09/2025

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
A aplicação web da **Balança Inteligente** foi desenvolvida para digitalizar o processo de pesagem de pacientes antes e depois das sessões. Utilizando um dispositivo IoT (balança) com comunicação via SerialPort, a interface permite identificar o paciente através de QR Code, informar o uso e o peso de equipamentos auxiliares (como cadeira de rodas ou maca), calcular o peso líquido e enviá-lo para a API Helix.

A solução tem como foco a **facilidade de uso para a equipe de enfermagem**, a **precisão dos registros clínicos** e a **integração com os sistemas assistenciais existentes**.

---

## 2. Arquitetura da Aplicação {#arquitetura-da-aplicacao}  
A aplicação segue arquitetura **cliente-dispositivo-servidor**, com comunicação direta com o hardware (balança) e envio posterior para a API.

- **Camada de Apresentação (UI):** interface web em HTML/CSS/JavaScript rodando no navegador.  
- **Camada de Comunicação:** leitura dos dados da balança via SerialPort e leitura de QR Code via leitor.  
- **Camada de Integração:** envio dos dados processados para a API Helix.  
- **Cálculo Local:** peso líquido é calculado diretamente no navegador antes do envio.

---

## 3. Tecnologias e Dependências {#tecnologias-e-dependencias}  
- **Linguagem:** JavaScript (Vanilla)  
- **Frontend:** HTML5, CSS3  
- **Leitura de QR Code:** fazendo o uso de leitor
- **Comunicação com Balança:** através de SerialPort 
- **HTTP Requests:** Fetch API para integração com backend  
---

## 4. Estrutura de Pastas {#estrutura-de-pastas}  

```
.
├── css/
│   └── styles.css                    # Estilos principais da aplicação
├── images/                           # Ícones e logos
│   ├── favicon.ico                   # Ícone do navegador
│   ├── logo-dark.png                       
│   └── new-logo-GSCF-horizontal-white.png  
├── js/
│   └── app.js                        # Script principal da interface
├── lotties/
│   ├── bye-animation.json
│   ├── checked-animation.json
│   ├── entrancy-animation.json
│   ├── error-animation.json
│   ├── scan-animation.json
│   └── warning-animation.json              
├── .gitignore                        # Arquivos/paths ignorados pelo Git
├── index.html                        # Arquivo HTML principal
├── main.js                           # Script de controle principal 
├── package-lock.json                 # Lockfile do npm
├── package.json                      # Configuração e dependências do projeto
├── preload.js                        # Script de preload 
└── renderer.js                       # Script do renderer process 


```     

---

## 5. Fluxo de Autenticação {#fluxo-de-autenticacao}  
1. O enfermeiro escaneia o QR Code do paciente usando o leitor.  
2. O sistema extrai o identificador do paciente do QR Code.  
3. Esse identificador é utilizado para autenticação via API Helix (se necessário).  
4. Não há necessidade de login por usuário (sem autenticação tradicional).

---

## 6. Integração com Backend {#integracao-com-backend}  
- **Base URL:** definida no código-fonte.  
- **Requisições HTTP:** realizadas via `fetch` com método `POST` para envio de dados de pesagem.  
- **Endpoints REST:** integração direta com endpoint específico da API Helix para registro de peso.  

---

## 7. Gerenciamento de Estado {#gerenciamento-de-estado}  
- **Sessão do Paciente:** mantida apenas durante o uso da tela atual.  
- **Persistência:** os dados não são armazenados localmente após envio.  

---

## 8. Estilo e UI {#estilo-e-ui}  
- **Design:** layout simples e focado na usabilidade por profissionais da saúde.  
- **Componentes:** campos de entrada, dropdowns de seleção e botões de ação.  
- **Acessibilidade:** botões grandes e fontes legíveis para facilitar uso em ambientes clínicos.  

---

## 9. Boas Práticas e Padronizações {#boas-praticas-e-padronizacoes}  
- Código modular separado por responsabilidades.  
- Funções documentadas via comentários internos.  
- Padrão de nomenclatura consistente.  
- Tratamento de erros com alertas visuais para o enfermeiro.  

---

## 10. Configuração e Execução {#configuracao-e-execucao}  
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub  
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/smart-balance)

---

## 📌 Observações  
- O navegador deve ter suporte à API Serial para leitura direta da balança.  
- A balança deve estar conectada e configurada na porta correta antes de iniciar a pesagem.  

