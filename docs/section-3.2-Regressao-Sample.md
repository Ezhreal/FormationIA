# Exemplo Prático: Modelando Vendas de E-commerce com Statsmodels

Este exemplo mostra como utilizar o Statsmodels para construir um modelo de regressão linear para prever vendas de uma loja online, com base em diversos fatores como gastos em marketing, preços e métricas de tráfego.

## 1. Preparação do Ambiente e Dados

Primeiro, vamos configurar o ambiente e criar um conjunto de dados simulado para o exemplo:

```python
# Importações necessárias
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import statsmodels.api as sm
from statsmodels.formula.api import ols
from scipy import stats

# Configurações de visualização
sns.set_theme(style="whitegrid")
plt.rcParams['figure.figsize'] = (10, 6)
```

### 1.1 Criação do Dataset Simulado

Vamos simular dados para uma loja online por um período de 100 dias:

```python
# Definir uma semente para reprodutibilidade
np.random.seed(42)

# Número de observações
n = 100

# Variáveis independentes
marketing_spend = np.random.uniform(1000, 5000, n)  # Gastos diários em marketing (R$)
price_discount = np.random.uniform(0, 30, n)        # Desconto no preço (%)
website_traffic = np.random.normal(1500, 300, n)    # Tráfego diário no site (visitantes)
season_effect = np.sin(np.linspace(0, 2*np.pi, n))  # Efeito sazonal

# Índice de dias
dias = pd.date_range(start='2023-01-01', periods=n)

# Criando a variável dependente (vendas) com relações conhecidas e algum ruído
vendas = (
    20                             # Base constante
    + 0.05 * marketing_spend       # Efeito positivo dos gastos em marketing
    + 10 * price_discount          # Efeito positivo dos descontos
    + 0.02 * website_traffic       # Efeito positivo do tráfego
    + 500 * season_effect          # Efeito sazonal
    + np.random.normal(0, 500, n)  # Ruído aleatório
)

# Criar o DataFrame
data = pd.DataFrame({
    'data': dias,
    'vendas': vendas,
    'marketing_spend': marketing_spend,
    'price_discount': price_discount,
    'website_traffic': website_traffic,
    'week_day': dias.dayofweek
})

# Adicionar uma variável categórica para o dia da semana
data['is_weekend'] = (data['week_day'] >= 5).astype(int)

# Exibir as primeiras linhas
print(data.head())
```

## 2. Exploração dos Dados

### 2.1 Estatísticas Descritivas

```python
# Estatísticas descritivas
print("\nEstatísticas descritivas:")
print(data.describe())

# Verificar se há valores ausentes
print("\nValores ausentes por coluna:")
print(data.isnull().sum())
```

### 2.2 Visualização das Distribuições

```python
# Distribuição da variável dependente (vendas)
plt.figure()
sns.histplot(data=data, x='vendas', kde=True)
plt.title('Distribuição das Vendas Diárias')
plt.xlabel('Vendas (R$)')
plt.show()

# Distribuição das variáveis independentes
fig, axes = plt.subplots(1, 3, figsize=(15, 5))

sns.histplot(data=data, x='marketing_spend', kde=True, ax=axes[0])
axes[0].set_title('Distribuição dos Gastos em Marketing')
axes[0].set_xlabel('Gastos (R$)')

sns.histplot(data=data, x='price_discount', kde=True, ax=axes[1])
axes[1].set_title('Distribuição dos Descontos')
axes[1].set_xlabel('Desconto (%)')

sns.histplot(data=data, x='website_traffic', kde=True, ax=axes[2])
axes[2].set_title('Distribuição do Tráfego do Site')
axes[2].set_xlabel('Visitantes')

plt.tight_layout()
plt.show()

# Séries temporais
plt.figure(figsize=(12, 8))
plt.subplot(3, 1, 1)
plt.plot(data['data'], data['vendas'])
plt.title('Vendas ao Longo do Tempo')
plt.ylabel('Vendas (R$)')

plt.subplot(3, 1, 2)
plt.plot(data['data'], data['marketing_spend'])
plt.title('Gastos em Marketing ao Longo do Tempo')
plt.ylabel('Gastos (R$)')

plt.subplot(3, 1, 3)
plt.plot(data['data'], data['price_discount'])
plt.title('Descontos ao Longo do Tempo')
plt.ylabel('Desconto (%)')

plt.tight_layout()
plt.show()

# Vendas por dia da semana
plt.figure()
sns.boxplot(data=data, x='week_day', y='vendas')
plt.title('Vendas por Dia da Semana')
plt.xlabel('Dia da Semana (0=Segunda, 6=Domingo)')
plt.ylabel('Vendas (R$)')
plt.show()
```

