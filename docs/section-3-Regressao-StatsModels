# Aprendendo Regressão Linear com Statsmodels: Um Guia Prático

Este guia apresenta o processo completo de análise de regressão linear utilizando a biblioteca Statsmodels em Python, desde a exploração inicial dos dados até a interpretação final do modelo.

## 1. Introdução ao Statsmodels

Statsmodels é uma biblioteca Python que fornece classes e funções para a estimativa de diferentes modelos estatísticos, realização de testes estatísticos e exploração de dados. Para regressão linear, oferece uma implementação robusta com foco em análise estatística detalhada.

### Principais vantagens do Statsmodels:
- Estatísticas detalhadas sobre o modelo
- Testes de diagnóstico para validar pressupostos
- Uso de fórmulas similares à linguagem R
- Foco na interpretação estatística

## 2. O Processo de Modelagem com Statsmodels

### 2.1 Preparação do Ambiente

```python
# Importações básicas
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Importações específicas do statsmodels
import statsmodels.api as sm
from statsmodels.formula.api import ols
```

### 2.2 Exploração e Visualização dos Dados

Esta fase é crucial para entender os dados antes de modelá-los:

1. **Visualizar estatísticas descritivas**:
   - Entender distribuições, médias, medianas
   - Identificar valores extremos

2. **Criar matriz de correlação**:
   - Visualizar com mapa de calor
   - Identificar variáveis fortemente correlacionadas

3. **Examinar relações entre variáveis**:
   - Gráficos de dispersão entre a variável dependente e cada variável independente
   - Identificar padrões lineares ou não-lineares

```python
# Exemplo de código para correlação e visualização
corr = base.corr()
sns.heatmap(corr, cmap='coolwarm', annot=True, fmt='.2f')
plt.title('Matriz de Correlação')
plt.show()

# Criando gráficos de dispersão para visualizar relações
for col in predictors:
    plt.figure(figsize=(8, 6))
    sns.scatterplot(x=col, y='variavel_alvo', data=base)
    plt.title(f'Relação entre {col} e variável alvo')
    plt.show()
```

### 2.3 Construção do Modelo

Statsmodels oferece duas abordagens principais para criar modelos de regressão:

#### Método 1: Usando fórmulas (recomendado para iniciantes)
```python
# Criação do modelo usando fórmulas estilo R
modelo = ols(formula='y ~ x1 + x2 + x3', data=base)
resultado = modelo.fit()
```

#### Método 2: Usando matrizes
```python
# Adicionando constante (intercepto) manualmente
X = sm.add_constant(base[['x1', 'x2', 'x3']])
y = base['y']
modelo = sm.OLS(y, X)
resultado = modelo.fit()
```

### 2.4 Interpretação do Resumo do Modelo

O método `summary()` do Statsmodels fornece um relatório estatístico completo:

```python
print(resultado.summary())
```

O relatório gerado contém informações valiosas:

**Seção superior:**
- R-squared: Proporção da variância explicada (0-1)
- Adj. R-squared: R² ajustado pelo número de variáveis
- F-statistic: Testa se pelo menos uma variável independente é significativa
- Prob (F-statistic): Valor p para o teste F
- AIC/BIC: Critérios de informação (menores valores são melhores)

**Seção central (coeficientes):**
- coef: Valores dos coeficientes estimados
- std err: Erro padrão de cada coeficiente
- t: Estatística t para cada coeficiente
- P>|t|: Valor p para cada coeficiente
- [0.025 0.975]: Intervalo de confiança de 95% para cada coeficiente

**Seção inferior:**
- Testes de diagnóstico e condições do modelo

### 2.5 Diagnóstico dos Pressupostos

Para uma regressão linear válida, é importante verificar os pressupostos:

