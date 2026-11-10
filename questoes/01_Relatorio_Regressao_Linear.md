# Relatório Técnico - Projeto 1: Regressão Linear Múltipla (Usina CCPP)

**Universidade:** Universidade Federal Rural do Semi-Árido (UFERSA)  
**Disciplina:** PAM0466 – Sistemas Inteligentes  
**Docente:** Prof. Dr. Pedro Thiago Valério de Souza  
**Discente:** Nattan Ferreira Lopes  

---

## 1. Contextualização e Enunciado do Problema

> *"Uma usina termelétrica de ciclo combinado (UTCC) é uma instalação de alta eficiência que combina turbinas a gás e a vapor para gerar eletricidade. Ela reaproveita o calor dos gases de exaustão da turbina a gás (ciclo Brayton) para produzir vapor em uma caldeira, acionando uma segunda turbina (ciclo Rankine), aumentando significativamente o rendimento. A principal vantagem é a eficiência energética, que pode chegar a 60%, reduzindo as emissões de gases de efeito estufa.*  
> *Embora apresente alta eficiência, o principal desafio técnico no uso de uma usina UTCC reside na integração, sincronização e operação conjunta de dois ciclos termodinâmicos distintos (ciclo Brayton e ciclo Rankine) para funcionar de forma eficiente. Assim sendo, a potência elétrica de saída (PE) é altamente sensível às condições ambientais.*  
> *Neste projeto, você deve construir um modelo matemático de regressão linear múltipla para prever a produção de energia baseando-se em sensores ambientais, permitindo a otimização do despacho energético.*  
> *Para realizar o treinamento do nosso modelo, utilizamos o dataset UCI CCPP contendo 9.568 amostras coletadas ao longo de 6 anos com as seguintes variáveis:*  
> - **AT** (*Ambient Temperature*): Temperatura em °C. Afeta a densidade do ar e a eficiência da combustão.  
> - **V** (*Exhaust Vacuum*): Vácuo de escape em cm Hg. Relacionado à pressão na turbina a vapor.  
> - **AP** (*Ambient Pressure*): Pressão atmosférica em mbar.  
> - **RH** (*Relative Humidity*): Umidade relativa em percentagem.  
> - **PE** (*Net Hourly Electrical Energy Output*): A variável alvo em MW (MegaWatts)."*

---

## 2. Pré-Processamento dos Dados e Metodologia

Conforme solicitado pelas diretrizes gerais do problema, o pré-processamento compreendeu:

1. **Tratamento de Valores Ausentes:** A verificação amostral com `df.isnull().sum()` constatou que o conjunto de dados possui integridade total (0 valores nulos em todas as 9.568 linhas).
2. **Divisão Treino/Teste:** O dataset foi dividido na proporção de **80% para treinamento** (7.654 amostras) e **20% para teste** (1.914 amostras), utilizando amostragem reproduzível com `random_state=42`.
3. **Padronização dos Atributos:** As variáveis preditoras independentes foram submetidas ao escalonamento por Z-score via `StandardScaler` (média 0 e desvio padrão 1). O ajuste dos parâmetros da normalização foi computado exclusivamente sobre o conjunto de treino (`fit_transform`) e replicado no teste (`transform`), mitigando vazamento de dados (*data leakage*).

---

## 3. Resolução das Tarefas

---

### Tarefa 1: Correlação entre Variáveis Independentes e Variável Dependente (PE)

> **Enunciado:** *"Calcule a correlação entre as variáveis independentes (AT, V, AP, RH) e a variável dependente (PE). Quais variáveis parecem ter maior influência na produção de energia?"*

A matriz de correlação linear de Pearson calculada entre os sensores ambientais e a potência elétrica de saída (**PE**) apresentou os seguintes coeficientes:

| Variável | Sensor | Correlação com PE ($r$) | Sentido da Relação |
| :---: | :--- | :---: | :--- |
| **AT** | Temperatura Ambiente | **-0.9481** | Negativa Fortíssima |
| **V** | Vácuo de Escape | **-0.8698** | Negativa Muito Forte |
| **AP** | Pressão Ambiente | **+0.5184** | Positiva Moderada |
| **RH** | Umidade Relativa | **+0.3898** | Positiva Moderada |

**Análise das Variáveis de Maior Influência:**  
As variáveis com **maior influência na produção de energia são a Temperatura Ambiente (AT) e o Vácuo de Escape (V)**:
- A **Temperatura (AT)** apresenta correlação quase perfeita de **-0.95**. Sob a ótica termodinâmica, o aumento da temperatura reduz a massa específica do ar admitido pela turbina a gás, diminuindo o fluxo de oxigênio para combustão e, consequentemente, a potência total gerada.
- O **Vácuo de Escape (V)** apresenta correlação fortemente negativa de **-0.87**. Valores mais elevados indicam maior contrapressão no condensador de vapor do ciclo Rankine, reduzindo a queda de entalpia útil na turbina e o rendimento global da planta.

