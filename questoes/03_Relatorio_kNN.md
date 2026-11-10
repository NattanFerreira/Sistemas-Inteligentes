# Relatório Técnico - Projeto 3: Classificação com $k$-NN (Qualidade de Vinhos)

**Universidade:** Universidade Federal Rural do Semi-Árido (UFERSA)  
**Disciplina:** PAM0466 – Sistemas Inteligentes  
**Docente:** Prof. Dr. Pedro Thiago Valério de Souza  
**Discente:** Nattan Ferreira Lopes  

---

## 1. Contextualização e Enunciado do Problema

> *"A indústria vinícola depende historicamente de avaliações humanas — caras, lentas e subjetivas. Um modelo preditivo de qualidade tem aplicações diretas no controle de lotes antes do engarrafamento, na precificação e na certificação de denominação de origem por órgãos reguladores... Plataformas como a Vivino já utilizam machine learning com esse objetivo. No Brasil, o contexto do Vale do São Francisco torna o problema especialmente próximo da nossa realidade.*  
> *Neste projeto, você é convidado a construir um modelo de classificação de vinhos em três categorias de qualidade (baixa, média e alta) a partir de um conjunto de dados químicos utilizando o k-NN (k-Nearest Neighbors).*  
> *O projeto utiliza o arquivo winequality-red.csv do dataset Wine Quality, com 1.599 amostras de vinho tinto da região do Vinho Verde (Portugal) descritas por 11 atributos físico-químicos: acidez fixa, acidez volátil, ácido cítrico, açúcar residual, cloretos, dióxido de enxofre livre, dióxido de enxofre total, densidade, pH, sulfatos e teor alcoólico. O rótulo de qualidade (quality) é um valor inteiro de 0 a 10 atribuído por sommeliers."*

---

## 2. Resolução das Tarefas Solicitadas

---

### Tarefa 1: Preparação dos Dados, Recodificação e Justificativa da Normalização

> **Enunciado:** *"Preparação dos dados: carregue o dataset e recodifique a variável quality em três classes: notas $\le 5$ como ruim, 6–7 como médio e $\ge 8$ como bom. Em seguida, normalize todos os atributos utilizando Min-Max ou Z-score. Justifique por que a normalização é uma etapa obrigatória para o k-NN."*

1. **Recodificação em 3 Classes:**
   - Notas $\le 5 \to$ **Ruim (Classe 0)**: 744 amostras (46,53%).
   - Notas $6$ e $7 \to$ **Médio (Classe 1)**: 837 amostras (52,35%).
   - Notas $\ge 8 \to$ **Bom (Classe 2)**: 18 amostras (1,13%).
2. **Normalização dos Atributos:** Empregou-se a padronização por Z-score via `StandardScaler`, transformando cada um dos 11 atributos para média 0 e variância unitária.

**Justificativa da Obrigatoriedade da Normalização no $k$-NN:**  
O algoritmo $k$-NN é baseado exclusivamente no cômputo da distância geométrica entre os vetores de atributos para determinar vizinhança. Sob a métrica da **distância euclidiana**:

$$d(\mathbf{x}, \mathbf{y}) = \sqrt{\sum_{i=1}^{p} (x_i - y_i)^2}$$

Caso os dados mantenham suas escalas brutas, atributos com valores numéricos de magnitude elevada dominam desproporcionalmente a soma quadrática. No dataset:
- O atributo `total sulfur dioxide` varia na faixa de **6 a 289**;
- O atributo `chlorides` varia de **0,01 a 0,61**.

Como as diferenças pontuais são elevadas ao quadrado, o termo quadrático de `total sulfur dioxide` atinge magnitude de centenas a milhares, absorvendo mais de **99% da distância total**. Como consequência direta, o $k$-NN ignoraria variáveis químicas essenciais (como cloretos, pH e teor alcoólico). A padronização equaliza a contribuição dimensional de todos os 11 atributos.

---

### Tarefa 2: Divisão Treino/Teste Estratificada

> **Enunciado:** *"Divisão treino/teste: divida os dados em 80% para treino e 20% para teste, utilizando divisão estratificada para garantir que a proporção das três classes seja mantida em ambos os conjuntos."*

- **Particionamento:** Os dados foram segmentados em **80% para treino** (1.279 instâncias) e **20% para teste** (320 instâncias), com semente pseudoaleatória fixada em `random_state=42`.
- **Importância da Estratificação (`stratify=y`):**  
  A distribuição original apresenta severo desbalanceamento: a classe de qualidade superior ("Bom") abrange apenas 18 observações no universo de 1.599 vinhos (1,13%). Em uma amostragem puramente aleatória não estratificada, haveria probabilidade não desprezível de alocar todas as amostras positivas no treino e nenhuma no teste (ou vice-versa). A estratificação preserva com precisão a razão populacional no teste: 149 amostras na classe Ruim, 167 na classe Médio e 4 na classe Bom.

---

### Tarefa 3: Treinamento, Validação Cruzada e Escolha do $k$