#### Normalidade dos Resíduos
```python
# Obter resíduos
residuos = resultado.resid

# Histograma dos resíduos
plt.figure(figsize=(8, 6))
plt.hist(residuos, bins=15)
plt.title('Histograma dos Resíduos')
plt.xlabel('Resíduos')
plt.ylabel('Frequência')
plt.show()

# Q-Q plot
import scipy.stats as stats
plt.figure(figsize=(8, 6))
stats.probplot(residuos, dist="norm", plot=plt)
plt.title('Q-Q Plot dos Resíduos')
plt.show()

# Teste de Shapiro-Wilk
stat, pval = stats.shapiro(residuos)
print(f'Teste Shapiro-Wilk: estatística={stat:.4f}, p-valor={pval:.4f}')
print(f'Os resíduos {"não " if pval < 0.05 else ""}seguem uma distribuição normal (p-valor = {pval:.4f})')
```

#### Homoscedasticidade (Variância Constante)
```python
# Gráfico de resíduos vs valores ajustados
plt.figure(figsize=(8, 6))
plt.scatter(resultado.fittedvalues, residuos)
plt.axhline(y=0, color='r', linestyle='-')
plt.title('Resíduos vs Valores Ajustados')
plt.xlabel('Valores Ajustados')
plt.ylabel('Resíduos')
plt.show()

# Teste de Breusch-Pagan
from statsmodels.stats.diagnostic import het_breuschpagan
bp_test = het_breuschpagan(residuos, resultado.model.exog)
print(f'Teste Breusch-Pagan: estatística={bp_test[0]:.4f}, p-valor={bp_test[1]:.4f}')
print(f'Os resíduos {"apresentam" if bp_test[1] < 0.05 else "não apresentam"} heteroscedasticidade (p-valor = {bp_test[1]:.4f})')
```

#### Independência dos Resíduos (Ausência de Autocorrelação)
```python
# Gráfico de resíduos vs ordem
plt.figure(figsize=(8, 6))
plt.scatter(range(len(residuos)), residuos)
plt.axhline(y=0, color='r', linestyle='-')
plt.title('Resíduos vs Ordem')
plt.xlabel('Ordem da Observação')
plt.ylabel('Resíduos')
plt.show()

# Teste de Durbin-Watson
from statsmodels.stats.stattools import durbin_watson
dw = durbin_watson(residuos)
print(f'Estatística de Durbin-Watson: {dw:.4f}')
if dw < 1.5:
    print('Possível autocorrelação positiva')
elif dw > 2.5:
    print('Possível autocorrelação negativa')
else:
    print('Não há evidência de autocorrelação')
```

### 2.6 Verificação de Multicolinearidade

```python
# Calcular o VIF (Variance Inflation Factor)
from statsmodels.stats.outliers_influence import variance_inflation_factor

X = resultado.model.exog
vif_data = pd.DataFrame()
vif_data["Variável"] = resultado.model.exog_names
vif_data["VIF"] = [variance_inflation_factor(X, i) for i in range(X.shape[1])]
print(vif_data)
print("VIF > 10 indica multicolinearidade problemática")
```

### 2.7 Comparação e Seleção de Modelos

Statsmodels facilita a criação e comparação de diferentes modelos:

```python
# Criar diferentes modelos
modelo1 = ols('y ~ x1 + x2 + x3', data=base).fit()
modelo2 = ols('y ~ x1 + x2', data=base).fit()
modelo3 = ols('y ~ x1 + x3', data=base).fit()

# Comparar métricas
modelos = {
    'Modelo Completo': modelo1,
    'Modelo x1 + x2': modelo2,
    'Modelo x1 + x3': modelo3
}

# Tabela comparativa
comparacao = pd.DataFrame({
    'R²': [modelo.rsquared for modelo in modelos.values()],
    'R² Ajustado': [modelo.rsquared_adj for modelo in modelos.values()],
    'AIC': [modelo.aic for modelo in modelos.values()],
    'BIC': [modelo.bic for modelo in modelos.values()],
    'RMSE': [np.sqrt(np.mean(modelo.resid**2)) for modelo in modelos.values()]
})

print(comparacao)
```