---

### Tarefa 2: Correlação entre Variáveis Independentes e Multicolinearidade

> **Enunciado:** *"Calcule a correlação entre as variáveis independentes (AT, V, AP, RH) entre si. Existe alguma relação forte entre elas? Se sim, isso pode indicar multicolinearidade."*

As correlações par a par calculadas entre as variáveis explicativas são apresentadas a seguir:

- **Correlação entre AT e V:** **+0.8441**
- **Correlação entre AT e AP:** $-0.5075$
- **Correlação entre AT e RH:** $-0.5425$
- **Correlação entre V e AP:** $-0.4136$
- **Correlação entre V e RH:** $-0.3112$
- **Correlação entre AP e RH:** $+0.0996$

**Avaliação de Multicolinearidade:**  
**Sim.** Constata-se uma forte correlação linear positiva entre a **Temperatura Ambiente (AT) e o Vácuo de Escape (V) ($r = 0{,}8441$)**.  
Essa relação evidencia **potencial multicolinearidade**, decorrente do acoplamento térmico da usina: elevações de temperatura ambiente aquecem a água de resfriamento utilizada no condensador, elevando correspondentemente o vácuo/pressão residual de escape.

---

### Tarefa 3: Construção do Modelo de Regressão Linear Múltipla

> **Enunciado:** *"Construa um modelo de regressão linear múltipla utilizando as variáveis independentes (AT, V, AP, RH) para prever a variável dependente (PE). Divida os dados em treino (80%) e teste (20%). Realize o pré-processamento dos dados, incluindo a normalização ou padronização das variáveis independentes, tratamento de valores ausentes e divisão dos dados em conjuntos de treino e teste."*

O modelo de regressão linear múltipla foi ajustado com as 4 variáveis independentes padronizadas sobre as 7.654 amostras de treino. A equação linear resultante expressa a potência predita $\hat{y}$ (PE em MW) em função das variáveis padronizadas ($z_{\text{AT}}, z_{\text{V}}, z_{\text{AP}}, z_{\text{RH}}$):

$$\hat{y} = \theta_0 + \theta_1 z_{\text{AT}} + \theta_2 z_{\text{V}} + \theta_3 z_{\text{AP}} + \theta_4 z_{\text{RH}}$$

Os parâmetros estimados pelo algoritmo foram:
- **Intercepto ($\theta_0$):** $454{,}36\text{ MW}$ (valor médio da geração de energia na base de treino)
- **Coeficiente AT ($\theta_1$):** $-14{,}72$
- **Coeficiente V ($\theta_2$):** $-3{,}03$
- **Coeficiente AP ($\theta_3$):** $+0{,}39$
- **Coeficiente RH ($\theta_4$):** $-2{,}31$

A magnitude dos coeficientes padronizados reflete analiticamente a preponderância da temperatura ambiente no comportamento da usina.

---

### Tarefa 4: Avaliação de Desempenho e Adequação do Modelo

> **Enunciado:** *"Avalie o desempenho do modelo utilizando métricas como R², RMSE e MAE. O modelo é adequado para prever a produção de energia? Justifique sua resposta."*

O modelo foi avaliado no conjunto de teste independente (1.914 amostras nunca observadas durante o ajuste):

| Métrica | Valor Obtido | Significado Teórico |
| :--- | :---: | :--- |
| **$R^2$ (Coeficiente de Determinação)** | **0.9301** | O modelo linear explica **93,01%** de toda a variância da produção de energia da planta. |
| **RMSE (Raiz do Erro Quadrático Médio)** | **4.5026 MW** | Penaliza desvios maiores, indicando desvio padrão dos resíduos de 4,50 MW. |
| **MAE (Erro Médio Absoluto)** | **3.5959 MW** | Em média, as estimativas pontuais horárias desviam 3,60 MW em módulo do valor real. |

**Adequação do Modelo e Justificativa:**  
**Sim, o modelo é altamente adequado para prever a produção de energia.**  
A amplitude real de produção da usina varia entre 420 MW e 495 MW, apresentando média em torno de 454 MW. Um Erro Médio Absoluto de **3,60 MW** equivale a um erro relativo médio inferior a **0,8%**. Adicionalmente, o coeficiente $R^2 > 0{,}93$ comprova que a simplificação por regressão linear múltipla preserva elevada capacidade de generalização e estabilidade, prestando-se com precisão suficiente ao planejamento do despacho térmico.
