
# APIs de Energia Renovável e Aprendizado de Máquina

**Projeto acadêmico — Ciência da Computação | FIAP**

## Objetivo

Este projeto tem como objetivo aplicar técnicas de Aprendizado de Máquina (Machine Learning) na análise de dados relacionados à energia renovável, utilizando informações obtidas por meio de APIs públicas.

A atividade foi dividida em duas tarefas:

1. **Classificação:** identificar a fonte de geração de empreendimentos energéticos da ANEEL a partir de características como potência e localização.
2. **Regressão:** estimar a radiação solar horizontal média em Petrolina (PE) com base em variáveis meteorológicas e na hora do dia.

Foram implementados e comparados três algoritmos para cada tarefa, utilizando métricas apropriadas para avaliar seus desempenhos.

## Tecnologias utilizadas

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn
* Scikit-learn
* Jupyter Notebook
* APIs REST (JSON)

## Fontes de dados

### 1. ANEEL — SIGA

Fonte: [Sistema de Informações de Geração da ANEEL](https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel)

Os dados foram obtidos por meio da API pública CKAN/DataStore, considerando empreendimentos dos seguintes tipos:

* UFV — Solar fotovoltaica
* EOL — Eólica
* UHE — Usina Hidrelétrica
* PCH — Pequena Central Hidrelétrica
* CGH — Central Geradora Hidrelétrica

As categorias UHE, PCH e CGH foram agrupadas na classe Hidráulica.

O conjunto final possui **3.876 registros**, com três atributos numéricos e uma variável categórica.

### 2. Open-Meteo — Historical Weather API

Fonte: [Open-Meteo Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api)

* **Localização:** Petrolina, Pernambuco
* **Latitude:** -9.39
* **Longitude:** -40.50
* **Período:** 01/04/2025 a 30/06/2025
* **Fuso horário:** America/Recife
* **Frequência:** horária

Foram utilizadas as seguintes variáveis meteorológicas:

* Temperatura a 2 metros (°C)
* Umidade relativa (%)
* Cobertura de nuvens (%)
* Velocidade do vento (km/h)
* Hora do dia

A variável alvo é a radiação solar horizontal média, expressa em W/m².

O conjunto final possui **1.001 registros**, considerando os horários entre 07h e 17h.

Os dados históricos do Open-Meteo são provenientes de modelos meteorológicos e reanálises, não de medições diretas de um painel fotovoltaico.

## Estrutura do projeto

```text
.
├── Aula_APIs_Energia_Renovavel_ML.ipynb
├── aneel_classificacao_orange.csv
├── meteo_regressao_orange.csv
└── README.md
```

## Como executar

### 1. Clonar o repositório

```bash
git clone https://github.com/Dustyluc123/checkpoint02-energia-renovavel.git
 
cd checkpoint02-energia-renovavel
```

Substitua os valores acima pelo endereço e nome do repositório no GitHub.

### 2. Instalar as dependências

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### 3. Executar o notebook

```bash
jupyter notebook
```

Abra o arquivo `.ipynb` e execute as células sequencialmente.

O notebook contém as consultas às APIs, o tratamento dos dados, a análise exploratória, o treinamento dos modelos, as métricas de avaliação e as conclusões.

As consultas às APIs são públicas e não exigem chave de autenticação. É necessária conexão com a internet para reproduzir a coleta dos dados.

Os arquivos CSV também estão disponibilizados no repositório para permitir a análise sem realizar uma nova consulta.

## Metodologia

### Tarefa 1 — Classificação

**Objetivo:** classificar os empreendimentos em Solar, Eólica ou Hidráulica.

Variáveis de entrada:

* `potencia_kw`
* `latitude`
* `longitude`

Variável alvo:

* `fonte`

A base foi dividida em treinamento e teste na proporção de 80%/20%, utilizando estratificação e semente fixa.

Foram utilizados três classificadores:

1. K-Nearest Neighbors (KNN)
2. Árvore de Decisão
3. Regressão Logística

A avaliação considerou Accuracy, Precision, Recall e F1-score, utilizando média macro para as métricas por classe, além de matrizes de confusão.

### Resultados

| Algoritmo           | Accuracy | Precision Macro | Recall Macro | F1 Macro |
| ------------------- | -------: | --------------: | -----------: | -------: |
| KNN                 |   0.9665 |          0.9677 |       0.9649 |   0.9662 |
| Árvore de Decisão   |   0.9639 |          0.9643 |       0.9627 |   0.9634 |
| Regressão Logística |   0.8235 |          0.8275 |       0.8200 |   0.8182 |

O KNN apresentou os maiores valores nas métricas avaliadas, seguido pela Árvore de Decisão.

No KNN e na Árvore de Decisão os erros são poucos (26 e 28 em 776 amostras) e a confusão mais frequente é entre Solar e Hidráulica. A confusão forte entre Solar e Eólica aparece apenas na Regressão Logística (51 casos), por ser um modelo linear que não separa bem as regiões geográficas.

### Tarefa 2 — Regressão

**Objetivo:** estimar a radiação solar horizontal média em Petrolina (PE).

Variáveis de entrada:

* `temperatura_c`
* `umidade_pct`
* `nuvens_pct`
* `vento_kmh`
* `hora`

Variável alvo:

* `radiacao_w_m2`

A divisão dos dados respeitou a ordem cronológica, utilizando aproximadamente 80% das primeiras observações para treinamento e os 20% finais para teste, sem embaralhamento.

Foram utilizados três regressores:

1. Regressão Linear
2. Random Forest Regressor
3. KNN Regressor

As métricas utilizadas foram MAE, MSE e R², além da comparação gráfica entre valores reais e previstos.

### Resultados

| Algoritmo        | MAE (W/m²) |        MSE |     R² |
| ---------------- | ---------: | ---------: | -----: |
| Regressão Linear |   145.2049 | 30034.2011 | 0.3598 |
| Random Forest    |    66.2517 |  7250.8882 | 0.8455 |
| KNN              |    68.2706 |  7607.9614 | 0.8378 |

O Random Forest apresentou o menor erro absoluto médio e quadrático, além do maior coeficiente de determinação.

O resultado indica que o modelo conseguiu representar boa parte da variação da radiação solar no conjunto de teste.

## Conclusão

A atividade permitiu aplicar diferentes algoritmos de Machine Learning em problemas de classificação e regressão relacionados ao setor de energia renovável.

Na classificação, o KNN apresentou os melhores resultados gerais, demonstrando que potência e localização podem fornecer informações relevantes para identificar a fonte de geração. Entretanto, essas características não são suficientes para garantir uma classificação confiável em todos os cenários reais.

Na regressão, o Random Forest apresentou melhor desempenho na estimativa da radiação solar, considerando as métricas analisadas. A hora do dia e as condições meteorológicas demonstraram relevância para compreender o comportamento da radiação ao longo do período.

Por fim, a radiação solar estimada não representa diretamente a energia elétrica produzida por um sistema fotovoltaico, uma vez que a geração também depende de fatores técnicos e operacionais dos equipamentos.

Os resultados estão limitados ao período e às características dos dados utilizados, sendo necessária uma avaliação adicional para verificar sua generalização em outras épocas do ano.

## Autor

**Lucas Barreto Santana**

Ciência da Computação — FIAP

RM: 573149

## Referências

* [ANEEL — Dados Abertos / SIGA](https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel)
* [ANEEL — Recurso de dados SIGA](https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel/resource/11ec447d-698d-4ab8-977f-b424d5deee6a)
* [Open-Meteo — Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api)
* [Scikit-learn](https://scikit-learn.org/)