### 2.3 Análise de Correlações

```python
# Calcular a matriz de correlação
corr = data.drop(['data', 'week_day'], axis=1).corr()

# Visualizar a matriz de correlação
plt.figure(figsize=(10, 8))
mask = np.triu(np.ones_like(corr, dtype=bool))  # Máscara para o triângulo superior
sns.heatmap(corr, annot=True, fmt='.2f', cmap='coolwarm',
            mask=mask, cbar_kws={'shrink': .8}, vmin=-1, vmax=1)
plt.title('Matriz de Correlação das Variáveis')
plt.show()

# Correlações com a variável dependente (vendas)
correlacoes_vendas = corr['vendas'].sort_values(ascending=False)
print("\nCorrelações com vendas:")
print(correlacoes_vendas)
```

### 2.4 Visualização das Relações com a Variável Dependente

```python
# Gráficos de dispersão para cada variável independente vs. vendas
fig, axes = plt.subplots(2, 2, figsize=(14, 10))

# Marketing vs Vendas
sns.regplot(x='marketing_spend', y='vendas', data=data, ax=axes[0, 0])
axes[0, 0].set_title(f'Vendas vs. Marketing Spend (r = {corr.loc["vendas", "marketing_spend"]:.2f})')

# Desconto vs Vendas
sns.regplot(x='price_discount', y='vendas', data=data, ax=axes[0, 1])
axes[0, 1].set_title(f'Vendas vs. Desconto (r = {corr.loc["vendas", "price_discount"]:.2f})')

# Tráfego vs Vendas
sns.regplot(x='website_traffic', y='vendas', data=data, ax=axes[1, 0])
axes[1, 0].set_title(f'Vendas vs. Tráfego (r = {corr.loc["vendas", "website_traffic"]:.2f})')

# Fim de semana vs Vendas
sns.boxplot(x='is_weekend', y='vendas', data=data, ax=axes[1, 1])
axes[1, 1].set_title('Vendas: Dia de Semana vs. Fim de Semana')
axes[1, 1].set_xlabel('Fim de Semana (0=Não, 1=Sim)')

plt.tight_layout()
plt.show()
```

## 3. Construção e Análise do Modelo

### 3.1 Modelo Inicial com Todas as Variáveis

```python
# Modelo inicial com todas as variáveis
formula = 'vendas ~ marketing_spend + price_discount + website_traffic + is_weekend'
modelo = ols(formula=formula, data=data).fit()

# Exibir o resumo do modelo
print(modelo.summary())
```

### 3.2 Diagnóstico dos Resíduos