> **Enunciado:** *"Treinamento e escolha do k: treine o modelo para diferentes valores de k (ex: 1, 3, 5, 7, 11) utilizando validação cruzada de 5 folds sobre o conjunto de treino. Plote a acurácia média em função de k e escolha o valor com melhor desempenho. O que acontece com o modelo para valores muito baixos e muito altos de k?"*

Executou-se o protocolo de validação cruzada estratificada em 5 partições (*5-Fold Stratified Cross-Validation*) sobre a base de treino, avaliando valores ímpares de $k$:

| Valor de $k$ | Acurácia Média no 5-Fold | Comportamento da Fronteira |
| :---: | :---: | :--- |
| **$k = 1$** | **73,03%** | Alta complexidade geométrica / Sobreajuste pontual |
| $k = 3$ | 70,84% | Redução da influência de outliers isolados |
| $k = 5$ | 71,07% | Suavização inicial |
| $k = 7$ | 69,74% | Região de transição intermediária |
| $k = 9$ | 71,07% | Estabilização estatística da vizinhança |
| $k = 11$ | 71,54% | Crescimento de acurácia com maior suavização |
| $k = 13$ | 72,17% | Boa capacidade de generalização regional |
| **$k = 15$** | **72,40%** | Maior equilíbrio entre suavidade e acurácia média |
| $k = 17$ | 71,93% | Tendência de dominância da classe majoritária |
| $k = 21$ | 71,23% | Subajuste progressivo das fronteiras locais |

**Comportamento para Valores Extremos de $k$:**
- **Para $k$ excessivamente baixo ($k \to 1$):** O modelo condiciona sua classificação a um único vizinho imediato, tornando-se hipersensível a desvios amostrais, erros de rotulagem e ruídos estocásticos. Isso fragmenta o espaço de decisão, implicando **alta variância e sobreajuste (overfitting)**.
- **Para $k$ excessivamente alto ($k \to N$):** A vizinhança de consulta incorpora uma fração abrangente de todo o espaço amostral. Por consequência do critério de maioria, a classe modal (majoritária) predomina sobre as predições em praticamente qualquer coordenada, colapsando as fronteiras de decisão e incorrendo em **alto viés e subajuste (underfitting)**.
- **Escolha do Hiperparâmetro:** Embora $k=1$ alcance a pontuação pontual máxima de 73,03% no treino, a escolha de **$k=15$** (72,40%) oferece melhor robustez de fronteira e tolerância a ruído para generalização em dados não vistos.

---

### Tarefa 4: Avaliação de Desempenho no Teste e Análise de Erros

> **Enunciado:** *"Avaliação do modelo: utilizando o conjunto de teste, calcule a matriz de confusão e as métricas de acurácia, precisão, recall e F1-score para cada classe. Responda: Entre quais classes o modelo erra com mais frequência?"*

Avaliando o classificador parametrizado com o hiperparâmetro selecionado sobre o conjunto de teste de 320 vinhos, obtiveram-se os seguintes resultados analíticos:

| Classe de Qualidade | Suporte Real | Precisão | Recall | F1-Score |
| :--- | :---: | :---: | :---: | :---: |
| **Ruim ($\le 5$)** | 149 | 0,70 | 0,73 | 0,72 |
| **Médio ($6-7$)** | 167 | 0,74 | 0,73 | 0,74 |
| **Bom ($\ge 8$)** | 4 | 1,00 | 0,25 | 0,40 |
| **Desempenho Global (Acurácia)** | **320** | **72,50%** | **72,50%** | **0,72** |

#### Matriz de Confusão no Teste:

| Classe Real \ Prevista | Previsto: Ruim ($\le 5$) | Previsto: Médio ($6-7$) | Previsto: Bom ($\ge 8$) |
| :--- | :---: | :---: | :---: |
| **Real: Ruim ($\le 5$)** | **109** | 40 | 0 |
| **Real: Médio ($6-7$)** | 45 | **122** | 0 |
| **Real: Bom ($\ge 8$)** | 1 | 2 | **1** |

#### Análise: Entre quais classes o modelo erra com mais frequência?
O classificador erra com expressiva preponderância ao **confundir reciprocamente as classes Ruim ($\le 5$) e Médio ($6-7$)**:
- **40 amostras da classe Ruim** foram classificadas erroneamente como **Médio**;
- **45 amostras da classe Médio** foram classificadas erroneamente como **Ruim**.

**Justificativa Técnica:**  
Os rótulos das classes adjacentes (nota 5 versus nota 6) encontram-se situados exatamente no limiar da fronteira de corte estabelecida pela recodificação. As assinaturas físico-químicas de um vinho de nota 5 e de nota 6 sobrepõem-se no espaço euclidiano, tratando-se de uma discriminação sutil e sujeita à própria variabilidade subjetiva das notas atribuídas pelos degustadores. Em contrapartida, para a classe **Bom**, a precisão foi de 100% sobre as predições geradas, porém o recall foi restrito a 25% em decorrência da reduzida densidade de instâncias no espaço amostral (apenas 4 no teste).
