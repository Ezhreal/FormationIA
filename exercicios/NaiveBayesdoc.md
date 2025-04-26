# Documentação do Modelo de Classificação de Acidentes

## Resumo do Projeto
Este projeto utilizou um modelo de Naive Bayes Gaussiano para classificar acidentes de seguro em quatro categorias: "None" (Nenhum), "Mild" (Leve), "Moderate" (Moderado) e "Severe" (Grave), baseado em múltiplas variáveis do conjunto de dados "insurance.csv".

## Metodologia Aplicada
1. **Pré-processamento de Dados**:
   - Codificação de variáveis categóricas usando LabelEncoder
   - Divisão do conjunto de dados (70% treino, 30% teste)

2. **Modelo Utilizado**:
   - Algoritmo Naive Bayes Gaussiano (GaussianNB)
   - Treinamento com os dados de treino
   - Predição com os dados de teste

3. **Avaliação do Modelo**:
   - Métricas globais
   - Métricas por classe

## Código Implementado

### Passo 1: Importação das bibliotecas
```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.naive_bayes import GaussianNB
from sklearn.preprocessing import LabelEncoder
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, classification_report
```

### Passo 2: Carregamento e preparação dos dados
```python
# Carregar o conjunto de dados
base = pd.read_csv('insurance.csv')

# Separar features (X) e target (y)
x_base = base.drop('Accident', axis=1)  # 'Accident' é a variável alvo
y_base = base['Accident']
```

### Passo 3: Tratamento das variáveis categóricas
```python
# Converter todas as variáveis categóricas para numéricas
for coluna in x_base.select_dtypes(include=['object']).columns:
    le = LabelEncoder()
    x_base[coluna] = le.fit_transform(X[coluna])
```

### Passo 4: Divisão em conjuntos de treino e teste
```python
# Dividir os dados (70% treino, 30% teste)
X_treino, X_teste, y_treino, y_teste = train_test_split(x_base, y_base, test_size=0.3, random_state=42)
```

### Passo 5: Criação e treinamento do modelo
```python
# Criar o modelo Naive Bayes Gaussiano
modelo = GaussianNB()

# Treinar o modelo
modelo.fit(X_treino, y_treino)
```

### Passo 6: Realização de previsões
```python
# Fazer previsões no conjunto de teste
previsoes = modelo.predict(X_teste)
```

### Passo 7: Avaliação do modelo
```python
# Calcular métricas gerais
accuracy = accuracy_score(y_teste, previsoes)
precision = precision_score(y_teste, previsoes, average='weighted')
recall = recall_score(y_teste, previsoes, average='weighted')
f1 = f1_score(y_teste, previsoes, average='weighted')

# Exibir métricas gerais
print(f'Acurácia: {accuracy:.4f}')
print(f'Precisão: {precision:.4f}')
print(f'Recall: {recall:.4f}')
print(f'F1-Score: {f1:.4f}')

# Exibir relatório detalhado de classificação
print(classification_report(y_teste, previsoes))
```

## Resultados Obtidos

### Métricas Globais
```
Acurácia: 0.8425
Precisão: 0.8851
Recall: 0.8425
F1-Score: 0.8178
```

### Métricas por Classe
```
              precision    recall  f1-score   support

        Mild       0.64      0.79      0.71       517
    Moderate       0.33      0.66      0.44       476
        None       0.98      1.00      0.99      4298
      Severe       0.85      0.06      0.11       709

    accuracy                           0.84      6000
   macro avg       0.70      0.63      0.56      6000
weighted avg       0.89      0.84      0.82      6000
```

## Análise dos Resultados

### Pontos Positivos
1. **Alta Acurácia Global**: 84.25% das predições estão corretas, o que é um bom resultado geral.

2. **Excelente Desempenho para Classe "None"**: O modelo é quase perfeito para identificar casos sem acidentes (precision: 0.98, recall: 1.00).

### Pontos de Atenção
1. **Desbalanceamento de Classes**: 
   - A classe "None" tem 4298 exemplos (71.6% do total)
   - As demais classes têm quantidade bem menor de exemplos

2. **Baixo Desempenho em Classes Minoritárias**:
   - **Severe**: Apesar de alta precisão (0.85), o recall é extremamente baixo (0.06), indicando que o modelo raramente identifica acidentes graves quando eles ocorrem
   - **Moderate**: Baixa precisão (0.33) indica muitos falsos positivos

## Interpretação Visual

```
Distribuição das Classes:
None: ████████████████████████████████████████████████ (71.6%)
Severe: ███████ (11.8%)
Mild: █████ (8.6%)
Moderate: █████ (7.9%)

Desempenho por Classe (F1-Score):
None: ████████████████████████████████████████████████ (0.99)
Mild: █████████████████████████████████ (0.71)
Moderate: ██████████████████████ (0.44)
Severe: █████ (0.11)
```

## Conclusões e Recomendações

### Diagnóstico
O modelo apresenta bom desempenho geral, impulsionado principalmente pelo excelente reconhecimento da classe majoritária ("None"). No entanto, há um problema crítico com o reconhecimento de acidentes graves (classe "Severe"), com recall de apenas 6%.

### Próximos Passos Recomendados

1. **Tratamento do Desbalanceamento**:
   - Técnicas de oversampling (SMOTE)
   - Undersampling da classe majoritária
   - Ajuste de pesos das classes

2. **Explorar Outros Algoritmos**:
   - Random Forest ou XGBoost podem lidar melhor com dados desbalanceados
   - Comparar o desempenho com o modelo atual

3. **Feature Engineering**:
   - Analisar a importância das variáveis
   - Criar novas características que possam ajudar a distinguir melhor entre as classes

4. **Otimização de Hiperparâmetros**:
   - Testar diferentes configurações do modelo
   - Utilizar validação cruzada para uma avaliação mais robusta

5. **Validação de Negócio**:
   - Avaliar o custo de classificar erroneamente acidentes graves como leves ou inexistentes
   - Ajustar o modelo considerando o impacto no negócio

## Limitações do Naive Bayes

O algoritmo Naive Bayes assume que todas as características são independentes entre si, o que raramente ocorre na realidade. No contexto de seguros, fatores como idade do motorista, valor do carro e histórico de direção provavelmente têm correlações entre si. Esta suposição "naive" pode limitar o desempenho do modelo, especialmente para as classes minoritárias.

## Aprendizados

Este projeto demonstra que mesmo um algoritmo relativamente simples como o Naive Bayes pode alcançar resultados decentes em tarefas de classificação. No entanto, para problemas com classes desbalanceadas e características correlacionadas, técnicas adicionais ou algoritmos mais sofisticados podem ser necessários para melhorar o desempenho nas classes minoritárias.
