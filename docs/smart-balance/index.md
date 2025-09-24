# Projeto Balança Inteligente

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
A **Balança Inteligente** é um sistema desenvolvido para auxiliar o processo de pesagem de pacientes no sistema. Utilizando um dispositivo IoT (balança) conectado a um sistema web, permite a identificação do paciente via QR Code, além de considerar equipamentos auxiliares (como cadeiras de rodas ou macas) para garantir a precisão do peso líquido do paciente.

### Objetivo do Software  
- Automatizar o processo de pesagem de pacientes no setor de hemodiálise e no Hospital do Câncer.  
- Garantir a precisão do peso líquido, considerando o uso de equipamentos auxiliares.  
- Registrar os dados diretamente na API Helix, promovendo integração com o ecossistema hospitalar.  
- Oferecer uma interface web simples, intuitiva e de fácil uso pela equipe de enfermagem.

### Escopo  
- Leitura de QR Code para identificação do paciente.  
- Seleção do tipo de equipamento auxiliar (se houver) e entrada do peso correspondente.  
- Cálculo automático e exibição do peso líquido do paciente.  
- Registro dos dados na API Helix.

---

## 2. Stakeholders {#stakeholders}  

| Grupo                      | Responsabilidade                                                                 |
|----------------------------|----------------------------------------------------------------------------------|
| **Equipe de Enfermagem**  | Operação do sistema no momento da pesagem, entrada de dados e validação.        |
| **Equipe de Inovação**    | Desenvolvimento, manutenção e atualização do sistema da balança.     |
| **TI Hospitalar**         | Suporte técnico e infraestrutura.                      |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}  
O sistema utiliza uma arquitetura baseada em dispositivos IoT conectado a uma interface web desenvolvida em HTML, CSS e JavaScript. O dispositivo (balança) transmite os dados via SerialPort, e a interface permite entrada manual de informações complementares.

- **Frontend:** WebApp em HTML/CSS/JavaScript rodando em navegador.  
- **Dispositivo IoT:** Balança eletrônica com comunicação via SerialPort. 
- **Interface do Usuário:** Intuitiva e de fácil utilização.  
- **Armazenamento:** Registro dos dados na API Helix.

---

## 4. Funcionalidades Principais {#funcionalidades-principais}  
- **Leitura de QR Code:** escaneamento da pulseira do paciente para identificação.  
- **Seleção de Equipamento Auxiliar:** cadeira de rodas, maca, etc.  
- **Entrada do Peso do Equipamento:** valor fixo informado manualmente.  
- **Cálculo do Peso Líquido:** subtração automática do peso do equipamento.  
- **Exibição em Tela:** visualização do peso final do paciente de forma destacada.  
- **Registro de Dados:** envio dos dados coletados e calculados para a API Helix.

---

## 5. Fluxo de Uso {#fluxo-de-uso}  
1. Enfermeiro acessa a interface web pelo navegador.  
2. Paciente sobe na balança.  
3. Enfermeiro escaneia o QR Code do paciente via leitor.  
4. Sistema identifica o paciente.  
5. Enfermeiro informa se o paciente está com equipamento auxiliar.  
6. Caso sim, seleciona o tipo e informa o peso do equipamento. 
7. O sistema calcula o peso líquido (peso bruto - peso do equipamento).  
8. Exibe o peso final do paciente na tela.  
9. Registra os dados automaticamente na API Helix.

---

## 6. Requisitos Técnicos {#requisitos-técnicos}  
- **Linguagem (Frontend):** HTML, CSS, JavaScript 
- **Dispositivo IoT:** Balança eletrônica com comunicação via porta serial 
- **Comunicação:** via SerialPort para leitura do peso bruto  
- **Requisitos de Hardware:**  
    - PC com navegador moderno 
    - Leitor de QR Code 
    - Porta serial disponível para conexão com a balança  
- **Integração Backend:** API Helix para persistência dos dados

---

📌 **Observação:** Esta documentação deverá ser atualizada sempre que houver alterações na integração com a balança, na interface de usuário ou nos requisitos de coleta de dados.

