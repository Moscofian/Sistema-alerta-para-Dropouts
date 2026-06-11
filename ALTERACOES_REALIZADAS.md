# Alterações Realizadas no Notebook

## Resumo
Renomeação de rótulos das classes (True/False → "Evasão"/"Não Evasão") em todo o notebook, correção de avisos de depreciação e ajustes de nomenclatura.

---

## Edições por Célula

### Cell 024c3b6d - Distribuição das evasões

**Antes:**
```python
# Relação entre alunos
print("\nDistribuição das evasões:\n")
ax = sns.countplot(x='Dropped_Out', data=df)
ax.bar_label(ax.containers[0])
plt.title('Distribuição dos alunos')
plt.show()
```

**Depois:**
```python
# Relação entre alunos
print("\nDistribuição das evasões:\n")
situacao = df['Dropped_Out'].map({False: 'Não Evasão', True: 'Evasão'})
ax = sns.countplot(x=situacao, order=['Não Evasão', 'Evasão'])
ax.bar_label(ax.containers[0])
plt.title('Distribuição dos alunos')
plt.xlabel('Situação')
plt.ylabel('Quantidade')
plt.show()
```

---

### Cell 90828e46 - Taxa de evasão por gênero

**Antes:**
```python
# Taxa de evasão por gênero
fig, ax = plt.subplots(figsize=(8, 5))
sns.countplot(data=df, x='Gender', hue='Dropped_Out', ax=ax)

ax.set_title('Taxa de evasão por gênero')
ax.set_xlabel('Gênero')
ax.set_ylabel('Quantidade')

for p in ax.patches:
    ax.annotate(format(p.get_height(), '.0f'), 
                (p.get_x() + p.get_width() / 2., p.get_height()), 
                ha = 'center', va = 'center', 
                xytext = (0, 5), 
                textcoords = 'offset points')

ax.legend(title='Dropped Out')

plt.show()
```

**Depois:**
```python
# Taxa de evasão por gênero
situacao = df['Dropped_Out'].map({False: 'Não Evasão', True: 'Evasão'})

fig, ax = plt.subplots(figsize=(8, 5))
sns.countplot(data=df, x='Gender', hue=situacao, hue_order=['Não Evasão', 'Evasão'], ax=ax)

ax.set_title('Taxa de evasão por gênero')
ax.set_xlabel('Gênero')
ax.set_ylabel('Quantidade')

for p in ax.patches:
    ax.annotate(format(p.get_height(), '.0f'), 
                (p.get_x() + p.get_width() / 2., p.get_height()), 
                ha = 'center', va = 'center', 
                xytext = (0, 5), 
                textcoords = 'offset points')

ax.legend(title='Situação')

plt.show()
```

---

### Cell a454c38e - Evasão por idade

**Antes:**
```python
# Evasão por idade
sns.countplot(data=df, x='Age', hue='Dropped_Out')
plt.title('Evasão por idade')
plt.show()
```

**Depois:**
```python
# Evasão por idade
situacao = df['Dropped_Out'].map({False: 'Não Evasão', True: 'Evasão'})
ax = sns.countplot(data=df, x='Age', hue=situacao, hue_order=['Não Evasão', 'Evasão'])
ax.legend(title='Situação')
plt.title('Evasão por idade')
plt.xlabel('Idade')
plt.ylabel('Quantidade')
plt.show()
```

---

### Cell 9046374e - Evasão por faltas

**Antes:**
```python
# Evasão por faltas
sns.boxplot(data=df, x='Dropped_Out', y='Number_of_Absences')
plt.title('Faltas x Evasão')
plt.show()
```

**Depois:**
```python
# Evasão por faltas
situacao = df['Dropped_Out'].map({False: 'Não Evasão', True: 'Evasão'})
sns.boxplot(x=situacao, y=df['Number_of_Absences'], order=['Não Evasão', 'Evasão'])
plt.title('Faltas x Evasão')
plt.xlabel('Situação')
plt.ylabel('Número de Faltas')
plt.show()
```

---

### Cell 5e309d1d - Relatório de Classificação

**Antes:**
```python
# Relatório completo de métricas
print("\n--- Relatório de Classificação ---")
print(classification_report(y_teste, y_pred))
```

**Depois:**
```python
# Relatório completo de métricas
# target_names segue a ordem das classes: False (Não Evasão) e True (Evasão)
print("\n--- Relatório de Classificação ---")
print(classification_report(y_teste, y_pred, target_names=['Não Evasão', 'Evasão']))
```

---

### Cell 36e8cf47 - Matriz de Confusão

**Antes:**
```python
# Visualizar a Matriz de Confusão
ConfusionMatrixDisplay.from_predictions(y_teste, y_pred, cmap='Blues')
plt.title("Matriz de Confusão")
plt.show()
```