## 3. Aplicação Prática: Um Exemplo com o Dataset Automobiles

Vamos consolidar o aprendizado com um exemplo prático:

### 3.1 Exploração Inicial

```python
# Carregando o dataset (ex: mtcars)
url = "https://gist.githubusercontent.com/seankross/a412dfbd88b3db70b74b/raw/5f23f993cd87c283ce766e7ac6b329ee7cc2e1d1/mtcars.csv"
base = pd.read_csv(url)

# Estatísticas descritivas
print(base.describe())

# Matriz de correlação
corr = base.corr()
plt.figure(figsize=(10, 8))
sns.heatmap(corr, cmap='coolwarm', annot=True, fmt='.2f')
plt.title('Matriz de Correlação')
plt.show()
```

### 3.2 Visualização de Relações

```python
# Visualizar relações com mpg
predictors = ['wt', 'disp', 'hp']
for pred in predictors:
    plt.figure(figsize=(8, 6))
    sns.scatterplot(x=pred, y='mpg', data=base)
    plt.title(f'Relação entre {pred} e mpg')
    plt.xlabel(pred)
    plt.ylabel('mpg')
    plt.show()
```

### 3.3 Construção do Modelo

```python
# Modelo inicial com todas as variáveis relevantes
modelo = ols('mpg ~ wt + disp + hp', data=base).fit()
print(modelo.summary())
```

### 3.4 Diagnóstico de Resíduos

```python
# Análise dos resíduos
residuos = modelo.resid

plt.figure(figsize=(12, 10))
# Histograma
plt.subplot(2, 2, 1)
plt.hist(residuos, bins=15)
plt.title('Histograma dos Resíduos')
# Q-Q plot
plt.subplot(2, 2, 2)
stats.probplot(residuos, dist="norm", plot=plt)
plt.title('Q-Q Plot')
# Resíduos vs ajustados
plt.subplot(2, 2, 3)
plt.scatter(modelo.fittedvalues, residuos)
plt.axhline(y=0, color='r')
plt.title('Resíduos vs Ajustados')
# Resíduos vs ordem
plt.subplot(2, 2, 4)
plt.scatter(range(len(residuos)), residuos)
plt.axhline(y=0, color='r')
plt.title('Resíduos vs Ordem')
plt.tight_layout()
plt.show()
```

### 3.5 Comparação de Modelos

```python
# Comparar modelos alternativos
modelo_completo = ols('mpg ~ wt + disp + hp', data=base).fit()
modelo_wt = ols('mpg ~ wt', data=base).fit()
modelo_wt_hp = ols('mpg ~ wt + hp', data=base).fit()
modelo_disp_hp = ols('mpg ~ disp + hp', data=base).fit()

# Tabela comparativa
modelos = {
    'Modelo Completo': modelo_completo,
    'Modelo wt': modelo_wt,
    'Modelo wt+hp': modelo_wt_hp,
    'Modelo disp+hp': modelo_disp_hp
}

comparacao = pd.DataFrame({
    'R²': [modelo.rsquared for modelo in modelos.values()],
    'R² Ajustado': [modelo.rsquared_adj for modelo in modelos.values()],
    'AIC': [modelo.aic for modelo in modelos.values()],
    'BIC': [modelo.bic for modelo in modelos.values()],
    'RMSE': [np.sqrt(np.mean(modelo.resid**2)) for modelo in modelos.values()]
})

print(comparacao)
```

### 3.6 Modelo Final e Interpretação

