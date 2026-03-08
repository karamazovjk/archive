# Python para Análise de Dados — Guia de Referência
> Guia de estudos, foco em leitura, compreensão e correção de código.
---

## Glossário Rápido

| Termo | Significado |
|-------|-------------|
| `DataFrame` | Tabela (linhas + colunas) do pandas |
| `Series` | Uma única coluna do pandas |
| `NaN` | Valor nulo / ausente |
| `dtype` | Tipo de dado (int, float, str, datetime...) |
| `index` | O "número da linha" do DataFrame (não necessariamente 0,1,2...) |
| `feature` | Variável de entrada de um modelo (coluna preditora) |
| `target` | O que o modelo tenta prever |
| `overfitting` | Modelo que decorou o treino mas não generaliza |
| `pipeline` | Sequência encadeada de transformações + modelo |
| `EDA` | Exploratory Data Analysis — exploração inicial dos dados |


## 1. As Bibliotecas que Você Vai Ver em Todo Lugar

```python
import pandas as pd        # manipulação de tabelas (a mais importante)
import numpy as np         # operações numéricas e arrays
import matplotlib.pyplot as plt   # gráficos básicos
import seaborn as sns      # gráficos estatísticos mais bonitos
import plotly.express as px       # gráficos interativos
from sklearn import ...    # machine learning
```

> **Regra prática:** se o código importa `pd`, ele está manipulando tabelas. Se importa `sklearn`, está fazendo algum modelo preditivo.

---

## 2. Pandas — O Coração da Análise de Dados

### O que é um DataFrame
É basicamente uma tabela Excel dentro do Python. Linhas = registros, colunas = variáveis.

```python
df = pd.read_csv("arquivo.csv")     # lê um CSV
df = pd.read_excel("arquivo.xlsx")  # lê um Excel
df.head()        # mostra as 5 primeiras linhas
df.tail(10)      # mostra as últimas 10 linhas
df.shape         # (quantidade de linhas, quantidade de colunas)
df.dtypes        # tipo de cada coluna (int, float, object, datetime...)
df.info()        # resumo geral: tipos + valores nulos
df.describe()    # estatísticas básicas: média, min, max, desvio padrão
```

### Selecionando dados

```python
df["coluna"]                    # seleciona uma coluna → retorna Series
df[["col1", "col2"]]            # seleciona múltiplas colunas → retorna DataFrame
df.loc[0]                       # seleciona linha pelo índice (label)
df.iloc[0]                      # seleciona linha pela posição numérica
df.loc[df["idade"] > 30]        # filtra linhas onde idade > 30
df[df["cidade"] == "São Paulo"] # filtra por valor exato
```

### Limpeza de dados (o que você mais vai corrigir)

```python
df.isnull().sum()               # conta valores nulos por coluna
df.dropna()                     # remove linhas com qualquer valor nulo
df.fillna(0)                    # substitui nulos por 0
df.fillna(df["col"].mean())     # substitui nulos pela média da coluna
df.drop_duplicates()            # remove linhas duplicadas
df.drop(columns=["col"])        # remove uma coluna
df.rename(columns={"old": "new"})  # renomeia coluna
df["col"] = df["col"].astype(int)  # converte tipo da coluna
df["col"] = df["col"].str.strip()  # remove espaços em branco (texto)
df["col"] = df["col"].str.lower()  # converte texto para minúsculas
```

### Transformações comuns

```python
df["col_nova"] = df["col1"] + df["col2"]       # cria coluna nova
df["col"] = df["col"].apply(lambda x: x * 2)   # aplica função em cada valor
df.groupby("categoria")["valor"].sum()          # soma agrupada por categoria
df.groupby("categoria").agg({"valor": "mean", "qtd": "sum"})  # múltiplas agregações
df.sort_values("coluna", ascending=False)       # ordena decrescente
df.merge(df2, on="id", how="left")              # join com outro DataFrame
pd.concat([df1, df2])                           # empilha dois DataFrames
df.pivot_table(values="valor", index="mes", columns="produto", aggfunc="sum")
```

### O que é `lambda` (vai aparecer muito)

```python
# lambda é uma função anônima de uma linha
# Em vez de escrever:
def dobrar(x):
    return x * 2

# Você escreve:
lambda x: x * 2

# Exemplo real:
df["categoria"] = df["valor"].apply(lambda x: "alto" if x > 1000 else "baixo")
```

---

## 3. NumPy — Quando os Dados São Números

Pandas usa NumPy por baixo. Você vai ver NumPy principalmente para operações matemáticas.

```python
arr = np.array([1, 2, 3, 4, 5])
arr.mean()    # média
arr.std()     # desvio padrão
arr.max()     # máximo
np.zeros(5)   # array de zeros: [0. 0. 0. 0. 0.]
np.nan        # representa "não é um número" — o NaN que aparece em dados faltantes
np.where(arr > 3, "alto", "baixo")  # if/else vetorizado
```

> **Dica:** quando você ver `NaN` num DataFrame, é um `np.nan` por baixo. São a mesma coisa.

---

## 4. Visualização — Ler Gráficos no Código

### Matplotlib (base de tudo)

