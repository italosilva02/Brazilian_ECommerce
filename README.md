# 🛒 Brazilian E-Commerce Analytics | Power BI

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-00599C?style=for-the-badge&logo=microsoft&logoColor=white)
![Kaggle](https://img.shields.io/badge/Kaggle-20BEFF?style=for-the-badge&logo=Kaggle&logoColor=white)
![Status](https://img.shields.io/badge/Status-Concluído-success?style=for-the-badge)

> Análise ponta a ponta das operações de um marketplace de e-commerce brasileiro. O projeto transforma dados transacionais brutos em inteligência de negócios, avaliando performance logística, satisfação do cliente e volume de vendas.

---

## 📊 Acesse o Dashboard Interativo

Para interagir com os filtros, navegar pelas páginas e explorar os dados dinamicamente, acesse a versão publicada no Power BI Web:

[![Acessar Relatório Power BI](https://img.shields.io/badge/Visualizar_Dashboard_Interativo-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://app.powerbi.com/view?r=eyJrIjoiYmZhZDQyYjQtYTNkNC00OTkzLThiZWMtNGI4YmFhMWMyNjA5IiwidCI6IjkwN2IxZDhiLTE2ZTEtNDZiZi05ODczLTI3MjNmNTlmODcwYSJ9)

*(Link direto: [https://app.powerbi.com/view...](https://app.powerbi.com/view?r=eyJrIjoiYmZhZDQyYjQtYTNkNC00OTkzLThiZWMtNGI4YmFhMWMyNjA5IiwidCI6IjkwN2IxZDhiLTE2ZTEtNDZiZi05ODczLTI3MjNmNTlmODcwYSJ9))*

---

## 💡 Principais Insights Analíticos

A análise dos dados (2016–2018) revelou oportunidades críticas de otimização operacional e de mercado:

*   📍 **Concentração Geográfica Extrema:** O estado de São Paulo responde sozinho por **42% dos pedidos**. O eixo SP-RJ-MG concentra **66,6%** do volume total, revelando um enorme potencial de expansão (e necessidade de reestruturação logística) para as regiões Norte, Nordeste e Centro-Oeste.
*   🚚 **Gargalos de Entrega vs. Expectativa:** Embora **95%** das entregas cumpram o prazo estimado pelo site, o tempo médio de entrega real é de **12,56 dias**. Em um mercado competitivo onde entregas ocorrem em 3-5 dias, há um risco latente de churn.
*   ⭐ **Polarização na Satisfação do Cliente:** A nota média é positiva (4,09/5), mas **11,5%** das avaliações são extremas (Nota 1). A discrepância indica que a experiência do usuário costuma ser excelente, exceto quando ocorrem falhas graves (geralmente logísticas).
*   💳 **Comportamento de Pagamento:** Cartão de crédito domina absoluto (**75,7%**), com ticket médio superior. Contudo, o boleto bancário (19,5%) adiciona um "delay" natural de ~1,4 dias no processamento, impactando a percepção final do prazo de entrega.
*   📦 **Impacto do Frete:** O frete representa cerca de **16,6%** da receita total. Para produtos de baixo ticket, esse custo proporcional é um provável ofensor de taxas de conversão.

---

## 📈 Resumo de KPIs

| Métrica | Valor | Métrica | Valor |
|---|---|---|---|
| 📦 **Total de Pedidos** | 99.441 | 💰 **Receita Produtos** | R$ 1.359.164.370 |
| 👥 **Clientes Únicos** | 96.096 | 🚚 **Receita de Frete** | R$ 225.190.954 |
| 🏪 **Total de Vendedores**| 3.095 | 💳 **Ticket Médio** | R$ 13.668,05 |
| 🛒 **Itens Vendidos** | 134.936 | ⭐ **Nota Média (CSAT)** | 4,09 / 5,00 |

---

## ⚙️ Arquitetura do Modelo Semântico

O projeto foi modelado no Power BI utilizando uma abordagem mista de **Snowflake Schema** e **Star Schema**, garantindo a performance da ferramenta no modo *Import* sem sobrecarregar a memória. A modelagem foi centrada na tabela fato `olist_order_items_dataset`.

```mermaid
graph TD
    C[Customers] -->|1:N| O(Orders)
    O -->|1:N| P[Payments]
    O -->|1:N| R[Reviews]
    O -->|1:N| I(Order Items - FACT)
    S[Sellers] -->|1:N| I
    PR[Products] -->|1:N| I
    T[Translation] -->|1:N| PR
    G[Geolocation] -.-> |Desconectada| C