```python
# Selecionar o melhor modelo
modelo_final = ols('mpg ~ wt + hp', data=base).fit()
print(modelo_final.summary())

# Interpretação dos coeficientes
print("\nInterpretação dos coeficientes:")
print(f"Intercepto: {modelo_final.params[0]:.4f}")
print(f"wt: {modelo_final.params[1]:.4f} - Para cada aumento de 1000 libras no peso,")
print(f"    o consumo diminui em {abs(modelo_final.params[1]):.2f} mpg, mantendo hp constante.")
print(f"hp: {modelo_final.params[2]:.4f} - Para cada aumento de 10 HP na potência,")
print(f"    o consumo diminui em {abs(modelo_final.params[2])*10:.2f} mpg, mantendo wt constante.")

# Visualização do modelo
y_pred = modelo_final.predict(base)
plt.figure(figsize=(8, 6))
plt.scatter(base['mpg'], y_pred)
plt.plot([base['mpg'].min(), base['mpg'].max()], 
         [base['mpg'].min(), base['mpg'].max()], 'k--')
plt.xlabel('MPG Observado')
plt.ylabel('MPG Predito')
plt.title('Valores Observados vs Preditos')
plt.show()
```

## 4. Dicas para o Uso Eficiente do Statsmodels

1. **Use fórmulas para modelos iniciais**: A sintaxe com fórmulas é mais intuitiva para iniciantes.

2. **Preste atenção às notas do summary()**: As notas no sumário podem indicar problemas, como multicolinearidade.

3. **Explore os objetos de resultado**: O objeto resultado possui muitos atributos e métodos além do `.summary()`:
   - `.params`: Coeficientes
   - `.pvalues`: Valores p
   - `.conf_int()`: Intervalos de confiança
   - `.predict()`: Fazer previsões

4. **Diagnóstico completo**: Sempre verifique os pressupostos da regressão antes de tirar conclusões.

5. **Interpretação contextualizada**: Interprete os coeficientes no contexto do seu problema, não apenas como números.

## 5. Quando os Pressupostos São Violados

### Normalidade dos Resíduos
- **Se p < 0.05 no teste Shapiro-Wilk**: Os resíduos não seguem distribuição normal
- **Solução**: Transformar a variável dependente (log, raiz quadrada)

### Heteroscedasticidade
- **Se p < 0.05 no teste Breusch-Pagan**: Variância não constante dos resíduos
- **Solução**: Transformação da variável dependente ou usar erros padrão robustos

### Autocorrelação
- **Se DW < 1.5 ou DW > 2.5**: Possível autocorrelação dos resíduos
- **Solução**: Se os dados forem temporais, considerar modelos de séries temporais

### Multicolinearidade
- **Se VIF > 10 para alguma variável**: Possível multicolinearidade severa
- **Solução**: Remover variáveis redundantes ou usar métodos de regularização

## 6. Resumo do Processo de Análise

1. **Explorar os dados** visualmente e com estatísticas descritivas
2. **Visualizar correlações** entre variáveis
3. **Construir um modelo inicial** com variáveis potencialmente relevantes
4. **Avaliar o ajuste do modelo** (R², F-statistic)
5. **Verificar a significância dos coeficientes** (valores p)
6. **Realizar diagnóstico dos pressupostos** (normalidade, homoscedasticidade, etc.)
7. **Refinar o modelo** removendo variáveis não significativas ou com multicolinearidade
8. **Comparar modelos alternativos** (R² ajustado, AIC, BIC)
9. **Selecionar o modelo final** equilibrando simplicidade e poder explicativo
10. **Interpretar os resultados** no contexto do problema em estudo

## 7. Conclusão

O Statsmodels é uma ferramenta poderosa para análise de regressão linear que combina facilidade de uso com informações estatísticas detalhadas. Seguindo o processo estruturado apresentado neste guia, você poderá construir e interpretar modelos de regressão com confiança, entendendo não apenas os resultados, mas também suas limitações e pressupostos.

Lembre-se de que a modelagem é tanto uma arte quanto uma ciência - o objetivo final não é apenas maximizar o R², mas construir um modelo interpretável e útil para o problema em questão.
