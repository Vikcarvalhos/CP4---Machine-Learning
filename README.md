# Equipe

| Nome | RM |
|---|---:|
| Diana Letícia de Souza Inocencio | RM553562 |
| João Viktor Carvalho de Souza | RM552613 |
| Lucas Reis Diniz | RM552838 |
| Thiago Araújo Vieira | RM553477 |
| Victor Augusto Pereira dos Santos | RM553518 |
| Vitor de Moura Nascimento | RM553806 |

# Detecção de Fraudes com Regressão Logística e Isolation Forest

## Objetivo

Este projeto compara duas abordagens para detecção de fraudes em transações financeiras:

- **Regressão Logística**, abordagem supervisionada.
- **Isolation Forest**, abordagem não supervisionada para detecção de anomalias.

A análise foi realizada **com e sem PCA**, conforme solicitado na atividade, com foco em métricas adequadas para classe rara: **Recall**, **F1-Score**, **Precision-Recall AUC** e análise de **falsos positivos** e **falsos negativos**.

## Arquivos da entrega

- `fraude_deteccao.ipynb`: notebook principal com código, gráficos, métricas e análises.
- `relatorio_fraude.pdf`: relatório final em PDF com metodologia, resultados e respostas teóricas.
- `README.md`: este arquivo, com resumo da entrega e instruções de execução.
- `creditcard.csv`: base de dados utilizada.

## Base de dados

O dataset possui **284.807 transações**, sendo:

Origem do dataset: [Credit Card Fraud Detection - Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud).

| Classe | Quantidade | Proporção |
|---|---:|---:|
| Normal | 284.315 | 99,8273% |
| Fraude | 492 | 0,1727% |

Esse desbalanceamento torna a acurácia uma métrica inadequada: um modelo que prevê todas as transações como normais já alcança aproximadamente **99,83% de acurácia**, mas detecta **zero fraudes**.

## Modelos e hiperparâmetros avaliados

### Regressão Logística

Foram avaliadas as variações exigidas:

- Regularização: `L1`, `L2`, `ElasticNet`
- Parâmetro `C`: `0.01`, `0.1`, `1`, `10`
- `class_weight`: padrão (`None`) e `'balanced'`
- Ajuste de threshold em validação para maximizar F1-Score

### Isolation Forest

Foram avaliadas variações de:

- `n_estimators`
- `contamination`, incluindo a taxa real de fraude
- `max_samples`

Como é um método não supervisionado, as predições foram convertidas para o padrão da variável alvo:

- `-1` foi tratado como fraude (`1`)
- `1` foi tratado como normal (`0`)

## Principais resultados no teste

| Modelo | Precision | Recall | F1 | ROC-AUC | PR-AUC | FP | FN | TP |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Regressão Logística - Sem PCA | 0,8560 | 0,7230 | 0,7839 | 0,9669 | 0,6907 | 18 | 41 | 107 |
| Isolation Forest - Sem PCA | 0,3816 | 0,1959 | 0,2589 | 0,9424 | 0,1561 | 47 | 119 | 29 |
| Regressão Logística - Com PCA | 0,8201 | 0,7703 | 0,7944 | 0,9490 | 0,7053 | 25 | 34 | 114 |
| Isolation Forest - Com PCA | 0,1258 | 0,1284 | 0,1271 | 0,9360 | 0,0836 | 132 | 129 | 19 |

## Conclusões principais

A **Regressão Logística com PCA** apresentou o melhor equilíbrio geral, com **F1 = 0,7944** e **Recall = 0,7703**, detectando 114 das 148 fraudes no conjunto de teste.

A **Regressão Logística sem PCA** teve maior precision, com menos falsos positivos, mas perdeu mais fraudes do que a versão com PCA.

O **Isolation Forest** teve desempenho inferior, especialmente em F1-Score e Recall. Mesmo com ROC-AUC alto, sua precisão operacional foi baixa para fraude, evidenciando que ranking de anomalia não garante boa classificação final em bases extremamente desbalanceadas.

O PCA ajudou levemente a Regressão Logística, aumentando Recall, F1 e PR-AUC. Para o Isolation Forest, o PCA prejudicou o desempenho, provavelmente por remover informações úteis para o isolamento das anomalias.

## Como executar

1. Abra o arquivo `fraude_deteccao.ipynb` em Jupyter Notebook, JupyterLab ou VS Code.
2. Garanta que o arquivo `creditcard.csv` esteja na mesma pasta do notebook.
3. Execute as células em ordem.

Bibliotecas necessárias:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn
```

## Observação sobre tempo de execução

O notebook avalia múltiplas combinações de hiperparâmetros. Para reduzir o tempo de execução, é possível diminuir a grade de busca, por exemplo:

- Reduzir `max_iter` da Regressão Logística.
- Usar menos valores em `n_estimators` e `max_samples` no Isolation Forest.
- Manter `n_jobs=-1` para aproveitar os núcleos de CPU disponíveis.