```python
# Obter os resíduos
residuos = modelo.resid

# Criar uma figura para os gráficos de diagnóstico
fig, axes = plt.subplots(2, 2, figsize=(14, 10))

# 1. Histograma dos resíduos
sns.histplot(residuos, kde=True, ax=axes[0, 0])
axes[0, 0].set_title('Histograma dos Resíduos')
axes[0, 0].set_xlabel('Resíduos')

# 2. Q-Q plot para verificar normalidade
stats.probplot(residuos, plot=axes[0, 1])
axes[0, 1].set_title('Q-Q Plot dos Resíduos')

# 3. Resíduos vs. valores ajustados
axes[1, 0].scatter(modelo.fittedvalues, residuos)
axes[1, 0].axhline(y=0, color='r', linestyle='-')
axes[1, 0].set_title('Resíduos vs. Valores Ajustados')
axes[1, 0].set_xlabel('Valores Ajustados')
axes[1, 0].set_ylabel('Resíduos')

# 4. Resíduos vs. tempo (ordem das observações)
axes[1, 1].scatter(range(len(residuos)), residuos)
axes[1, 1].axhline(y=0, color='r', linestyle='-')
axes[1, 1].set_title('Resíduos vs. Ordem')
axes[1, 1].set_xlabel('Ordem das Observações')
axes[1, 1].set_ylabel('Resíduos')

plt.tight_layout()
plt.show()

# Testes estatísticos para os pressupostos
# 1. Teste de normalidade (Shapiro-Wilk)
stat, pval = stats.shapiro(residuos)
print(f'\nTeste de Shapiro-Wilk para normalidade:')
print(f'Estatística: {stat:.4f}, p-valor: {pval:.4f}')
if pval < 0.05:
    print('Os resíduos não seguem uma distribuição normal (p < 0.05)')
else:
    print('Não há evidência contra a normalidade dos resíduos (p > 0.05)')

# 2. Teste de heteroscedasticidade (Breusch-Pagan)
from statsmodels.stats.diagnostic import het_breuschpagan
bp_test = het_breuschpagan(residuos, modelo.model.exog)
print(f'\nTeste de Breusch-Pagan para heteroscedasticidade:')
print(f'Estatística: {bp_test[0]:.4f}, p-valor: {bp_test[1]:.4f}')
if bp_test[1] < 0.05:
    print('Há evidência de heteroscedasticidade nos resíduos (p < 0.05)')
else:
    print('Não há evidência de heteroscedasticidade nos resíduos (p > 0.05)')

# 3. Teste de autocorrelação (Durbin-Watson)
from statsmodels.stats.stattools import durbin_watson
dw = durbin_watson(residuos)
print(f'\nEstatística de Durbin-Watson: {dw:.4f}')
if dw < 1.5:
    print('Possível autocorrelação positiva nos resíduos')
elif dw > 2.5:
    print('Possível autocorrelação negativa nos resíduos')
else:
    print('Não há evidência forte de autocorrelação nos resíduos')
```

### 3.3 Verificação de Multicolinearidade

```python
# Verificação de multicolinearidade usando VIF
from statsmodels.stats.outliers_influence import variance_inflation_factor

# Preparar matriz de variáveis independentes
X = sm.add_constant(data[['marketing_spend', 'price_discount', 
                          'website_traffic', 'is_weekend']])

# Calcular VIF para cada variável
vif_data = pd.DataFrame()
vif_data["Variável"] = X.columns
vif_data["VIF"] = [variance_inflation_factor(X.values, i) for i in range(X.shape[1])]
print("\nFator de Inflação da Variância (VIF):")
print(vif_data)
```

### 3.4 Comparação de Modelos Alternativos

```python
# Criar diferentes modelos para comparação
modelo1 = ols('vendas ~ marketing_spend + price_discount + website_traffic + is_weekend', 
             data=data).fit()
modelo2 = ols('vendas ~ marketing_spend + price_discount + website_traffic', 
             data=data).fit()
modelo3 = ols('vendas ~ marketing_spend + price_discount', 
             data=data).fit()
modelo4 = ols('vendas ~ marketing_spend + website_traffic', 
             data=data).fit()

# Armazenar os modelos em um dicionário
modelos = {
    'Completo': modelo1,
    'Sem is_weekend': modelo2,
    'Apenas marketing e desconto': modelo3,
    'Apenas marketing e tráfego': modelo4
}

# Criar DataFrame para comparação
resultados = pd.DataFrame({
    'R²': [modelo.rsquared for modelo in modelos.values()],
    'R² Ajustado': [modelo.rsquared_adj for modelo in modelos.values()],
    'AIC': [modelo.aic for modelo in modelos.values()],
    'BIC': [modelo.bic for modelo in modelos.values()],
    'RMSE': [np.sqrt(np.mean(modelo.resid**2)) for modelo in modelos.values()]
})

# Mostrar resultados da comparação
print("\nComparação de modelos alternativos:")
print(resultados)
```

## 4. Modelo Final e Interpretação

Vamos supor que o modelo com marketing_spend e price_discount tenha sido selecionado como o melhor:

