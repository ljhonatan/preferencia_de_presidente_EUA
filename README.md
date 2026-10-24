# preferencia_de_presidente_EUA
uma analise simples e rapida de uma serie temporal dos presidentes
# 📊 Análise ARIMA da Série `president`

## 📖 Descrição

Este repositório contém um script em **R** para análise de séries temporais aplicada ao dataset `president`, disponível no pacote `forecast`. A série representa o **percentual de aprovação presidencial nos EUA** em pesquisas trimestrais, cobrindo o período de **1945 a 1974**.

O script realiza:

1. **Visualização** da série temporal original;
2. **Ajuste automático** de um modelo ARIMA via `auto.arima()`;
3. **Extração e diagnóstico** dos resíduos do modelo;
4. **Verificação das premissas** do modelo (normalidade, homocedasticidade, autocorrelação) por meio de gráficos e testes estatísticos.

---

## 🎯 Objetivos

- Identificar o modelo ARIMA mais adequado para a série `president`;
- Avaliar a qualidade do ajuste por meio da análise de resíduos;
- Testar se os resíduos se comportam como **ruído branco** (premissa fundamental para previsões confiáveis);
- Verificar a **normalidade** dos resíduos (Shapiro-Wilk) e a **ausência de autocorrelação** (Ljung-Box).

---

## 🛠️ Requisitos

- **R** versão 4.0 ou superior
- Pacotes:
  - `forecast` — modelagem e previsão de séries temporais
  - `ggplot2` — visualização gráfica

### Instalação dos pacotes

install.packages("forecast")
install.packages("ggplot2")

---



## 🚀 Como Executar

1. Clone o repositório:

git clone https://github.com/seu-usuario/analise-president-arima.git
cd analise-president-arima

2. Abra o R ou RStudio e execute o script:

source("analise_president.R")

3. Os gráficos serão exibidos no dispositivo padrão (RStudio Plot Pane ou janela gráfica).

---

## 🧠 Etapas da Análise

### 1. Visualização da Série

autoplot(president)

Gráfico da série original para inspeção visual de tendência, sazonalidade e quebras estruturais.

### 2. Ajuste do Modelo ARIMA

prev = auto.arima(president)

`auto.arima()` seleciona automaticamente as ordens **(p,d,q)(P,D,Q)** minimizando o **AIC/BIC**, testando diferenciações e componentes sazonais.

### 3. Extração dos Resíduos

prev$residuals
print(prev$residuals)

Resíduos = valores observados − valores ajustados. São a base de todo o diagnóstico do modelo.

### 4. Diagnóstico Visual

autoplot(prev$residuals)   # resíduos ao longo do tempo
hist(prev$residuals)       # histograma

Resíduos ideais devem parecer **ruído branco** centrado em zero, sem padrões temporais.

### 5. Estatísticas Descritivas

var(prev$residuals, na.rm = TRUE)
mean(as.vector(prev$residuals), na.rm = TRUE)

- **Variância**: mede a dispersão dos erros;
- **Média**: espera-se valor próximo de **zero** em um modelo bem ajustado.

### 6. Testes Formais

checkresiduals(prev)          # resíduos + ACF + histograma + Ljung-Box
shapiro.test(prev$residuals)  # normalidade dos resíduos

- **`checkresiduals()`**: realiza o teste de **Ljung-Box** para autocorrelação;
- **`shapiro.test()`**: testa a hipótese de **normalidade** (H₀: resíduos são normais).

---

## ⚠️ Observações Importantes

- A linha `act(prev$residuals, na.act.on = na.pass)` presente no script original **é inválida** — a função `act()` **não existe** no R. A execução dessa linha causará erro. Recomenda-se removê-la ou substituí-la por:

na.action(prev$residuals)
# ou
sum(is.na(prev$residuals))

- Verifique `summary(prev)` para conhecer o modelo ARIMA efetivamente escolhido (ordens p, d, q).
- Para visualizar previsões futuras, utilize:

forecast(prev, h = 8)

---

## 📊 Resumo dos Diagnósticos

