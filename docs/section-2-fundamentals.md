# Conceitos Fundamentais de Aprendizado de Máquina

## 1. Principais Paradigmas de Aprendizado de Máquina

### 1.1 Classificação
A classificação é uma tarefa supervisionada onde o objetivo é prever a qual categoria (classe) um determinado exemplo pertence. O modelo aprende a partir de exemplos rotulados e depois aplica esse conhecimento para classificar novos dados.

**Características principais:**
- É um aprendizado supervisionado (requer dados rotulados)
- A variável alvo é categórica/discreta
- Exemplos: spam ou não-spam, doente ou saudável, tipo de flor

### 1.2 Regressão
A regressão também é uma tarefa supervisionada, mas ao invés de prever categorias, seu objetivo é prever valores numéricos contínuos. O modelo tenta estabelecer relações matemáticas entre variáveis para fazer previsões precisas.

**Características principais:**
- É um aprendizado supervisionado (requer dados rotulados)
- A variável alvo é numérica/contínua
- Exemplos: previsão de preços, estimativa de idade, previsão de temperatura

### 1.3 Agrupamento (Clustering)
O agrupamento é uma tarefa não-supervisionada que busca encontrar padrões ou estruturas naturais nos dados, agrupando exemplos similares sem ter acesso a rótulos predefinidos.

**Características principais:**
- É um aprendizado não-supervisionado (não requer dados rotulados)
- Não há variável alvo predefinida
- O objetivo é descobrir grupos naturais nos dados
- Exemplos: segmentação de clientes, agrupamento de documentos por tema, identificação de padrões de comportamento

### 1.4 Regras de Associação
As regras de associação são um método de aprendizado de máquina não supervisionado que busca descobrir relações interessantes entre variáveis em grandes conjuntos de dados. Essas técnicas identificam padrões frequentes, associações ou correlações entre conjuntos de itens.

**Características principais:**
- É um aprendizado não supervisionado (não requer dados rotulados)
- Busca identificar relações do tipo "se A, então B" nos dados
- Trabalha com regras que possuem uma parte antecedente e uma parte consequente
- É avaliada por métricas como suporte, confiança e lift
- Exemplo clássico: análise de cesta de compras (market basket analysis)

**Algoritmos comuns:**
- Apriori
- ECLAT
- FP-Growth

## 2. Tipos de Treinamento em Classificação

### 2.1 Aprendizado Supervisionado
É o tipo mais comum para classificação, onde o modelo aprende a partir de dados rotulados. O algoritmo recebe exemplos de entrada junto com suas respectivas classes e aprende a mapear as entradas para as saídas corretas.

### 2.2 Aprendizado Semi-supervisionado
Utiliza tanto dados rotulados quanto não rotulados durante o treinamento. É útil quando temos poucos dados rotulados (que são caros ou difíceis de obter) e muitos dados não rotulados. O modelo aprende a estrutura geral dos dados com os exemplos não rotulados e refina sua capacidade de classificação com os exemplos rotulados.

### 2.3 Aprendizado por Transferência
Envolve usar um modelo pré-treinado em um conjunto de dados grande e depois ajustá-lo para uma tarefa específica com um conjunto de dados menor. Isso aproveita o conhecimento já adquirido pelo modelo em um domínio para aplicá-lo em outro relacionado.

### 2.4 Aprendizado Ativo
O algoritmo seleciona ativamente quais dados devem ser rotulados por um especialista humano. Ele identifica os exemplos mais informativos ou incertos, otimizando assim o processo de rotulagem e potencialmente reduzindo a quantidade necessária de dados rotulados.

### 2.5 Aprendizado Ensemble
Combina múltiplos modelos de classificação para obter melhor desempenho do que qualquer modelo individual. Técnicas como bagging (Random Forest), boosting (AdaBoost, XGBoost) e stacking são exemplos de métodos ensemble.

### 2.6 Aprendizado Online
O modelo é atualizado incrementalmente à medida que novos dados chegam, em vez de ser treinado de uma só vez com todo o conjunto de dados. É útil quando os dados são muito grandes ou chegam continuamente em um fluxo.

## 3. Problemas de Ajuste em Modelos

### 3.1 Subajuste (Underfitting)
O subajuste ocorre quando o modelo é muito simples para capturar a complexidade dos dados de treinamento.

**Características:**
- Alto erro tanto nos dados de treinamento quanto nos dados de teste
- Baixa variância, mas alto viés
- O modelo não consegue aprender os padrões nos dados

**Causas comuns:**
- Modelo com complexidade insuficiente
- Treinamento insuficiente
- Features inadequadas ou insuficientes

**Soluções:**
- Aumentar a complexidade do modelo (mais parâmetros, camadas, etc.)
- Melhorar a qualidade ou quantidade das features
- Reduzir a regularização (se estiver sendo aplicada excessivamente)
- Treinar o modelo por mais tempo

### 3.2 Sobreajuste (Overfitting)
O sobreajuste ocorre quando o modelo aprende detalhes e ruído dos dados de treinamento que prejudicam seu desempenho em novos dados.

**Características:**
- Erro muito baixo nos dados de treinamento, mas alto nos dados de teste
- Alta variância, baixo viés
- O modelo memoriza os dados de treinamento em vez de aprender padrões generalizáveis

**Causas comuns:**
- Modelo muito complexo para a quantidade de dados disponível
- Treinamento excessivo
- Ruído nos dados de treinamento
- Falta de dados representativos