```python
# Selecionar o modelo final
modelo_final = modelos['Apenas marketing e desconto']
print("\nResumo do modelo final:")
print(modelo_final.summary())

# Interpretar os coeficientes
print("\nInterpretação dos coeficientes:")
for var, coef in modelo_final.params.items():
    if var == 'Intercept':
        print(f"Intercepto: {coef:.2f} - Valor base das vendas")
    elif var == 'marketing_spend':
        print(f"marketing_spend: {coef:.4f} - Para cada R$ 1.000 adicional em marketing,")
        print(f"                          as vendas aumentam em R$ {coef*1000:.2f}")
    elif var == 'price_discount':
        print(f"price_discount: {coef:.2f} - Para cada 1% adicional de desconto,")
        print(f"                         as vendas aumentam em R$ {coef:.2f}")
```

### 4.1 Visualização do Modelo Final

```python
# Visualizar valores preditos vs. observados
y_pred = modelo_final.predict(data)
plt.figure(figsize=(10, 6))
plt.scatter(data['vendas'], y_pred, alpha=0.6)
plt.plot([data['vendas'].min(), data['vendas'].max()], 
         [data['vendas'].min(), data['vendas'].max()], 'k--')
plt.title('Valores Observados vs. Preditos')
plt.xlabel('Vendas Observadas (R$)')
plt.ylabel('Vendas Preditas (R$)')
plt.grid(True, linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

# Visualizar o efeito parcial de cada variável
from statsmodels.graphics.regressionplots import plot_ccpr

fig, axes = plt.subplots(1, 2, figsize=(14, 6))

# Efeito do marketing
plot_ccpr(modelo_final, 'marketing_spend', ax=axes[0])
axes[0].set_title('Efeito Parcial do Marketing nas Vendas')
axes[0].set_xlabel('Gastos em Marketing (R$)')
axes[0].set_ylabel('Efeito nas Vendas (R$)')
axes[0].grid(True, linestyle='--', alpha=0.7)

# Efeito do desconto
plot_ccpr(modelo_final, 'price_discount', ax=axes[1])
axes[1].set_title('Efeito Parcial do Desconto nas Vendas')
axes[1].set_xlabel('Desconto (%)')
axes[1].set_ylabel('Efeito nas Vendas (R$)')
axes[1].grid(True, linestyle='--', alpha=0.7)

plt.tight_layout()
plt.show()
```

### 4.2 Exemplo de Cenários para o Negócio

```python
# Criar cenários para fazer previsões
cenarios = pd.DataFrame({
    'marketing_spend': [1500, 2500, 3500, 4500],
    'price_discount': [5, 10, 15, 20]
})

# Descrição dos cenários
cenarios['descricao'] = [
    'Marketing baixo, desconto baixo',
    'Marketing médio, desconto médio',
    'Marketing alto, desconto médio-alto',
    'Marketing muito alto, desconto alto'
]

# Fazer previsões para cada cenário
cenarios['vendas_previstas'] = modelo_final.predict(cenarios)

# Exibir resultados
print("\nPrevisões para diferentes cenários de marketing e desconto:")
print(cenarios[['descricao', 'marketing_spend', 'price_discount', 'vendas_previstas']])

# Visualizar os cenários
plt.figure(figsize=(10, 6))
bars = plt.bar(cenarios['descricao'], cenarios['vendas_previstas'], color='skyblue')
plt.xticks(rotation=45, ha='right')
plt.title('Vendas Previstas para Diferentes Cenários')
plt.ylabel('Vendas Previstas (R$)')
plt.grid(axis='y', linestyle='--', alpha=0.7)

# Adicionar valores nas barras
for bar in bars:
    height = bar.get_height()
    plt.annotate(f'{height:.0f}',
                xy=(bar.get_x() + bar.get_width()/2, height),
                xytext=(0, 3),
                textcoords="offset points",
                ha='center', va='bottom', fontsize=9)
    
plt.tight_layout()
plt.show()
```

### 4.3 Análise de Retorno sobre Investimento (ROI)

