# Sistemas Inteligentes - 1ª Lista de Projetos

**Universidade:** Universidade Federal Rural do Semi-Árido (UFERSA)  
**Disciplina:** Sistemas Inteligentes  
**Docente:** Prof. Dr. Pedro Thiago Valério de Souza  
**Discente:** Nattan Ferreira Lopes  

---

## 1. Visão Geral do Repositório

Este repositório contém a implementação prática e os relatórios analíticos dos projetos da **1ª Lista de Projetos** da disciplina de Sistemas Inteligentes.

Cada projeto é composto por:
1. **Notebook interativo (`.ipynb`)**: Código fonte estruturado, documentado e com todas as saídas e visualizações gráficas geradas.
2. **Relatório analítico (`.md`)**: Análise teórica e empírica detalhada de cada tarefa solicitada, incluindo interpretação físico-química/médica e avaliação formal dos resultados.

---

## 2. Estrutura de Diretórios

```text
├── data/                                 # Datasets utilizados nos projetos
│   ├── CCPP/                             # Combined Cycle Power Plant (Regressão Linear)
│   ├── heart/                            # Heart Disease Cleveland (Regressão Logística)
│   └── wine/                             # Wine Quality Red (k-NN)
├── listas/
│   └── projetos1u.pdf                    # Enunciado oficial da lista de projetos
├── questoes/
│   ├── 01_Regressao_Linear_CCPP.ipynb    # Código da Questão 1
│   ├── 01_Relatorio_Regressao_Linear.md  # Relatório da Questão 1
│   ├── 02_Regressao_Logistica_Heart.ipynb# Código da Questão 2
│   ├── 02_Relatorio_Regressao_Logistica.md# Relatório da Questão 2
│   ├── 03_kNN_Wine_Quality.ipynb         # Código da Questão 3
│   └── 03_Relatorio_kNN.md               # Relatório da Questão 3
├── .gitignore                            # Arquivos ignorados pelo controle de versão
├── README.md                             # Documentação principal
└── requirements.txt                      # Dependências do projeto
```

---

## 3. Instruções de Reprodução e Execução

Para reproduzir os experimentos e executar os notebooks localmente:

### 3.1 Clonar o repositório
```bash
git clone https://github.com/NattanFerreira/Sistemas-Inteligentes.git
cd Sistemas-Inteligentes
```

### 3.2 Configurar o ambiente virtual

- **Linux / macOS:**
  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  ```

- **Windows (PowerShell):**
  ```powershell
  python -m venv .venv
  .venv\Scripts\Activate.ps1
  ```

- **Windows (Prompt de Comando - CMD):**
  ```cmd
  python -m venv .venv
  .venv\Scripts\activate.bat
  ```

### 3.3 Instalar dependências
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### 3.4 Execução dos Notebooks
```bash
jupyter notebook
```
Alternativamente, abra o diretório no **VS Code** e selecione o kernel do ambiente virtual `.venv`.

> **Nota:** Os notebooks utilizam carregamento relativo dinâmico, sendo executáveis tanto a partir do diretório raiz quanto de dentro da pasta `questoes/`.

---

## 4. Síntese dos Projetos

| # | Problema | Modelo | Dataset | Relatório Técnico | Notebook |
|:---:|---|---|---|:---:|:---:|
| **1** | Previsão de Energia em Usina de Ciclo Combinado | Regressão Linear Múltipla | UCI CCPP (9.568 amostras) | [Relatório 1](questoes/01_Relatorio_Regressao_Linear.md) | [Notebook 1](questoes/01_Regressao_Linear_CCPP.ipynb) |
| **2** | Diagnóstico de Doenças Cardíacas | Regressão Logística (`liblinear`) | UCI Heart Disease Cleveland (303 pacientes) | [Relatório 2](questoes/02_Relatorio_Regressao_Logistica.md) | [Notebook 2](questoes/02_Regressao_Logistica_Heart.ipynb) |
| **3** | Classificação de Qualidade de Vinhos | $k$-Nearest Neighbors ($k$-NN) | UCI Wine Quality Red (1.599 amostras) | [Relatório 3](questoes/03_Relatorio_kNN.md) | [Notebook 3](questoes/03_kNN_Wine_Quality.ipynb) |

---

## 5. Tecnologias e Bibliotecas Utilizadas

- **Linguagem:** Python 3.10+
- **Manipulação de Dados:** `pandas`, `numpy`
- **Visualização de Dados:** `matplotlib`, `seaborn`
- **Modelagem e Avaliação:** `scikit-learn`
- **Ambiente Interativo:** `jupyter`, `ipykernel`