**Soluções:**
- Simplificar o modelo
- Aplicar técnicas de regularização (L1, L2, Dropout)
- Usar validação cruzada para monitorar e evitar o sobreajuste
- Aumentar o conjunto de dados de treinamento
- Aplicar técnicas de aumento de dados (data augmentation)
- Early stopping (interromper o treinamento quando o desempenho no conjunto de validação começar a piorar)
- Poda (pruning) de árvores de decisão ou redes neurais

### 3.3 Equilíbrio Viés-Variância
O desafio central no treinamento de modelos é encontrar o equilíbrio entre viés e variância:

- **Viés alto** (subajuste): o modelo faz suposições muito fortes, simplificando demais a realidade
- **Variância alta** (sobreajuste): o modelo é muito sensível às flutuações nos dados de treinamento

Um bom modelo encontra o equilíbrio ótimo entre esses dois extremos, sendo capaz de generalizar bem para novos dados sem sacrificar sua capacidade de aprender padrões importantes.

## 4. Técnicas de Validação de Modelos

### 4.1 Validação Cruzada (Cross-Validation)
A validação cruzada é uma técnica robusta para avaliar o desempenho de um modelo, especialmente útil quando os dados são limitados.

#### 4.1.1 k-fold Cross-Validation
- Divide os dados em k subconjuntos (folds) de tamanho aproximadamente igual
- Treina o modelo k vezes, cada vez usando k-1 folds para treinamento e 1 fold para teste
- O resultado final é a média dos k desempenhos obtidos
- Comum usar k=5 ou k=10, dependendo do tamanho do conjunto de dados

#### 4.1.2 Leave-One-Out Cross-Validation (LOOCV)
- Caso especial onde k é igual ao número de exemplos no conjunto de dados
- Cada exemplo é usado individualmente como teste, enquanto todos os outros são usados para treino
- Computacionalmente caro para grandes conjuntos de dados, mas útil para conjuntos pequenos

#### 4.1.3 Stratified k-fold Cross-Validation
- Variação do k-fold que preserva a proporção de classes em cada fold
- Importante para conjuntos de dados desbalanceados

### 4.2 Hold-out Validation
- Divide os dados em conjuntos de treinamento, validação e teste
- Tipicamente 60-80% para treinamento, 10-20% para validação e 10-20% para teste
- Mais simples que a validação cruzada, mas menos robusta

### 4.3 Bootstrap
- Gera múltiplos conjuntos de dados de treinamento por amostragem com reposição
- Útil para estimar a variabilidade do modelo e calcular intervalos de confiança

### 4.4 Learning Curves
- Gráficos que mostram o desempenho do modelo à medida que o tamanho do conjunto de treinamento aumenta
- Útil para diagnosticar problemas de subajuste ou sobreajuste
- Ajuda a determinar se mais dados de treinamento seriam benéficos

### 4.5 Validation Curves
- Mostra o desempenho do modelo variando um hiperparâmetro específico
- Ajuda a escolher valores ótimos para hiperparâmetros

### 4.6 Grid Search e Random Search
- Técnicas de otimização de hiperparâmetros
- Grid Search: testa todas as combinações possíveis de valores de hiperparâmetros
- Random Search: testa combinações aleatórias, mais eficiente para espaços grandes

## 5. Algoritmo k-Nearest Neighbors (k-NN)

### 5.1 Funcionamento do k-NN
1. Armazena todos os exemplos de treinamento
2. Para classificar um novo exemplo:
   - Calcula a distância entre o novo exemplo e todos os exemplos de treinamento
   - Seleciona os k exemplos mais próximos (vizinhos)
   - A classe mais comum entre esses vizinhos é atribuída ao novo exemplo

### 5.2 Parâmetros importantes do k-NN
- **Valor de k**: Número de vizinhos a considerar
  - k pequeno: modelo mais sensível a ruído, alta variância
  - k grande: fronteiras de decisão mais suaves, maior viés
  - k ímpar é recomendado para problemas binários para evitar empates
  
- **Função de distância**: Como a similaridade é medida
  - Distância Euclidiana: mais comum, funciona bem para espaços contínuos
  - Distância de Manhattan: útil quando as dimensões têm significados diferentes
  - Distância de Minkowski: generalização das distâncias Euclidiana e Manhattan
  - Distância de Hamming: para dados categóricos

- **Ponderação de distância**: Como os vizinhos influenciam a decisão
  - Uniforme: todos os vizinhos têm o mesmo peso
  - Ponderada: vizinhos mais próximos têm maior influência na decisão final

### 5.3 Vantagens e Desvantagens do k-NN

**Vantagens:**
- Simples de implementar e entender
- Não faz suposições sobre a distribuição dos dados
- Eficaz para conjuntos de dados pequenos
- Naturalmente multi-classe

**Desvantagens:**
- Computacionalmente caro para grandes conjuntos de dados
- Sensível a features irrelevantes e à escala das features
- Requer normalização dos dados
- Sofre da "maldição da dimensionalidade" (em espaços de alta dimensão, a noção de "vizinhança" perde significado)

## 6. Métricas de Avaliação para Classificação

Para verificar a qualidade dos modelos de classificação, incluindo k-NN, são usadas diversas métricas:

- **Acurácia**: Proporção de previsões corretas
- **Precisão**: Proporção de verdadeiros positivos entre todos os classificados como positivos
- **Recall (Sensibilidade)**: Proporção de verdadeiros positivos identificados corretamente
- **F1-Score**: Média harmônica entre precisão e recall
- **Área sob a curva ROC (AUC-ROC)**: Mede a capacidade do modelo de distinguir entre classes
- **Matriz de Confusão**: Tabela que visualiza verdadeiros/falsos positivos/negativos
