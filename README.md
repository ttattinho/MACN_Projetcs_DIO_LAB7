# MACN_Projetcs_DIO_LAB7

# 🚗 Rent a Car - Arquitetura da Aplicação no Azure

## ☁️ Visão Geral

A aplicação Rent a Car é uma solução moderna, escalável e desacoplada, desenvolvida para nuvem Azure. Utiliza Node.js 18 no frontend e backend (BFF/API), hospedados como Container Apps, além de Azure Service Bus, Azure Functions (.NET), Azure Database for SQL Server e Logic Apps. O ambiente é seguro, resiliente e totalmente observável.

## 🧱 Componentes da Solução

### 🐳 Azure Container Registry (ACR)

Armazena as imagens Docker do frontend (Node.js 18) e backend (Node.js 18).

### 🌐 Frontend (Azure Container App - Node.js 18)

*   Interface Web acessível via HTTP com DNS público.
*   Responsável por interações diretas com o usuário.

### ⚙️ Backend / BFF (Azure Container App - Node.js 18)

*   Implementado como API gateway/BFF (Backend For Frontend) usando Express, CORS, dotenv, @azure/identity, @azure/service-bus.
*   Realiza autenticação, gerenciamento das requisições e orquestração entre frontend e demais serviços.
*   Publica mensagens na fila `rentprocess` do Azure Service Bus.

### 🔁 Processamento Assíncrono

📥 Fila: `rentprocess` (Azure Service Bus)

*   Alimentada pelo backend/BFF com solicitações de locação.

⚙️ Azure Function: `rentprocess` (.NET)

*   Consome mensagens da fila `rentprocess`.
*   Persiste dados da locação no **Azure Database for SQL Server**.
*   Publica nova mensagem na fila `paymentqueue` para processamento de pagamento.

💳 Fila: `paymentqueue` (Azure Service Bus)

*   Contém dados para processamento de pagamento.

💰 Azure Function: `paymentprocess` (.NET)

*   Consome mensagens da `paymentqueue`.
*   Persiste status do pagamento no **Cosmos DB**.
*   Aciona a Logic App para envio de e-mail de notificação ao cliente.

### ✉️ Notificação ao Cliente

📬 Azure Logic App

*   Acionada pela função `paymentprocess`.
*   Envia e-mails ao cliente com o resultado da transação.

## 🔐 Segurança e Monitoramento

### 🔒 Azure Key Vault

*   Gerenciamento centralizado de secrets e credenciais sensíveis.

### 📊 Application Insights

*   Telemetria, métricas e diagnósticos em tempo real para Container Apps e Functions.

### 📈 Azure Monitor / Log Analytics

*   Consolidação de logs, alertas e auditoria centralizada.

## 📡 Arquitetura Visual (texto descritivo)

1.  **Usuário** acessa o frontend (Web App em Node.js 18, no Container App).
2.  **Frontend** comunica-se com o **Backend BFF/API** (Node.js 18, no Container App).
3.  O **Backend/BFF** publica eventos na **fila **`rentprocess` (Service Bus).
4.  **Azure Function (**`rentprocess`) consome a fila, salva no **Azure Database for SQL Server** e publica na **fila **`paymentqueue`.
5.  **Azure Function (**`paymentprocess`) consome a fila, grava resultados no **Cosmos DB** e dispara a **Logic App**.
6.  **Logic App** envia notificação por e-mail ao cliente.
7.  **Segurança**, **telemetria** e **logs** garantidos por Key Vault, Application Insights e Azure Monitor.

## ✅ Benefícios da Arquitetura

*   🧩 **Baixo acoplamento** entre os componentes com uso intensivo do Azure Service Bus.
*   🔄 **Escalabilidade automática** para APIs e processamento assíncrono com Container Apps e Functions.
*   🔐 **Segurança reforçada** utilizando Azure Key Vault para gestão de segredos.
*   📬 **Comunicação automatizada** e flexível via Logic Apps.
*   👁️ **Observabilidade total** com Application Insights e Azure Monitor.

## 🚀 Stack Tecnológica

*   **Frontend:** Node.js 18 (em Container App)
*   **Backend/BFF/API:** Node.js 18 com Express, CORS, dotenv, @azure/identity, @azure/service-bus (em Container App)
*   **Azure Functions:** .NET
*   **Mensageria:** Azure Service Bus
*   **Banco de Dados:** Azure Database for SQL Server (locações), Cosmos DB (pagamentos/status)
*   **Notificação:** Azure Logic Apps
*   **Segurança:** Azure Key Vault
*   **Monitoramento:** Application Insights, Azure Monitor/Log Analytics

