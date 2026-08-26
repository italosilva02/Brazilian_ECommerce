# 🛒 Brazilian E-Commerce — Power BI Analytics Report

<p align="center">
  <img 
    src="./images/dashboard-preview.png" 
    alt="Brazilian E-Commerce Power BI Dashboard"
    width="900"
  >
</p>

<p align="center">
  <a href="https://app.powerbi.com/view?r=eyJrIjoiYmZhZDQyYjQtYTNkNC00OTkzLThiZWMtNGI4YmFhMWMyNjA5IiwidCI6IjkwN2IxZDhiLTE2ZTEtNDZiZi05ODczLTI3MjNmNTlmODcwYSJ9">
    <img 
      src="https://img.shields.io/badge/Power%20BI-Abrir%20Dashboard-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"
      alt="Abrir Dashboard no Power BI"
    >
  </a>
</p>

<p align="center">
  <strong>Dashboard interativo desenvolvido em Power BI.</strong><br>
  Clique no botão acima para explorar o relatório completo.
</p>

---

## 📊 Dashboard Interativo

O relatório apresenta uma análise completa das operações de e-commerce brasileiro, explorando indicadores de vendas, logística, satisfação dos clientes, distribuição geográfica e comportamento de pagamento.

<p align="center">

<a href="https://app.powerbi.com/view?r=eyJrIjoiYmZhZDQyYjQtYTNkNC00OTkzLThiZWMtNGI4YmFhMWMyNjA5IiwidCI6IjkwN2IxZDhiLTE2ZTEtNDZiZi05ODczLTI3MjNmNTlmODcwYSJ9">

<img 
  src="./images/dashboard-preview.png"
  alt="Preview do Dashboard Brazilian E-Commerce"
  width="100%"
>

</a>

</p>

<p align="center">

### 👆 Clique na imagem para abrir o dashboard interativo

<a href="https://app.powerbi.com/view?r=eyJrIjoiYmZhZDQyYjQtYTNkNC00OTkzLThiZWMtNGI4YmFhMWMyNjA5IiwidCI6IjkwN2IxZDhiLTE2ZTEtNDZiZi05ODczLTI3MjNmNTlmODcwYSJ9">

<img src="https://img.shields.io/badge/Power%20BI-Explorar%20Relatório%20Interativo-F2C811?style=for-the-badge&logo=powerbi&logoColor=black">

</a>

</p>

---

## 📋 Visão Geral

Este projeto apresenta uma análise completa das operações de um marketplace brasileiro de e-commerce utilizando o dataset público da Olist.

O objetivo do relatório é transformar dados operacionais em informações que auxiliem na análise de:

- 💰 Volume de vendas e receita
- 📦 Performance logística
- 🚚 Prazo de entrega
- ⭐ Satisfação dos clientes
- 🗺️ Distribuição geográfica
- 💳 Comportamento de pagamento
- 🛍️ Performance de produtos e vendedores

**Período dos dados:** 2016 – 2018

**Ferramenta:** Power BI Desktop

**Modo de armazenamento:** Import

**Dataset:** Brazilian E-Commerce Public Dataset by Olist

---

## 🗂️ Dataset de Origem

Os dados utilizados neste projeto são provenientes do dataset público da Olist, disponibilizado no Kaggle.

O conjunto de dados contém informações sobre:

- Clientes
- Pedidos
- Produtos
- Vendedores
- Pagamentos
- Avaliações
- Geolocalização

O dataset permite acompanhar todo o ciclo de um pedido, desde a compra até a entrega e avaliação do cliente.

<p align="center">

<a href="https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce">

<img src="https://img.shields.io/badge/Kaggle-Acessar%20Dataset-20BEFF?style=for-the-badge&logo=kaggle&logoColor=white">

</a>

</p>

---

## 🏗️ Arquitetura do Modelo Semântico

O modelo foi estruturado utilizando conceitos de **Star Schema** e **Snowflake Schema**.

A tabela central do modelo é:

```text
olist_order_items_dataset