**Depois:**
```python
# Visualizar a Matriz de Confusão
ConfusionMatrixDisplay.from_predictions(
    y_teste, y_pred,
    display_labels=['Não Evasão', 'Evasão'],
    cmap='Blues'
)
plt.title("Matriz de Confusão")
plt.xlabel("Classe Prevista")
plt.ylabel("Classe Real")
plt.show()
```

---

### Cell 325e3e87 - Salvar Modelo

**Antes:**
```python
# Baixar o arquivo do modelo
joblib.dump(melhor_modelo, 'modelo_modelo_evasao.joblib')
```

**Depois:**
```python
# Baixar o arquivo do modelo
joblib.dump(melhor_modelo, 'modelo_evasao.joblib')
```

**Motivo:** Nome duplicado (`modelo_modelo_evasao` → `modelo_evasao`)

---

### Cell 50f32a72 - Carregar Modelo

**Antes:**
```python
# Carrega o modelo a partir do disco
modelo_evasao_deploy = joblib.load('modelo_modelo_evasao.joblib')
```

**Depois:**
```python
# Carrega o modelo a partir do disco
modelo_evasao_deploy = joblib.load('modelo_evasao.joblib')
```

**Motivo:** Sincronizar com o nome corrigido do arquivo

---

### Cell b5fa409f - Função prever_evasao

**Antes:**
```python
def prever_evasao(lista_novos_alunos):
    # ... (resto do código igual)
    previsoes = modelo_evasao_deploy.predict(df_novos)

    evasao = ['Estável (Negativo)' if p == 0 else 'Risco de Evasão (Positivo)' for p in previsoes]

    print("\n--- Resultados da Previsão ---")
    for i, resultado in enumerate(evasao):
        print(f"Aluno {i+1} -> Predição: {resultado}")
```

**Depois:**
```python
def prever_evasao(lista_novos_alunos):
    # ... (resto do código igual)
    previsoes = modelo_evasao_deploy.predict(df_novos)

    evasao = ['Não Evasão' if p == 0 else 'Evasão' for p in previsoes]

    print("\n--- Resultados da Previsão ---")
    for i, resultado in enumerate(evasao):
        print(f"Aluno {i+1} -> Predição: {resultado}")
```

**Motivo:** Consistência com rótulos "Evasão" e "Não Evasão" (removidas as variações "Estável" e "Risco de")

---

### Cell 28dc4a29 - Seleção de Colunas Categóricas (Correção de Aviso)

**Antes:**
```python
# Identifica as colunas categóricas e as númericas
cat_cols = X.select_dtypes(include=['object', 'category']).columns
num_cols = X.select_dtypes(exclude=['object', 'category']).columns

# Preprocessamento dos dados
# Transforma as cols cat em numéricas
# Padroniza as cols numéricas
preprocess = ColumnTransformer(
    transformers=[
        ('cat', OneHotEncoder(handle_unknown='ignore'), cat_cols),
        ('num', StandardScaler(), num_cols)
    ]
)
```

**Depois:**
```python
# Identifica as colunas categóricas e as númericas
num_cols = X.select_dtypes(include=[np.number]).columns
cat_cols = [col for col in X.columns if col not in num_cols]

# Preprocessamento dos dados
# Transforma as cols cat em numéricas
# Padroniza as cols numéricas
preprocess = ColumnTransformer(
    transformers=[
        ('cat', OneHotEncoder(handle_unknown='ignore'), cat_cols),
        ('num', StandardScaler(), num_cols)
    ]
)
```

**Motivo:** Evitar aviso de deprecação do pandas 3.0 ao usar `select_dtypes(include=['object', 'category'])`. A solução é explícita: seleciona numéricas via `np.number` e o resto automaticamente como categóricas.

---

## Resultados Finais

Após reexecução completa do notebook:

- **Relatório de Classificação:**
  ```
                precision    recall  f1-score   support
    Não Evasão       0.97      0.89      0.93       169
        Evasão       0.53      0.81      0.64        26
      accuracy                           0.88       195
     macro avg       0.75      0.85      0.78       195
  weighted avg       0.91      0.88      0.89       195
  ```

- **Resultados da Previsão (10 novos alunos):**
  - Alunos 1, 3, 4, 5, 6, 8, 9, 10 → **Não Evasão**
  - Alunos 2, 7 → **Evasão**

- **Status:** Nenhum aviso de depreciação sobre colunas (o warning do scikit-learn 1.8 sobre `penalty` permanece pois é de escopo maior).

---

## Commit

- **Branch:** `paulo-teste`
- **Commit:** `cf267e9`
- **Mensagem:** "Renomeia rótulos para Evasão/Não Evasão e corrige avisos"