```python
plt.figure(figsize=(10, 6))     # tamanho do gráfico em polegadas
plt.plot(x, y)                  # linha
plt.bar(x, y)                   # barras
plt.scatter(x, y)               # dispersão
plt.hist(df["col"], bins=20)    # histograma
plt.title("Título")
plt.xlabel("Eixo X")
plt.ylabel("Eixo Y")
plt.show()                      # renderiza o gráfico
```

### Seaborn (mais legível para análise estatística)

```python
sns.histplot(df["coluna"])                          # distribuição
sns.boxplot(x="categoria", y="valor", data=df)     # boxplot por categoria
sns.heatmap(df.corr(), annot=True)                 # mapa de correlação entre colunas
sns.scatterplot(x="col1", y="col2", hue="cat", data=df)  # dispersão colorida por categoria
sns.barplot(x="mes", y="vendas", data=df)           # barras com intervalo de confiança
```

### Plotly (gráficos interativos — comum em dashboards)

```python
fig = px.line(df, x="data", y="valor", title="Evolução")
fig = px.bar(df, x="categoria", y="total", color="regiao")
fig.show()     # abre no navegador ou no notebook
```

---

## 5. Estruturas Python que Aparecem em Código de Dados

### List comprehension

```python
# Em vez de:
resultado = []
for x in lista:
    resultado.append(x * 2)

# Você vai ver:
resultado = [x * 2 for x in lista]

# Com condição:
pares = [x for x in lista if x % 2 == 0]
```

### Dicionários (muito usados para configuração de modelos)

```python
params = {
    "n_estimators": 100,
    "max_depth": 5,
    "random_state": 42
}
params["n_estimators"]   # acessa valor pela chave
```

### f-strings (formatação de texto)

```python
nome = "Jack"
valor = 3.14159
print(f"Olá {nome}, o valor é {valor:.2f}")
# Saída: "Olá Jack, o valor é 3.14"
```

---

## 6. Erros Comuns que Você Vai Debugar

| Erro | O que significa | Como resolver |
|------|-----------------|---------------|
| `KeyError: 'coluna'` | A coluna não existe no DataFrame | Verificar `df.columns` para ver o nome exato |
| `ValueError: cannot convert float NaN` | Tem NaN onde não deveria | Usar `df.dropna()` ou `df.fillna()` antes |
| `TypeError: unsupported operand type` | Operação entre tipos incompatíveis (ex: somar texto com número) | Verificar `df.dtypes` e converter com `.astype()` |
| `IndexError: list index out of range` | Tentando acessar posição que não existe | Verificar o tamanho da lista/array |
| `SettingWithCopyWarning` | Tentando modificar uma cópia do DataFrame | Usar `.copy()` ou `.loc[]` explicitamente |

---

## 7. Estrutura Típica de um Script de Análise

Quando você abrir um arquivo `.py` ou notebook `.ipynb` de análise, provavelmente vai encontrar essa sequência:

```python
# 1. Imports
import pandas as pd
import matplotlib.pyplot as plt

# 2. Carregamento dos dados
df = pd.read_csv("dados.csv")

# 3. Exploração inicial
df.head()
df.info()
df.describe()

# 4. Limpeza
df = df.dropna(subset=["coluna_importante"])
df["data"] = pd.to_datetime(df["data"])

# 5. Transformações / Feature Engineering
df["ano"] = df["data"].dt.year
df["ticket_medio"] = df["receita"] / df["qtd_vendas"]

# 6. Análise / Agrupamentos
resumo = df.groupby("regiao")["receita"].sum().reset_index()

# 7. Visualização
sns.barplot(x="regiao", y="receita", data=resumo)
plt.title("Receita por Região")
plt.show()

# 8. (Opcional) Exportar resultado
resumo.to_csv("resumo_regioes.csv", index=False)
```

---

## 8. Conceitos de Machine Learning (para reconhecer, não decorar)

Quando o código usa `sklearn`, vai seguir sempre o mesmo padrão:

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression   # ou outro modelo
from sklearn.metrics import mean_squared_error

# Separar features (X) do target (y)
X = df[["col1", "col2", "col3"]]   # variáveis preditoras
y = df["resultado"]                 # o que quer prever

# Separar treino e teste
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Treinar o modelo
model = LinearRegression()
model.fit(X_train, y_train)

# Avaliar
predictions = model.predict(X_test)
print(mean_squared_error(y_test, predictions))
```

**Modelos que você vai ver com frequência:**
- `LinearRegression` — prever valores numéricos contínuos
- `LogisticRegression` — classificação binária (sim/não)
- `RandomForestClassifier / Regressor` — modelo mais robusto, muito usado
- `XGBClassifier` (da lib `xgboost`) — favorito em competições de dados
- `KMeans` — agrupamento sem rótulo (clustering)

---

## 9. Jupyter Notebooks — O Ambiente Mais Comum

Notebooks `.ipynb` são divididos em células. Cada célula pode ser código ou texto (Markdown). O estado é compartilhado — se você rodou uma célula que criou `df`, todas as células seguintes enxergam esse `df`.

**Atalhos essenciais:**
- `Shift + Enter` — executa célula e avança
- `Ctrl + Enter` — executa célula sem avançar
- `Esc + A` — nova célula acima
- `Esc + B` — nova célula abaixo
- `Esc + D D` — deleta célula

**Armadilha comum:** rodar células fora de ordem. Se o código quebra sem motivo aparente, tente `Restart Kernel & Run All` para rodar tudo do zero na sequência certa.

---

