# 👥 IBM HR Analytics | Employee Attrition & Performance

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-00599C?style=for-the-badge&logo=microsoft&logoColor=white)
![Kaggle](https://img.shields.io/badge/Kaggle-20BEFF?style=for-the-badge&logo=Kaggle&logoColor=white)
![Status](https://img.shields.io/badge/Status-Concluído-success?style=for-the-badge)

> Análise de People Analytics desenvolvida no Power BI para identificar padrões relacionados ao desligamento de colaboradores. O projeto transforma dados de Recursos Humanos em inteligência de negócios, analisando fatores como remuneração, idade, horas extras, satisfação, viagens e equilíbrio entre vida pessoal e profissional.

---

## 📊 Acesse o Dashboard Interativo

Para navegar pelas páginas, utilizar os filtros e explorar os indicadores dinamicamente, acesse a versão publicada no Power BI Web:

[![Acessar Relatório Power BI](https://img.shields.io/badge/Visualizar_Dashboard_Interativo-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://app.powerbi.com/view?r=eyJrIjoiZDNiNmIzNTMtMjYwMi00NGI5LThmYTktZjJhMDVmMWRjZGM5IiwidCI6IjkwN2IxZDhiLTE2ZTEtNDZiZi05ODczLTI3MjNmNTlmODcwYSJ9)

---

## 💡 Principais Insights Analíticos

A análise dos **1.470 colaboradores** revelou padrões importantes relacionados ao desligamento e à retenção de talentos:

* 🔴 **Taxa geral de atrito de 16,12%:** Dos 1.470 colaboradores analisados, **237 deixaram a organização**, enquanto 1.233 permaneceram ativos.

* ⏱️ **Horas extras apresentam forte associação com o desligamento:** Apenas **28,3% da força de trabalho realizava horas extras**, mas entre os colaboradores desligados esse percentual chegou a **53,6%**.

* 👨‍💼 **Sales Representative apresenta o maior índice de atrito:** O cargo registrou uma taxa de **39,8%**, representando o maior percentual entre todos os cargos analisados.

* 👶 **Colaboradores com até 25 anos apresentam maior risco:** A taxa de atrito desse grupo foi de **35,8%**, mais que o dobro da média geral da organização.

* 💰 **Baixa remuneração está associada a maiores taxas de desligamento:** Colaboradores com salário de até **$3.000 mensais** apresentaram uma taxa de atrito de **28,6%**, enquanto aqueles acima de $15.000 registraram apenas **3,8%**.

* ✈️ **Viagens frequentes aumentam significativamente o atrito:** O grupo `Travel_Frequently` apresentou **24,9% de desligamentos**, enquanto o grupo `Non-Travel` apresentou apenas **8,0%**.

* ⚖️ **Equilíbrio entre vida pessoal e trabalho influencia o risco:** Colaboradores que classificaram seu `WorkLifeBalance` como **Bad** apresentaram uma taxa de atrito de **31,3%**.

* 📈 **Cargos de entrada concentram grande parte dos desligamentos:** O `JobLevel 1` representa aproximadamente **37% da força de trabalho**, mas concentra **60% de todos os desligamentos**.

---

## 📈 Resumo de KPIs

| Métrica | Valor | Métrica | Valor |
|---|---:|---|---:|
| 👥 **Total de Colaboradores** | 1.470 | 🟢 **Headcount Ativo** | 1.233 |
| 🚪 **Total de Desligamentos** | 237 | 📉 **Taxa de Atrito** | 16,12% |
| 🎂 **Idade Média** | 36,9 anos | 💰 **Salário Médio** | $ 6.502,93 |
| 🕒 **Tempo Médio na Empresa** | 7,0 anos | ⏱️ **Com Horas Extras** | 28,3% |

---

## ⚙️ Arquitetura do Modelo Semântico

O projeto foi desenvolvido utilizando uma abordagem **flat**, composta por uma tabela principal contendo os dados dos colaboradores e uma tabela dedicada exclusivamente à organização das medidas DAX.

Essa estrutura foi escolhida considerando o volume reduzido do dataset, permitindo um modelo simples e eficiente para exploração dos dados no Power BI.

```mermaid
graph TD

    A[IBM HR Analytics Dataset] --> B[WA_Fn-UseC HR Employee Attrition]

    B --> C[Informações Demográficas]
    B --> D[Informações Profissionais]
    B --> E[Remuneração]
    B --> F[Satisfação e Engajamento]
    B --> G[Tempo de Empresa]
    B --> H[Attrition]

    B --> I[Colunas Calculadas DAX]

    J[_Medidas] --> K[Total de Funcionários]
    J --> L[Headcount Ativo]
    J --> M[Total de Desligamentos]
    J --> N[Taxa de Atrito]
    J --> O[Idade Média]
    J --> P[% Total de Funcionários]