```python
# Calcular o ROI para diferentes níveis de investimento em marketing
cenarios_roi = pd.DataFrame({
    'marketing_spend': np.linspace(1000, 5000, 9),  # 9 níveis de investimento
    'price_discount': [15] * 9  # Manter desconto constante (15%)
})

# Calcular vendas previstas
cenarios_roi['vendas_previstas'] = modelo_final.predict(cenarios_roi)

# Calcular ROI (assumindo margem de 30% sobre as vendas)
cenarios_roi['margem_bruta'] = cenarios_roi['vendas_previstas'] * 0.3
cenarios_roi['roi'] = (cenarios_roi['margem_bruta'] - cenarios_roi['marketing_spend']) / cenarios_roi['marketing_spend'] * 100

# Visualizar o ROI
plt.figure(figsize=(10, 6))
plt.plot(cenarios_roi['marketing_spend'], cenarios_roi['roi'], marker='o', linestyle='-')
plt.axhline(y=0, color='r', linestyle='--', alpha=0.7)
plt.title('ROI por Nível de Investimento em Marketing')
plt.xlabel('Gastos em Marketing (R$)')
plt.ylabel('ROI (%)')
plt.grid(True, linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

# Encontrar o nível ótimo de investimento (maior ROI)
melhor_roi = cenarios_roi.loc[cenarios_roi['roi'].idxmax()]
print("\nNível ótimo de investimento em marketing:")
print(f"Gastos em marketing: R$ {melhor_roi['marketing_spend']:.2f}")
print(f"Vendas previstas: R$ {melhor_roi['vendas_previstas']:.2f}")
print(f"ROI: {melhor_roi['roi']:.2f}%")
```

## 5. Conclusões Finais e Recomendações para o Negócio

```python
# Documentar as conclusões finais
print("\n=== CONCLUSÕES FINAIS E RECOMENDAÇÕES ===")
print("\n1. RESUMO DO MODELO FINAL:")
print(f"• R² = {modelo_final.rsquared:.4f} - O modelo explica {modelo_final.rsquared*100:.1f}% da variância nas vendas diárias")
print(f"• Equação: Vendas = {modelo_final.params['Intercept']:.2f} + {modelo_final.params['marketing_spend']:.4f} × Marketing + {modelo_final.params['price_discount']:.2f} × Desconto")

print("\n2. PRINCIPAIS INSIGHTS:")
print(f"• Cada R$ 1.000 investidos em marketing está associado a um aumento de R$ {modelo_final.params['marketing_spend']*1000:.2f} nas vendas")
print(f"• Cada 1% de desconto está associado a um aumento de R$ {modelo_final.params['price_discount']:.2f} nas vendas")

print("\n3. RECOMENDAÇÕES PRÁTICAS:")
print(f"• Nível ótimo de investimento em marketing: R$ {melhor_roi['marketing_spend']:.2f}")
print(f"• Manter descontos moderados (~15%) para maximizar o retorno")
print("• Monitorar continuamente o desempenho para ajustar a estratégia")

print("\n4. LIMITAÇÕES DO MODELO:")
# (Colocar aqui as limitações identificadas nos testes de pressupostos)
print("• O modelo não captura variações sazonais, que devem ser consideradas separadamente")
print("• Pressuposto de relação linear entre variáveis pode não capturar efeitos de saturação")
print("• Dados históricos podem não refletir precisamente condições de mercado futuras")

print("\n5. PRÓXIMOS PASSOS:")
print("• Integrar dados de concorrentes e fatores macroeconômicos")
print("• Explorar modelos de série temporal para capturar melhor a sazonalidade")
print("• Implementar um sistema de monitoramento contínuo do ROI de marketing")
```

## 6. Aprendizados do Processo

Este exemplo demonstrou o processo completo de análise de regressão linear com Statsmodels em um contexto de e-commerce, mostrando como:

1. **Explorar os dados** para entender padrões e relacionamentos
2. **Criar e comparar modelos** baseados em diferentes conjuntos de variáveis
3. **Avaliar pressupostos estatísticos** para garantir a validade do modelo
4. **Interpretar coeficientes** em um contexto de negócios
5. **Gerar insights acionáveis** para otimização de marketing e precificação

Cada etapa deste processo pode ser adaptada para diferentes contextos de negócios, sempre seguindo a mesma estrutura lógica de análise.
