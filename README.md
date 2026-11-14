# Segmentação e previsão de cancelamento de clientes em telecom

Projeto de Machine Learning Clássico (ABP). Tema: separação e previsão de cancelamento de clientes de uma empresa de telecomunicações.

**Grupo 6:** Otávio Bonini e Cauã Medeiros
**Professor:** Rodrigo Ramos Silva

## O que o projeto faz

O projeto responde a três perguntas sobre os clientes de uma operadora:

1. **Separação (K-means):** que tipos de cliente existem? O K-means junta os clientes em 4 grupos usando o tempo de casa e a mensalidade, sem saber quem cancelou.
2. **Previsão (classificação):** qual a chance de o cliente cancelar? Comparamos Árvore de Decisão, SVM, Random Forest e Gradient Boosting.
3. **Receita em risco:** quanto dinheiro está em jogo? Multiplicando a chance de cancelar pela mensalidade, cada cliente ganha uma receita em risco. Ordenando os clientes por esse valor, a equipe de retenção tem uma fila: começa por quem tem mais dinheiro em jogo.

## Arquivos

| Arquivo | O que é |
|---|---|
| `Projeto_ML_Churn_Telecom.ipynb` | Notebook do Google Colab com o projeto inteiro, do carregamento dos dados até a fila de retenção |
| `Projeto_ML_Churn_Telecom.docx` | Documento do projeto (relatório em formato ABNT) |
| `Projeto_ML_Churn_Telecom.pdf` | O mesmo documento em PDF |
| `Slides_Churn_Telecom.pdf` | Slides da apresentação |
| `requirements.txt` | Bibliotecas usadas |

## Base de dados

**Telco Customer Churn**, da IBM (também publicada no Kaggle).
7.043 clientes de uma operadora fictícia da Califórnia, com 21 colunas: tipo de contrato, tempo de casa, serviços contratados, forma de pagamento, mensalidade e se o cliente cancelou. Cerca de 26,5% cancelaram.

O notebook lê a base direto do GitHub da IBM. Não é preciso baixar nada antes.

- Kaggle: https://www.kaggle.com/datasets/blastchar/telco-customer-churn
- Arquivo usado: https://raw.githubusercontent.com/IBM/telco-customer-churn-on-icp4d/master/data/Telco-Customer-Churn.csv

## Como rodar

1. Abra o arquivo `Projeto_ML_Churn_Telecom.ipynb` no Google Colab (faça upload em https://colab.research.google.com).
2. No menu, clique em **Ambiente de execução > Executar tudo**.
3. Leva cerca de 2 minutos. A parte mais lenta é o GridSearch dos 4 modelos.

## Especificações técnicas

- **Linguagem:** Python 3
- **Ambiente:** Google Colab (não precisa de GPU)
- **Bibliotecas:** pandas, numpy, scikit-learn, matplotlib (lista em `requirements.txt`)
- **Reprodutibilidade:** todos os sorteios usam `random_state=42`, então o resultado é sempre o mesmo

Para rodar fora do Colab:

```
pip install -r requirements.txt
```

## Etapas do notebook

1. Carregamento da base e primeira olhada
2. Limpeza: TotalCharges vinha como texto, com 11 linhas em branco (clientes com 0 meses de casa, que viraram 0)
3. Coluna nova: `qtd_servicos`, quantos serviços o cliente contrata
4. Análise exploratória: cancelamento por contrato, internet, forma de pagamento, tempo de casa e mensalidade
5. K-means: escolha do número de grupos pela curva do cotovelo e pela silhouette (K = 4), e perfil de cada grupo
6. Classificação: divisão treino/teste (80/20, estratificada), StandardScaler e One-Hot Encoder dentro de um Pipeline, 4 modelos ajustados com GridSearchCV (validação cruzada em 5 partes, critério F1)
7. Comparação dos modelos, matrizes de confusão, curva ROC e importância das variáveis
8. Receita em risco, fila de retenção e ação sugerida para cada grupo

## Principais resultados

**Os 4 grupos do K-means**

| Grupo | Clientes | Cancelaram |
|---|---|---|
| Novos com conta alta | 2.204 | 49,2% |
| Novos com conta baixa | 1.732 | 24,5% |
| Antigos com conta alta | 1.952 | 15,6% |
| Antigos com conta baixa | 1.155 | 4,8% |

**Classificação (conjunto de teste, 1.409 clientes)**

| Modelo | Acurácia | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| **Gradient Boosting** | 74,2% | 50,8% | 81,0% | 0,625 | 0,845 |
| Random Forest | 76,8% | 54,7% | 73,3% | 0,626 | 0,842 |
| SVM | 73,7% | 50,3% | 80,2% | 0,618 | 0,832 |
| Árvore de Decisão | 73,2% | 49,7% | 80,5% | 0,614 | 0,829 |

**Fila de retenção:** os 20% primeiros da fila concentram 55,5% da receita que seria perdida e 46,3% dos clientes que cancelaram. Nesse topo, o cancelamento é 2,3 vezes maior do que escolhendo clientes ao acaso.

## Referências

VERBEKE, W.; DEJAEGER, K.; MARTENS, D.; HUR, J.; BAESENS, B. New insights into churn prediction in the telecommunication sector: a profit driven data mining approach. **European Journal of Operational Research**, v. 218, n. 1, p. 211-229, 2012.

IBM. Telco customer churn (11.1.3+). IBM Community, 2019. Disponível em: https://community.ibm.com/community/user/businessanalytics/blogs/steven-macko/2019/07/11/telco-customer-churn-1113
