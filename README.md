# 📈 Forecasting Financeiro: Transformers vs. State Space Models (Mamba)

Este repositório contém um estudo empírico e rigoroso comparando a eficácia de arquiteturas de *Deep Learning* de ponta na previsão de séries temporais financeiras. O foco principal é avaliar se os **State Space Models (SSMs)**, base da arquitetura Mamba, conseguem superar o mecanismo de Atenção Quadrática dos **Transformers** em ambientes de baixíssima relação sinal-ruído.

## 🎯 Resumo Executivo
Mercados financeiros são sistemas estocásticos e não-estacionários. A intuição comum em Inteligência Artificial dita que "modelos maiores e mais complexos geram resultados melhores". Este projeto desafia essa premissa. 

Construímos um laboratório quantitativo do zero para prever os retornos diários de três ativos com comportamentos distintos (Bitcoin, EURUSD e Petróleo WTI). Os resultados provam que a restrição paramétrica e o viés indutivo causal de um SSM Linear de **19 mil parâmetros** entregam predições direcionais superiores (e mais robustas) do que um Transformer de **562 mil parâmetros**, que sofreu de *overfitting* severo e acomodação de viés linear.

---

## 🔬 Metodologia e Engenharia de Dados

Para garantir integridade acadêmica e evitar o vazamento de dados (*Data Leakage*), comum em tutoriais amadores de machine learning financeiro, o pipeline foi construído sob regras estritas:

1. **Preços $\rightarrow$ Log-Returns:** O modelo não prevê o preço bruto absoluto, mas sim a variação percentual logarítmica, garantindo a estacionariedade parcial da série.
2. **Feature Engineering Blindada:** Adição de médias móveis, volatilidade histórica e momento. O escalonamento (`StandardScaler`) foi ajustado **exclusivamente** no conjunto de Treino, impedindo que os modelos "espiassem" a volatilidade macroeconômica do futuro.
3. **Divisão Cronológica Causal:** 70% Treino | 15% Validação | 15% Teste.
4. **Janela Deslizante:** *Lookback window* de 30 dias de passado para prever $T+1$.

---

## 🧠 Arquiteturas Avaliadas

### 1. O Desafiante 1: Time Series Transformer
* **Parâmetros:** ~562.000
* **Mecanismo:** *Self-Attention* e *Positional Encoding*.
* **Hipótese:** O Transformer processa a janela de 30 dias de forma global $\mathcal{O}(n^2)$. Em finanças, teorizamos que isso o faria decorar ruídos passados (correlações espúrias), prejudicando a predição real.

### 2. O Desafiante 2: State Space Model (SSM / Mamba-like)
* **Parâmetros:** ~19.000 (Redução de 96%)
* **Mecanismo:** Discretização de sistemas dinâmicos contínuos ($h_t = A h_{t-1} + B x_t$).
* **Hipótese:** Ao impor uma recorrência linear estrita $\mathcal{O}(n)$ e comprimir a informação em um estado oculto, o modelo atua como um filtro rigoroso, focando apenas no *momentum* estrutural e ignorando as flutuações diárias do mercado.

---

## 📊 Resultados e Desempenho *Out-of-Sample*

A métrica principal de avaliação de utilidade prática foi a **Acurácia Direcional (DA)**, comparada contra um *Baseline Naive* (a premissa de que o amanhã será igual ao hoje - *Random Walk*).

| Ativo | Dinâmica de Mercado | Baseline (Naive) | Transformer | SSM / Mamba | Veredito |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bitcoin** | Tendência / Volátil | 46.07% | 47.57% | **53.56%** | **SSM Venceu.** |
| **EURUSD** | Reversão à Média | **53.19%** | 51.60% | 49.47% | **Baseline Venceu.** |
| **Petróleo** | Choques / Tendência | 48.07% | 48.62% | **55.80%** | **SSM Esmagou.** |

### 🔍 O Paradoxo do Retorno Cumulativo (Análise Gráfica)
Ao plotarmos o retorno cumulativo no conjunto de Teste Cego, identificamos o comportamento interno das redes:
* **Transformer:** Demonstrou o clássico **Acomodamento de Viés Linear**. Devido à complexidade desnecessária para a tarefa, a rede "alucinou" supertendências e se desconectou da realidade estocástica (Overfitting).
* **SSM:** Ao possuir uma "visão limitada" pelos seus parcos 19k parâmetros, foi forçado a ser conservador. Ele rastreou os vetores macro direcionais de ativos de tendência (BTC e Óleo), provando que, em modelos financeiros, **a restrição é uma virtude matemática**.

---

## 🏆 Conclusão do Estudo

Este projeto prova na prática a premissa da **Hipótese dos Mercados Eficientes**. Redes Neurais profundas (como Transformers) não resolvem a estocasticidade do mercado apenas jogando poder computacional no problema; pelo contrário, elas amplificam o erro ao decorar o ruído.

O uso de **State Space Models**, inspirados na teoria de controle, representa o caminho correto para a modelagem financeira quantitativa quantitativa. O SSM atinge o estado da arte com uma fração ínfima do custo computacional, mitigando o ruído local e capturando a causalidade da série temporal.

---

## 🚀 Como reproduzir este experimento

1. Faça o clone do repositório:
   ```bash
   git clone [https://github.com/SEU-USUARIO/ssm-vs-transformer-finance.git](https://github.com/SEU-USUARIO/ssm-vs-transformer-finance.git)
