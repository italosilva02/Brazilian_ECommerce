# 👥 IBM HR Analytics | Employee Attrition & Performance

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-00599C?style=for-the-badge&logo=microsoft&logoColor=white)
![Kaggle](https://img.shields.io/badge/Kaggle-20BEFF?style=for-the-badge&logo=Kaggle&logoColor=white)
![Status](https://img.shields.io/badge/Status-Concluído-success?style=for-the-badge)

> Análise de People Analytics desenvolvida no Power BI para identificar padrões relacionados ao desligamento de colaboradores. O projeto transforma dados de RH em indicadores estratégicos, permitindo analisar fatores como remuneração, idade, horas extras, satisfação, viagens e equilíbrio entre vida pessoal e profissional.

---

## 📊 Acesse o Dashboard Interativo

Para navegar pelas páginas, utilizar os filtros e explorar os indicadores dinamicamente, acesse a versão publicada no Power BI Web:

[![Acessar Relatório Power BI](https://img.shields.io/badge/Visualizar_Dashboard_Interativo-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](COLOQUE_AQUI_O_LINK_DO_SEU_POWER_BI)

---

## 💡 Principais Insights Analíticos

A análise dos **1.470 colaboradores** revelou padrões importantes relacionados ao risco de desligamento:

* 🔴 **Taxa geral de atrito de 16,12%:** Dos 1.470 colaboradores analisados, **237 deixaram a organização**. O indicador representa o principal KPI do projeto e serve como referência para identificar os grupos com maior risco de saída.

* ⏱️ **Horas extras apresentam forte associação com o desligamento:** Enquanto **28,3% da força de trabalho realizava horas extras**, entre os colaboradores desligados esse percentual chegou a **53,6%**, indicando uma forte concentração de desligamentos nesse grupo.

* 👨‍💼 **Sales Representative apresenta o maior índice de atrito:** O cargo registrou uma taxa de **39,8%**, com 33 desligamentos em um total de 83 colaboradores. Além disso, apresentou o menor salário médio entre os cargos analisados.

* 👶 **Colaboradores com até 25 anos concentram maior risco:** A taxa de atrito desse grupo foi de **35,8%**, mais que o dobro da média geral da organização.

* 💰 **Baixa remuneração está associada a maiores taxas de desligamento:** Colaboradores com salário de até **$3.000 mensais** apresentaram uma taxa de atrito de **28,6%**, enquanto aqueles com salários acima de $15.000 tiveram apenas **3,8%**.

* ✈️ **Viagens frequentes aumentam significativamente o atrito:** O grupo `Travel_Frequently` apresentou **24,9% de desligamentos**, enquanto os colaboradores classificados como `Non-Travel` tiveram apenas **8,0%**.

* ⚖️ **Equilíbrio entre vida pessoal e trabalho influencia o risco:** Colaboradores que classificaram seu `WorkLifeBalance` como **Bad** apresentaram uma taxa de atrito de **31,3%**, uma das maiores observadas na análise.

* 📈 **Cargos de entrada concentram grande parte dos desligamentos:** O `JobLevel 1` representa aproximadamente **37% da força de trabalho**, mas concentra **143 dos 237 desligamentos**, equivalente a cerca de **60% do total**.

---

## 📈 Resumo de KPIs

| Métrica | Valor | Métrica | Valor |
|---|---:|---|---:|
| 👥 **Total de Colaboradores** | 1.470 | 🟢 **Headcount Ativo** | 1.233 |
| 🚪 **Total de Desligamentos** | 237 | 📉 **Taxa de Atrito** | 16,12% |
| 🎂 **Idade Média** | 36,9 anos | 💰 **Salário Médio** | $ 6.502,93 |
| 🕒 **Tempo Médio na Empresa** | 7,0 anos | ⏱️ **Colaboradores com Hora Extra** | 28,3% |

---

## ⚙️ Arquitetura do Modelo Semântico

O projeto foi desenvolvido utilizando um modelo **flat**, composto por uma tabela principal contendo os dados dos colaboradores e uma tabela dedicada exclusivamente às medidas DAX.

Essa abordagem foi escolhida considerando o volume relativamente pequeno do dataset, permitindo uma estrutura simples e eficiente para análise no Power BI.

```mermaid
graph TD

    A[IBM HR Analytics Dataset] --> B[WA_Fn-UseC HR Employee Attrition]

    B --> C[Informações Demográficas]
    B --> D[Informações Profissionais]
    B --> E[Remuneração]
    B --> F[Satisfação e Engajamento]
    B --> G[Tempo de Empresa]
    B --> H[Variável Attrition]

    B --> I[Colunas Calculadas DAX]

    J[_Medidas] --> K[Total de Funcionários]
    J --> L[Headcount Ativo]
    J --> M[Total de Desligamentos]
    J --> N[Taxa de Atrito]
    J --> O[Idade Média]
    J --> P[% Total de Funcionários]
