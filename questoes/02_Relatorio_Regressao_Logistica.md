# Relatório Técnico - Projeto 2: Regressão Logística para Previsão de Doenças Cardíacas

**Universidade:** Universidade Federal Rural do Semi-Árido (UFERSA)   
**Disciplina:** PAM0466 – Sistemas Inteligentes  
**Docente:** Prof. Dr. Pedro Thiago Valério de Souza  
**Discente:** Nattan Ferreira Lopes  

---

## 1. Contextualização e Enunciado do Problema

> *"Doenças cardiovasculares são a principal causa de morte no mundo, responsáveis por cerca de 18 milhões de óbitos por ano segundo a OMS. O diagnóstico precoce é determinante para o sucesso do tratamento. Existe, portanto, uma necessidade real de ferramentas capazes de estimar o risco de um paciente com base em dados clínicos simples, coletados em consulta de rotina. É nesse contexto que o aprendizado de máquina pode contribuir. Um modelo de classificação treinado com dados históricos de pacientes diagnosticados é capaz de aprender quais combinações de variáveis clínicas estão associadas à presença de doença cardíaca e aplicar esse aprendizado a novos pacientes.*  
> *Neste projeto, você é convidado a construir um modelo utilizando regressão logística para prever a presença de doença cardíaca com base em um conjunto de dados clínicos.*  
> *O projeto utiliza o dataset Heart Disease Cleveland (UCI Machine Learning Repository), com registros de 303 pacientes e 13 variáveis clínicas: age, sex, cp, trestbps, chol, fbs, restecg, thalach, exang, oldpeak, slope, ca e thal. A saída é a variável target, que indica o diagnóstico original de 0 a 4, binarizado para 0 (sem doença) e 1 (com doença) antes do treinamento."*

---

## 2. Pré-Processamento dos Dados e Metodologia

O pré-processamento estruturou as variáveis clínicas conforme os requisitos metodológicos:

1. **Tratamento de Valores Ausentes:** Identificaram-se 6 ocorrências de valores não computados (representados pelo caractere `?` na base original: 4 no atributo `ca` e 2 em `thal`). Como representam menos de 2% do total de registros, os valores foram imputados por meio da mediana das respectivas colunas numéricas.
2. **Binarização da Variável Alvo (`target`):**
   - Classe 0 $\to$ **0 (Sem doença cardíaca / Sadio)**: 164 pacientes (54,1%).
   - Classes 1, 2, 3 e 4 $\to$ **1 (Com presença de doença cardíaca / Cardiopata)**: 139 pacientes (45,9%).
3. **Divisão Estratificada Treino/Teste:** O dataset foi particionado em **80% para treino** (242 amostras) e **20% para teste** (61 amostras). Empregou-se estratificação amostral (`stratify=y` com `random_state=42`) para preservar rigorosamente a proporção das classes entre ambos os conjuntos.
4. **Padronização dos Atributos:** Aplicou-se o escalonador `StandardScaler` (média zero e variância unitária) sobre os 13 preditores contínuos e categóricos codificados, com parâmetros ajustados no conjunto de treino.

---

## 3. Resolução das Tarefas

---

### Tarefa 1: Treinamento do Modelo e Análise de Convergência

> **Enunciado:** *"Treinar um modelo de LogisticRegression do scikit-learn utilizando os dados de treino, com os seguintes parâmetros: solver='liblinear', random_state=42 e max_iter=1000. Indique quantas iterações foram necessárias para a convergência do modelo."*

O modelo de regressão logística foi instanciado e treinado conforme os parâmetros estabelecidos:

```python
modelo_log = LogisticRegression(solver='liblinear', random_state=42, max_iter=1000)
modelo_log.fit(X_train_scaled, y_train)
```

**Iterações para Convergência:**  
O modelo convergiu em exatamente **5 iterações**, valor obtido pelo atributo `modelo_log.n_iter_[0]`.  
A convergência célere frente ao teto de 1.000 iterações decorre da estabilidade numérica conferida pela padronização prévia dos dados e pela convexidade estrita da função de custo da perda logística binária (*binary cross-entropy*).

---

### Tarefa 2: Avaliação de Desempenho no Conjunto de Teste

> **Enunciado:** *"Avaliar o modelo utilizando os dados de teste e calcular as seguintes métricas: Acurácia, Precisão, Recall, F1-score e Curva ROC com valor de AUC."*

O desempenho preditivo computado sobre as 61 instâncias de teste apresentou os seguintes resultados:

| Métrica de Desempenho | Valor Obtido | Significado Teórico / Clínico |
| :--- | :---: | :--- |
| **Acurácia** | **86,89%** | Proporção total de acertos diagnósticos (53 predições corretas em 61 pacientes). |
| **Precisão** | **81,25%** | Dentre os pacientes previstos com cardiopatia, 81,25% de fato possuíam a enfermidade. |
| **Recall (Sensibilidade)** | **92,86%** | Dentre todos os indivíduos verdadeiramente cardiopatas, o modelo identificou **92,86%**. |
| **F1-Score** | **0,8667** | Média harmônica entre precisão e sensibilidade, expressando robustez global. |
| **AUC (Área sob a Curva ROC)** | **0,9513** | Grau de separabilidade probabilística global entre doentes e indivíduos saudáveis. |

#### Matriz de Confusão no Teste:

| Diagnóstico Real \ Previsto | Previsto: Sadio (0) | Previsto: Cardiopata (1) |
| :--- | :---: | :---: |
| **Real: Sadio (0)** | **27** (Verdadeiro Negativo) | **6** (Falso Positivo) |
| **Real: Cardiopata (1)** | **2** (Falso Negativo) | **26** (Verdadeiro Positivo) |

#### Interpretação Clínica dos Resultados:
Em problemas de triagem médica e apoio ao diagnóstico cardiológico, a minimização de **Falsos Negativos** (indivíduos enfermos liberados sem assistência) é o critério de maior criticidade, o que confere ao **Recall (Sensibilidade)** o papel de métrica primordial. O classificador alcançou **92,86% de Recall**, deixando de apontar apenas 2 pacientes em 28 casos patológicos no conjunto de teste, ao passo que a métrica **AUC-ROC de 0,9513** confirma um discriminador de excelente calibração estocástica.