| Verificação        | Função usada               | Objetivo                     |
|--------------------|----------------------------|------------------------------|
| Série original     | `autoplot(president)`      | Inspeção visual              |
| Ajuste do modelo   | `auto.arima()`             | Seleção automática ARIMA     |
| Resíduos numéricos | `print()`                  | Inspeção numérica            |
| Resíduos no tempo  | `autoplot()`               | Detectar padrões temporais   |
| Distribuição       | `hist()`, `shapiro.test()` | Normalidade                  |
| Dispersão          | `var()`                    | Homocedasticidade aproximada |
| Centralização      | `mean()`                   | Média ≈ 0                    |
| Autocorrelação     | `checkresiduals()`         | Ruído branco (Ljung-Box)     |

---

## 📌 Interpretação Esperada

- **Média dos resíduos ≈ 0** → modelo bem ajustado, sem viés sistemático.
- **Shapiro-Wilk com p-valor > 0,05** → não se rejeita a normalidade dos resíduos.
- **Ljung-Box com p-valor > 0,05** → não há autocorrelação residual significativa (resíduos ≈ ruído branco).
- **Resíduos no gráfico temporal** → devem flutuar aleatoriamente em torno de zero, sem tendência.

---

## 🧩 Tecnologias Utilizadas

- **Linguagem:** R
- **Bibliotecas:** `forecast`, `ggplot2`
- **Métodos estatísticos:** ARIMA, testes de Ljung-Box e Shapiro-Wilk

---

## 👤 Autor


- Email: darochaleaojhonatanestagio@gmail.com

---

## 📄 Licença

Este projeto está licenciado sob a **MIT License** — sinta-se livre para usar, modificar e distribuir.

---

## ⭐ Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir **issues** ou enviar **pull requests** com melhorias, correções ou sugestões.

---

> **Nota:** Este projeto tem fins **educacionais**, demonstrando o fluxo completo de ajuste e diagnóstico de um modelo ARIMA em R.
>
> # ============================================================
# Análise de Séries Temporais - Dataset president
# Modelagem ARIMA e diagnóstico de resíduos
# ============================================================

library(forecast)   # Pacote com funções de séries temporais e o dataset president
library(ggplot2)    # Pacote para gráficos (necessário para autoplot)

# ------------------------------------------------------------
# 1. Visualização da série original
# ------------------------------------------------------------
autoplot(president)  # Gráfico da série temporal president

# ------------------------------------------------------------
# 2. Ajuste automático do modelo ARIMA
# ------------------------------------------------------------
prev = auto.arima(president)  # Seleciona ordens (p,d,q)(P,D,Q) por AIC/BIC

# ------------------------------------------------------------
# 3. Extração e inspeção dos resíduos
# ------------------------------------------------------------
prev$residuals              # Acessa o vetor de resíduos
print(prev$residuals)       # Imprime os resíduos no console

# ------------------------------------------------------------
# 4. Diagnóstico visual dos resíduos
# ------------------------------------------------------------
autoplot(prev$residuals)    # Resíduos ao longo do tempo
hist(prev$residuals)        # Histograma dos resíduos

# ------------------------------------------------------------
# 5. Estatísticas descritivas dos resíduos
# ------------------------------------------------------------
var(prev$residuals, na.rm = TRUE)                    # Variância dos resíduos
mean(as.vector(prev$residuals), na.rm = TRUE)        # Média dos resíduos (esperado ~0)

# ------------------------------------------------------------
# 6. [ATENÇÃO] Linha inválida - função act() não existe
# ------------------------------------------------------------
# act(prev$residuals, na.act.on = na.pass)  # ERRO: função inexistente
# Alternativas sugeridas:
# na.action(prev$residuals)
# sum(is.na(prev$residuals))

# ------------------------------------------------------------
# 7. Testes formais de diagnóstico
# ------------------------------------------------------------
checkresiduals(prev)         # Plota resíduos + ACF + histograma + Ljung-Box
shapiro.test(prev$residuals) # Teste de normalidade (Shapiro-Wilk)
