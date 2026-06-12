# Slide: Feature Engineering & Balanceamento

> Parte do Paulo na apresentação. Substitui o slide 10 (template em branco "Look at these Equations").

---

## 1. Conteúdo do slide

**Título:** `FEATURE ENGINEERING & BALANCEAMENTO`

**Subtítulo:** `Preparando os dados para o modelo`

### Coluna esquerda — 3 tópicos

**1. Codificação Categórica → OneHotEncoder**
Transforma variáveis de texto (gênero, profissão dos pais, endereço) em colunas numéricas 0/1, sem criar ordem falsa entre categorias.

**2. Padronização → StandardScaler**
Coloca variáveis de escalas diferentes (idade × faltas) na mesma escala, evitando que números maiores dominem o modelo.

**3. Balanceamento → `class_weight='balanced'`**
Como só ~15% dos alunos evadem, dá mais peso aos erros na classe minoritária (Evasão), em vez de criar dados artificiais.

### Coluna direita — código

> Trecho retirado do notebook `Projeto-Dropout.ipynb`, **célula 20** (codificação + padronização).

```python
# Codificação (texto->número) + Padronização (mesma escala)
num_cols = X.select_dtypes(include=[np.number]).columns
cat_cols = [c for c in X.columns if c not in num_cols]

preprocess = ColumnTransformer(transformers=[
    ('cat', OneHotEncoder(handle_unknown='ignore'), cat_cols),
    ('num', StandardScaler(), num_cols)
])
```

> Trecho do notebook `Projeto-Dropout.ipynb`, **célula 22** (balanceamento dentro do modelo).

```python
# Balanceamento dentro do modelo
LogisticRegression(class_weight='balanced', ...)
```

---

## 2. O que falar (≈1min30)

> "Depois da análise exploratória, preparamos os dados em três etapas.
>
> **Primeiro, codificação categórica.** O modelo só entende números, não texto. Colunas como gênero,
> profissão dos pais e endereço são texto, então usamos o **OneHotEncoder**, que cria uma coluna 0/1
> para cada categoria. Escolhemos ele em vez de só numerar as categorias porque numerar criaria uma
> **ordem falsa** — como se uma profissão fosse 'maior' que outra.
>
> **Segundo, padronização.** As variáveis numéricas têm escalas bem diferentes: idade fica entre 15 e
> 22, mas faltas podem passar de 30. Sem ajustar, a Regressão Logística daria peso demais às variáveis
> de valor maior. O **StandardScaler** coloca tudo na mesma escala. Esses dois passos ficam dentro de
> um **ColumnTransformer**, que aplica cada transformação na coluna certa.
>
> **Terceiro, o balanceamento.** Como mostramos antes, só 15% dos alunos evadem. Em vez de inventar
> dados artificiais, usamos o **`class_weight='balanced'`**: ele faz o modelo **penalizar mais os erros
> na classe de evasão**, ou seja, deixar de identificar um aluno em risco 'pesa' mais. Assim o modelo
> aprende a prestar atenção justamente nos casos que mais importam."

---

## 3. COLA (levar na mão)

```
FEATURE ENGINEERING & BALANCEAMENTO

1. OneHotEncoder  -> texto vira coluna 0/1 (não cria ordem falsa)
2. StandardScaler -> idade x faltas na mesma escala
3. ColumnTransformer -> aplica cada transformação na coluna certa
4. class_weight='balanced' -> +peso na classe Evasão (15%), sem dados falsos

Frase de fecho: "errar um aluno em risco pesa mais p/ o modelo"
```

---

## ⚠️ Atenção

Apresentar **OneHotEncoder + StandardScaler** (é o que o código faz).
O artigo escreveu "LabelEncoder" e "StandardScaler(with_mean=False)" por engano — pedir ao grupo para corrigir o texto.
