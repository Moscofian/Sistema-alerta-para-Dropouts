# Artigo — Correções da minha parte (Seção 3.3: Data Wrangling e Feature Engineering)

> Checklist do que ajustar no `.docx`. Minha parte = Feature Engineering + Balanceamento
> (Seção 3.3, com gancho na EDA 3.2 e no balanceamento da 3.4).

---

## 🔴 Erros no texto a corrigir

### Erro 1 — Diz "LabelEncoder", mas o código usa OneHotEncoder

**Texto atual (errado):**
> "realizou-se a codificação das variáveis categóricas por meio do algoritmo **LabelEncoder**,
> convertendo os atributos textuais em **representações numéricas sequenciais**..., tornando o
> processamento mais rápido."

Problemas: o código usa `OneHotEncoder`, não `LabelEncoder`; e "representações numéricas
sequenciais" descreve o LabelEncoder (0,1,2,3...), o oposto do OneHot.

**Trocar por:**
> "realizou-se a codificação das variáveis categóricas por meio do **OneHotEncoder**, que converte
> cada categoria textual em **colunas binárias (0 ou 1)**. Optou-se por essa técnica em vez da
> codificação ordinal para **não introduzir uma ordem artificial** entre categorias sem hierarquia
> (ex.: profissão dos pais, motivo de escolha da escola)."

### Erro 2 — "StandardScaler(with_mean=False)" está errado

**Texto atual (errado):**
> "aplicou-se o método de padronização **StandardScaler(with_mean=False)**."

O código usa `StandardScaler()` no padrão (with_mean=True).

**Trocar por:**
> "aplicou-se o método de padronização **StandardScaler**, que centraliza os dados na média 0 e
> desvio-padrão 1."

### Erro 3 — Ordem das etapas (impreciso)

**Texto atual:**
> "três etapas interdependentes: **codificação categórica, mitigação de viés e a normalização de escala**."

Ordem real no código: (1) remoção de vazamento (drop das colunas) -> (2) codificação e padronização
juntas dentro do ColumnTransformer.

**Trocar por:**
> "a preparação seguiu duas frentes: primeiro a **remoção de variáveis com vazamento de dados**;
> em seguida, **codificação e padronização aplicadas simultaneamente** via ColumnTransformer."

### ✅ O que já está certo (não mexer)
- Justificativa do data leakage (remoção de `Final_Grade` e `Grade_2` por revelarem o desfecho).
- Gancho do balanceamento (`class_weight`) nas seções 3.2 e 3.4.

---

## 📊 Gráficos para inserir na minha parte

O texto descreve, mas não mostra nenhum gráfico. Inserir dois:

1. **Barras do desbalanceamento** (549 Não Evasão × 100 Evasão) — `Projeto-Dropout.ipynb`, célula 11.
   Justifica por que o balanceamento foi necessário.
2. **Histograma da Nota Final** com o pico de zeros — célula 12.
   Justifica a remoção das colunas de nota (data leakage). Legendar os zeros como
   "alunos que saíram no meio do ano".

> Obs.: os dois já estão prontos nos slides 8 e 9 — basta exportar a mesma imagem.

---

## 💻 Trechos de código para inserir (como "figuras de código")

Citar que são do `Projeto-Dropout.ipynb`.

**Código 1 — Remoção do vazamento (célula 19):**
```python
X = df.drop(columns=['Dropped_Out', 'Final_Grade', 'Grade_2'])
y = df['Dropped_Out']
```

**Código 2 — Codificação + Padronização (célula 20):**
```python
preprocess = ColumnTransformer(transformers=[
    ('cat', OneHotEncoder(handle_unknown='ignore'), cat_cols),
    ('num', StandardScaler(), num_cols)
])
```

**Código 3 — Balanceamento (célula 22):**
```python
LogisticRegression(class_weight='balanced', ...)
```

---

## Resumo (ordem de prioridade)
1. [ ] Corrigir LabelEncoder -> OneHotEncoder (Erro 1)
2. [ ] Remover "(with_mean=False)" (Erro 2)
3. [ ] Ajustar ordem das etapas (Erro 3)
4. [ ] Inserir gráfico do desbalanceamento
5. [ ] Inserir histograma da nota final (zeros)
6. [ ] Inserir os 3 trechos de código
