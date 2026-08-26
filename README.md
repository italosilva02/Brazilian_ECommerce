# 🛒 Brazilian E-Commerce — Power BI Analytics Report

> Análise completa de operações de e-commerce brasileiro baseada no dataset público da Olist (Kaggle), modelada e visualizada em Power BI Desktop.

---

## 📋 Índice

- [Visão Geral](#visão-geral)
- [Dataset de Origem](#dataset-de-origem)
- [Arquitetura do Modelo Semântico](#arquitetura-do-modelo-semântico)
- [Tabelas e Colunas](#tabelas-e-colunas)
- [Relacionamentos](#relacionamentos)
- [Medidas DAX](#medidas-dax)
- [KPIs e Valores Extraídos](#kpis-e-valores-extraídos)
- [Insights Analíticos](#insights-analíticos)
- [Problemas Identificados no Modelo](#problemas-identificados-no-modelo)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Como Abrir o Relatório](#como-abrir-o-relatório)
- [Estrutura de Arquivos](#estrutura-de-arquivos)

---

## Visão Geral

Este relatório analisa o desempenho operacional de um marketplace de e-commerce brasileiro com dados reais de pedidos, clientes, vendedores, produtos, pagamentos e avaliações. O objetivo é fornecer visibilidade sobre:

- Volume de vendas e receita
- Performance logística (prazos de entrega)
- Satisfação do cliente (avaliações)
- Distribuição geográfica
- Comportamento de pagamento

**Período dos dados:** 2016 – 2018  
**Fonte:** [Olist Store Dataset — Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)  
**Ferramenta:** Power BI Desktop  
**Modo de armazenamento:** Import (todas as tabelas)

---

## Dataset de Origem

O dataset é composto por informações comerciais anonimizadas de pedidos realizados na plataforma Olist, o maior marketplace do Brasil. Os dados cobrem o ciclo completo de um pedido: da compra à entrega, incluindo pagamento e avaliação do cliente.

```
Kaggle Dataset: olistbr/brazilian-ecommerce
Licença: CC BY-NC-SA 4.0
```

---

## Arquitetura do Modelo Semântico

O modelo segue uma estrutura **Snowflake Schema** com elementos de **Star Schema**, centrado na tabela de fatos `olist_order_items_dataset`.

```
olist_customers_dataset
        │
        │ (N:1 via customer_id)
        ▼
olist_orders_dataset ◄────────── olist_order_items_dataset ──► olist_products_dataset
        │                                  │                           │
        │ (1:N)                            │ (N:1 via seller_id)       │ (N:1)
        ▼                                  ▼                           ▼
olist_order_payments_dataset    olist_sellers_dataset    product_category_name_translation
olist_order_reviews_dataset

olist_geolocation_dataset  ← DESCONECTADA (sem relacionamento ativo)
```

### Tabela Fato Principal
`olist_order_items_dataset` — granularidade no nível do item de pedido.

### Tabelas Dimensão
- `olist_customers_dataset` — clientes
- `olist_products_dataset` — produtos
- `olist_sellers_dataset` — vendedores
- `product_category_name_translation` — lookup de categorias (PT → EN)

### Tabelas Fato Secundárias
- `olist_orders_dataset` — cabeçalho do pedido (status, datas, prazos)
- `olist_order_payments_dataset` — pagamentos por pedido
- `olist_order_reviews_dataset` — avaliações por pedido

### Tabelas de Data Automáticas (ocultas)
Power BI gera automaticamente 8 `LocalDateTable` (uma por coluna DateTime nas tabelas de negócio) e 1 `DateTableTemplate`.

---

## Tabelas e Colunas

### `olist_customers_dataset`
Dimensão de clientes. Cada linha representa um par único `customer_id × pedido`.

| Coluna | Tipo | Descrição |
|---|---|---|
| `customer_id` | String | Chave que relaciona com pedidos (não é o ID único do cliente) |
| `customer_unique_id` | String | Identificador único real do cliente |
| `customer_zip_code_prefix` | Int64 | CEP (5 dígitos) |
| `customer_city` | String | Cidade do cliente |
| `customer_state` | String | Estado (UF) do cliente |

---

### `olist_orders_dataset`
Cabeçalho de pedidos. Contém status, datas e colunas calculadas de prazo.

| Coluna | Tipo | Descrição |
|---|---|---|
| `order_id` | String | Identificador único do pedido |
| `customer_id` | String | FK para `olist_customers_dataset` |
| `order_status` | String | Status: `delivered`, `shipped`, `canceled`, `invoiced`, `processing`, `created`, `approved`, `unavailable` |
| `order_purchase_timestamp` | DateTime | Data/hora da compra |
| `order_approved_at` | DateTime | Data/hora de aprovação do pagamento |
| `order_delivered_carrier_date` | DateTime | Data de entrega à transportadora |
| `order_delivered_customer_date` | DateTime | Data de entrega ao cliente |
| `order_estimated_delivery_date` | DateTime | Prazo estimado informado ao cliente |
| `order_delevery_date_finish` | Double | Dias entre compra e conclusão da entrega *(coluna calculada — typo: delevery)* |
| `diff_recive_date` | Double | Dias entre aprovação e recebimento *(coluna calculada — typo: recive)* |

---

### `olist_order_items_dataset`
Tabela fato principal. Granularidade: item × pedido.

| Coluna | Tipo | Descrição |
|---|---|---|
| `order_id` | String | FK para `olist_orders_dataset` |
| `order_item_id` | Int64 | Número sequencial do item no pedido |
| `product_id` | String | FK para `olist_products_dataset` |
| `seller_id` | String | FK para `olist_sellers_dataset` |
| `shipping_limit_date` | DateTime | Data limite de envio pelo vendedor |
| `price` | Int64 | Preço unitário do item (sem frete) |
| `freight_value` | Int64 | Valor do frete do item |
| `Ano` | String | Ano da venda *(coluna calculada redundante)* |
| `Mês` | String | Mês da venda *(coluna calculada redundante)* |
| `Nome do Mês` | String | Nome do mês *(coluna calculada redundante)* |
| `Semana` | String | Semana *(coluna calculada redundante)* |
| `Semana do Mês` | String | Semana do mês *(coluna calculada redundante)* |
| `Semana do Mês.1` | Int64 | Semana do mês numérica *(coluna calculada redundante)* |

> ⚠️ As 6 colunas marcadas como redundantes duplicam informações já disponíveis nas `LocalDateTable` automáticas.

---

### `olist_order_payments_dataset`
Pagamentos por pedido. Um pedido pode ter múltiplos registros (parcelamento ou múltiplos meios).

| Coluna | Tipo | Descrição |
|---|---|---|
| `order_id` | String | FK para `olist_orders_dataset` |
| `payment_sequential` | Int64 | Sequencial quando há múltiplos pagamentos |
| `payment_type` | String | Tipo: `credit_card`, `boleto`, `voucher`, `debit_card`, `not_defined` |
| `payment_installments` | Int64 | Número de parcelas |
| `payment_value` | Int64 | Valor pago nesta linha |

---

### `olist_order_reviews_dataset`
Avaliações dos clientes após a entrega.

| Coluna | Tipo | Descrição |
|---|---|---|
| `review_id` | String | Identificador único da avaliação |
| `order_id` | String | FK para `olist_orders_dataset` |
| `review_score` | Int64 | Nota de 1 a 5 |
| `review_comment_title` | String | Título do comentário (opcional) |
| `review_comment_message` | String | Mensagem completa (opcional) |
| `review_creation_date` | DateTime | Data de criação da avaliação |
| `review_answer_timestamp` | DateTime | Data de resposta do vendedor |

---

### `olist_products_dataset`
Catálogo de produtos.

| Coluna | Tipo | Descrição |
|---|---|---|
| `product_id` | String | Identificador único do produto |
| `product_category_name` | String | Categoria em português |
| `product_name_lenght` | Int64 | Tamanho do nome em caracteres *(typo: lenght)* |
| `product_description_lenght` | Int64 | Tamanho da descrição em caracteres *(typo: lenght)* |
| `product_photos_qty` | Int64 | Quantidade de fotos |
| `product_weight_g` | Int64 | Peso em gramas |
| `product_length_cm` | Int64 | Comprimento em cm |
| `product_height_cm` | Int64 | Altura em cm |
| `product_width_cm` | Int64 | Largura em cm |

---

### `olist_sellers_dataset`
Vendedores cadastrados na plataforma.

| Coluna | Tipo | Descrição |
|---|---|---|
| `seller_id` | String | Identificador único do vendedor |
| `seller_zip_code_prefix` | Int64 | CEP (5 dígitos) |
| `seller_city` | String | Cidade do vendedor |
| `seller_state` | String | Estado (UF) do vendedor |

---

### `olist_geolocation_dataset`
Dados de geolocalização por CEP.

| Coluna | Tipo | Descrição |
|---|---|---|
| `geolocation_zip_code_prefix` | Int64 | CEP (5 dígitos) |
| `geolocation_lat` | Int64 | Latitude bruta |
| `geolocation_lng` | Int64 | Longitude bruta |
| `geolocation_city` | String | Cidade |
| `geolocation_state` | String | Estado |
| `Latitude_Corrigida` | Double | Latitude após tratamento |
| `Corrigido` | Double | Longitude após tratamento *(nome genérico)* |

> ⚠️ Tabela **sem relacionamento ativo** com o restante do modelo. Dados de geolocalização não participam de nenhuma análise cruzada.

---

### `product_category_name_translation`
Lookup de tradução de categorias de produtos.

| Coluna | Tipo | Descrição |
|---|---|---|
| `product_category_name` | String | Nome da categoria em português |
| `product_category_name_english` | String | Nome da categoria em inglês |

---

## Relacionamentos

### Relacionamentos de Negócio (7)

| De | Coluna | Para | Coluna | Cardinalidade | Filtro | Observação |
|---|---|---|---|---|---|---|
| `olist_orders_dataset` | `customer_id` | `olist_customers_dataset` | `customer_id` | **1:1** | BothDirections | ⚠️ Cardinalidade incorreta — deveria ser N:1 |
| `olist_order_items_dataset` | `order_id` | `olist_orders_dataset` | `order_id` | Many:1 | OneDirection | ✅ |
| `olist_order_payments_dataset` | `order_id` | `olist_orders_dataset` | `order_id` | Many:1 | OneDirection | ✅ |
| `olist_order_reviews_dataset` | `order_id` | `olist_orders_dataset` | `order_id` | Many:1 | OneDirection | ✅ |
| `olist_order_items_dataset` | `product_id` | `olist_products_dataset` | `product_id` | Many:1 | OneDirection | ✅ |
| `olist_order_items_dataset` | `seller_id` | `olist_sellers_dataset` | `seller_id` | Many:1 | OneDirection | ✅ |
| `olist_products_dataset` | `product_category_name` | `product_category_name_translation` | `product_category_name_english` | Many:1 | OneDirection | ✅ |

### Relacionamentos de Datas Automáticas (8)

Power BI gera automaticamente `LocalDateTable` para cada coluna DateTime:

| Tabela | Coluna DateTime | LocalDateTable |
|---|---|---|
| `olist_orders_dataset` | `order_purchase_timestamp` | LocalDateTable_ebf425ca |
| `olist_orders_dataset` | `order_approved_at` | LocalDateTable_de5094cb |
| `olist_orders_dataset` | `order_delivered_carrier_date` | LocalDateTable_ef187bd1 |
| `olist_orders_dataset` | `order_delivered_customer_date` | LocalDateTable_7477ff3c |
| `olist_orders_dataset` | `order_estimated_delivery_date` | LocalDateTable_17cc7a97 |
| `olist_order_items_dataset` | `shipping_limit_date` | LocalDateTable_0c7e7939 |
| `olist_order_reviews_dataset` | `review_creation_date` | LocalDateTable_7cbbe099 |
| `olist_order_reviews_dataset` | `review_answer_timestamp` | LocalDateTable_70f6868f |

---

## Medidas DAX

Todas as medidas estão na tabela `Measure`. O modelo contém 8 medidas, todas criadas via Quick Measures do Power BI.

---

### `avg_diff_reciving`
**Propósito:** Média de dias entre a aprovação do pedido e o recebimento pelo cliente.

```dax
avg_diff_reciving =
AVERAGE(olist_orders_dataset[diff_recive_date])
```

| Atributo | Valor |
|---|---|
| Resultado global | **11,18 dias** |
| Formato | Número geral |
| Tabela | Measure |

> ⚠️ Typo no nome: `reciving` → `receiving`. Sem proteção contra valores nulos.

---

### `avf_date_finish`
**Propósito:** Média de dias entre a compra e a conclusão da entrega.

```dax
avf_date_finish =
AVERAGE(olist_orders_dataset[order_delevery_date_finish])
```

| Atributo | Valor |
|---|---|
| Resultado global | **12,56 dias** |
| Formato | Número geral |
| Tabela | Measure |

> ⚠️ Typo duplo: `avf` (avg) e `delevery` (delivery). Sem proteção contra valores nulos.

---

### `meam_avg`
**Propósito:** Média aritmética entre o prazo de entrega (`avf_date_finish`) e o prazo de recebimento (`avg_diff_reciving`).

```dax
meam_avg =
([avf_date_finish] + [avg_diff_reciving]) / 2
```

| Atributo | Valor |
|---|---|
| Resultado global | **11,87 dias** |
| Formato | Número geral |
| Tabela | Measure |

> ⚠️ Typo: `meam` → `mean`. Depende de duas medidas com nomes incorretos.

---

### `target_score`
**Propósito:** Meta de nota de avaliação dos pedidos. Constante fixa.

```dax
target_score = 5
```

| Atributo | Valor |
|---|---|
| Resultado | **5** |
| Formato | `0` |
| Tabela | Measure |

> ⚠️ Valor hardcoded. Não reage a filtros nem é configurável. Ideal: parâmetro de What-If.

---

### `Média de diff_recive_date YTD`
**Propósito:** Acumulado no ano (Year-To-Date) do prazo médio de recebimento, usando `order_approved_at` como eixo de tempo.

```dax
Média de diff_recive_date YTD =
IF(
    ISFILTERED('olist_orders_dataset'[order_approved_at]),
    ERROR("Medidas rápidas de inteligência de tempo somente podem ser agrupadas ou
           filtradas pela hierarquia de data fornecida pelo Power BI ou pela
           coluna de data primária."),
    TOTALYTD(
        AVERAGE('olist_orders_dataset'[diff_recive_date]),
        'olist_orders_dataset'[order_approved_at].[Date]
    )
)
```

| Atributo | Valor |
|---|---|
| Formato | Número geral |
| Tabela | Measure |
| Template | Quick Measure — YearToDate |

> ⚠️ Padrão `ISFILTERED + ERROR` bloqueia filtros diretos sobre a coluna de data, retornando erro em vez de valor. Uso de `.[Date]` é frágil e depende de hierarquia implícita.

---

### `Média de diff_recive_date MTD`
**Propósito:** Acumulado no mês (Month-To-Date) do prazo médio de recebimento.

```dax
Média de diff_recive_date MTD =
IF(
    ISFILTERED('olist_orders_dataset'[order_approved_at]),
    ERROR("..."),
    TOTALMTD(
        AVERAGE('olist_orders_dataset'[diff_recive_date]),
        'olist_orders_dataset'[order_approved_at].[Date]
    )
)
```

| Atributo | Valor |
|---|---|
| Formato | Número geral |
| Template | Quick Measure — MonthToDate |

> ⚠️ Mesmos problemas do YTD acima.

---

### `Média de order_delevery_date_finish MTD`
**Propósito:** MTD da média de dias até a conclusão da entrega.

```dax
Média de order_delevery_date_finish MTD =
IF(
    ISFILTERED('olist_orders_dataset'[order_approved_at]),
    ERROR("..."),
    TOTALMTD(
        AVERAGE('olist_orders_dataset'[order_delevery_date_finish]),
        'olist_orders_dataset'[order_approved_at].[Date]
    )
)
```

| Atributo | Valor |
|---|---|
| Formato | Número geral |
| Template | Quick Measure — MonthToDate |

> ⚠️ Typo `delevery` herdado. Mesmos problemas de ISFILTERED.

---

### `Média de review_score MTD`
**Propósito:** MTD da nota média de avaliação dos pedidos, usando `review_answer_timestamp` como eixo de tempo.

```dax
Média de review_score MTD =
IF(
    ISFILTERED('olist_order_reviews_dataset'[review_answer_timestamp]),
    ERROR("..."),
    TOTALMTD(
        AVERAGE('olist_order_reviews_dataset'[review_score]),
        'olist_order_reviews_dataset'[review_answer_timestamp].[Date]
    )
)
```

| Atributo | Valor |
|---|---|
| Formato | `#,0.00` |
| Template | Quick Measure — MonthToDate |

> ⚠️ Usa `review_answer_timestamp` como eixo de tempo. Semanticamente mais correto seria `review_creation_date`. Mesmo padrão frágil ISFILTERED.

---

## KPIs e Valores Extraídos

Todos os valores abaixo foram calculados via DAX diretamente do modelo em produção.

### Visão Geral do Negócio

| KPI | Valor |
|---|---|
| Total de Pedidos | **99.441** |
| Total de Clientes Únicos | **96.096** |
| Total de Vendedores | **3.095** |
| Total de Produtos | **32.951** |
| Total de Itens Vendidos | **134.936** |
| Receita Total (produtos) | **R$ 1.359.164.370** |
| Receita de Frete | **R$ 225.190.954** |
| Receita Total + Frete | **R$ 1.584.355.324** |
| Ticket Médio por Pedido | **R$ 13.668,05** |
| % Frete sobre Receita Total | **~16,6%** |

---

### Logística e Prazos

| KPI | Valor |
|---|---|
| Pedidos com status `delivered` | **96.478** (97,0%) |
| Pedidos entregues dentro do prazo | **91.614** (95,0% dos entregues) |
| Prazo Médio de Recebimento (`diff_recive_date`) | **11,18 dias** |
| Prazo Médio de Entrega (`order_delevery_date_finish`) | **12,56 dias** |
| Prazo Médio Combinado | **11,87 dias** |

**Distribuição de status dos pedidos:**

| Status | Quantidade | % |
|---|---|---|
| `delivered` | 96.478 | 97,0% |
| `shipped` | 1.107 | 1,1% |
| `canceled` | 625 | 0,6% |
| `unavailable` | 609 | 0,6% |
| `invoiced` | 314 | 0,3% |
| `processing` | 301 | 0,3% |
| `created` | 5 | < 0,1% |
| `approved` | 2 | < 0,1% |
| **Total** | **99.441** | **100%** |

---

### Avaliações dos Clientes

| KPI | Valor |
|---|---|
| Total de Avaliações | **99.224** |
| Nota Média Geral | **4,09 / 5** |
| Meta de Avaliação | **5** |
| % Avaliações com nota 5 | **57,8%** |
| % Avaliações com nota 4 | **19,3%** |
| % Avaliações com nota 3 | **8,2%** |
| % Avaliações com nota 2 | **3,2%** |
| % Avaliações com nota 1 | **11,5%** |

**Distribuição de notas:**

| Nota | Quantidade | % |
|---|---|---|
| ⭐⭐⭐⭐⭐ 5 | 57.328 | 57,8% |
| ⭐⭐⭐⭐ 4 | 19.142 | 19,3% |
| ⭐⭐⭐ 3 | 8.179 | 8,2% |
| ⭐⭐ 2 | 3.151 | 3,2% |
| ⭐ 1 | 11.424 | 11,5% |
| **Total** | **99.224** | **100%** |

---

### Formas de Pagamento

| Tipo | Transações | Valor Total | % Transações |
|---|---|---|---|
| Cartão de Crédito | 76.795 | R$ 1.254.208.419 | 75,7% |
| Boleto | 19.784 | R$ 286.936.127 | 19,5% |
| Voucher | 5.775 | R$ 37.943.687 | 5,7% |
| Cartão de Débito | 1.529 | R$ 21.798.979 | 1,5% |
| Não definido | 3 | R$ 0 | < 0,1% |

---

### Top 10 Estados por Volume de Pedidos

| Estado | Pedidos | % do Total |
|---|---|---|
| SP — São Paulo | 41.746 | **42,0%** |
| RJ — Rio de Janeiro | 12.852 | 12,9% |
| MG — Minas Gerais | 11.635 | 11,7% |
| RS — Rio Grande do Sul | 5.466 | 5,5% |
| PR — Paraná | 5.045 | 5,1% |
| SC — Santa Catarina | 3.637 | 3,7% |
| BA — Bahia | 3.380 | 3,4% |
| DF — Distrito Federal | 2.140 | 2,2% |
| ES — Espírito Santo | 2.033 | 2,0% |
| GO — Goiás | 2.020 | 2,0% |

---

## Insights Analíticos

### 1. Concentração geográfica extrema
São Paulo representa **42% de todos os pedidos** sozinho. Os 3 maiores estados (SP, RJ, MG) concentram **66,6% do volume total**. Isso indica oportunidade de crescimento nas regiões Norte, Nordeste e Centro-Oeste, que representam menos de 10% combinados.

### 2. Alto índice de entrega no prazo
**95% dos pedidos entregues chegaram dentro do prazo estimado** (91.614 de 96.478 entregues). No entanto, o prazo médio de entrega é de **12,56 dias** — relativamente alto para padrões modernos de e-commerce, onde entregas em 3–5 dias são cada vez mais a expectativa.

### 3. Nota média robusta com cauda negativa relevante
A nota média de **4,09/5** é positiva, mas **11,5% das avaliações são nota 1** (11.424 avaliações). Essa cauda de notas muito baixas é desproporcional em relação às notas 2 e 3, sugerindo polarização: clientes ou estão muito satisfeitos (57,8% nota 5) ou muito insatisfeitos. As notas 1 geralmente correlacionam com atrasos na entrega ou produto não recebido.

### 4. Domínio do cartão de crédito
**75,7% das transações são por cartão de crédito**, e o valor médio por transação com cartão é substancialmente maior que boleto. A presença do boleto em 19,5% das transações é característica do mercado brasileiro e reflete inclusão de consumidores sem acesso a crédito.

### 5. Taxa de cancelamento baixa, mas com atenção
Apenas **0,6% dos pedidos foram cancelados** (625 pedidos). Outros 609 estão como `unavailable` — possivelmente produtos que saíram de estoque após a compra. Juntos representam ~1,2% de pedidos problemáticos.

### 6. Prazo de recebimento vs. prazo de entrega
O `diff_recive_date` (11,18 dias) mede da aprovação ao recebimento, enquanto `order_delevery_date_finish` (12,56 dias) mede da compra à entrega. A diferença de ~1,4 dias representa o tempo entre a compra e a aprovação do pagamento, que é principalmente o prazo de processamento do boleto bancário.

### 7. Frete representa 16,6% da receita
O valor de frete (R$ 225M) é significativo em relação à receita de produtos (R$ 1,36B). Em categorias com produtos leves e de baixo ticket, o frete pode representar 30–50% do valor do pedido — um fator determinante na conversão.

### 8. Tabela de geolocalização inutilizada
`olist_geolocation_dataset` contém coordenadas para análise de mapas, mas **não possui relacionamento com nenhuma outra tabela**. Todo o potencial de análise geográfica avançada (mapas de calor, rotas de entrega, análise de cobertura) está bloqueado.

---

## Problemas Identificados no Modelo

| # | Severidade | Problema | Impacto |
|---|---|---|---|
| P01 | 🔴 Crítico | Relacionamento `orders → customers` com cardinalidade 1:1 e filtro bidirecional | Resultados DAX incorretos em análises cruzadas |
| P02 | 🔴 Crítico | `olist_geolocation_dataset` desconectada | Dados de geo inacessíveis |
| P03 | 🟡 Moderado | 4 medidas com padrão `ISFILTERED + ERROR` (Quick Measures) | Medidas retornam erro com filtros diretos de data |
| P04 | 🟡 Moderado | Typos em colunas: `delevery`, `recive`, `lenght` | Nomenclatura incorreta exposta ao usuário |
| P05 | 🟡 Moderado | Typos em medidas: `reciving`, `avf`, `meam` | Código DAX com nomes incorretos |
| P06 | 🟡 Moderado | 6 colunas calculadas de data redundantes em `olist_order_items_dataset` | Modelo maior em memória sem necessidade |
| P07 | 🟡 Moderado | Nenhuma medida de volume ou receita | Análises fundamentais ausentes |
| P08 | 🟢 Menor | `target_score = 5` hardcoded | Meta não configurável |
| P09 | 🟢 Menor | Colunas `Personalizar` vazias em 2 tabelas | Poluição do painel de campos |
| P10 | 🟢 Menor | Nomes de tabelas/colunas em inglês (padrão Kaggle) | UX inadequada para usuário final PT-BR |
| P11 | 🟢 Menor | Chaves técnicas visíveis (`order_id`, `product_id`, etc.) | Painel de campos poluído |
| P12 | 🟢 Menor | Ausência de descrições em todas as medidas e colunas | Sem documentação inline no relatório |

---

## Tecnologias Utilizadas

| Tecnologia | Versão / Detalhe |
|---|---|
| Power BI Desktop | 17.0.22.20 (Analysis Services) |
| DAX | Medidas de inteligência de tempo (TOTALYTD, TOTALMTD) |
| Modo de armazenamento | Import |
| Dataset de origem | Kaggle — Olist Brazilian E-Commerce |
| MCP Server | `@microsoft/powerbi-modeling-mcp-win32-x64` |

---

## Como Abrir o Relatório

### Pré-requisitos
- Power BI Desktop instalado ([download gratuito](https://powerbi.microsoft.com/desktop/))
- Windows 10 ou superior

### Passos
1. Clone ou baixe este repositório
2. Abra o arquivo `.pbix` no Power BI Desktop
3. Os dados já estão importados — não é necessária conexão com fonte de dados
4. Para atualizar os dados, substitua os arquivos CSV originais do Kaggle e atualize via **Transformar Dados → Fechar e Aplicar**

### Download do Dataset Original
```
https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce
```

---

## Estrutura de Arquivos

```
📁 PowerBI-MCP/
│
├── 📄 README.md                     ← Este arquivo
├── 📊 Brazilian E-Commerce.pbix     ← Arquivo do relatório Power BI
│
└── 📁 data/  (opcional — fontes CSV)
    ├── olist_customers_dataset.csv
    ├── olist_geolocation_dataset.csv
    ├── olist_order_items_dataset.csv
    ├── olist_order_payments_dataset.csv
    ├── olist_order_reviews_dataset.csv
    ├── olist_orders_dataset.csv
    ├── olist_products_dataset.csv
    ├── olist_sellers_dataset.csv
    └── product_category_name_translation.csv
```

---

## Autor

Desenvolvido como projeto de análise de dados com Power BI e integração via MCP (Model Context Protocol).

---

## Licença

Os dados do dataset Olist estão licenciados sob [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).  
O relatório Power BI e a documentação são de uso livre para fins educacionais e de portfólio.

---

*Documentação gerada automaticamente via [Power BI Modeling MCP](https://github.com/microsoft/powerbi-modeling-mcp) + Kiro AI — Agosto 2026*
