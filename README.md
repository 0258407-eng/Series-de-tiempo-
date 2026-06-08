# Stochastic_Models

This course is based on Lecture Notes given by César Galindo at Universidad Panamericana.

Implemented by  **Jacobo Herrera Bistre** — 8th semester, Quantitative Finance.

---

## Overview

This repository contains all deliverables for the course *Análisis de Series de Tiempo* (Time Series Analysis), covering SARIMA/SARIMAX modeling and the GARCH family of volatility models. The work connects theoretical econometrics to practical Python implementations using real market data, with applications in regulatory risk (Basel III) and CFA-level financial analysis.

---

## Contents

1. **SARIMA Complete Guide** (`SARIMA_Guia_Completa.pdf`) — Foundational reference covering the full SARIMA(p,d,q)(P,D,Q)_S model family. Includes ACF/PACF interpretation, model identification rules, AIC vs. BIC selection criteria, and the 6-step modeling workflow. Three worked Python examples: stock prices (ARIMA(1,1,1)), USD/MXN exchange rate (SARIMA(1,1,1)(1,1,1)_12), and WTI crude oil (ARIMA(2,1,2) with rolling forecast).

2. **SARIMA in Crypto and Indices** (`SARIMA_Crypto_Indices.pdf`) — Applied guide comparing four financial time series. Bitcoin BTC/USD fitted with ARIMA(1,1,0) — PACF cuts off at lag 1, no seasonality given 24/7 trading. Ethereum ETH/USD fitted with ARIMA(1,1,1) — mixed ACF/PACF pattern from DeFi activity plus confirmed ARCH effects requiring a GARCH layer. S&P 500 and IPC México fitted with SARIMA(1,1,1)(1,1,0)_12 — monthly data with clear seasonal peaks at lags 12, 24, 36. Also demonstrates SARIMAX using the S&P 500 as an exogenous variable in the IPC model (historical correlation ≈ 0.70–0.85).

3. **Problem Set Solutions — SARIMAX & GARCH** (`respuestas.pdf`) — Full solutions to 10 analytical exercises and 10 CFA Level III-style multiple choice questions covering the entire SARIMAX/GARCH family. Each exercise includes a technical answer with formal notation and an accessible plain-language explanation.

4. **Final Project** (`Proyecto_final.docx`) — Polished deliverable integrating all exercises. Covers SARIMAX identification and estimation (sovereign EM bond, Treasury yield exogenous variable); LM-ARCH testing and GARCH(1,1) estimation with covariance stationarity verification; GJR-GARCH leverage effect and News Impact Curve; EGARCH log-variance modeling; GARCH-in-Mean dynamic risk premium; full ARIMA+GARCH validation protocol (Ljung-Box on residuals and squares, Sign-Bias tests); dynamic VaR computation with GARCH-t and Kupiec backtesting; SARIMAX multi-variable WTI model with REER and global PMI; and model selection under AIC/BIC with Basel III recommendation.

---

## Key Models and Results

| Model | Asset / Context | Key Result |
|---|---|---|
| ARIMA(1,1,0) | Bitcoin BTC/USD | PACF cuts at lag 1; short-run momentum; heavy tails → complement with GARCH |
| ARIMA(1,1,1) | Ethereum ETH/USD | Mixed AR+MA pattern; confirmed ARCH effect (LM test) |
| SARIMA(1,1,1)(1,1,0)_12 | S&P 500 / IPC México | Seasonal peaks at lags 12, 24, 36; IPC modeled with S&P as exogenous variable |
| SARIMAX(1,1,1)(1,1,0)_12 | EM sovereign bond | β̂(Treasury yield) = −0.87 (t = −7.25); ADF confirms d = 1 |
| GARCH(1,1) | General equity | α̂ + β̂ = 0.984 < 1 ✓; σ̄_annual ≈ 21.18%; shock half-life ≈ 34 days |
| GJR-GARCH(1,1,1) | S&P 500 | Leverage ratio 2.86×; negative shock impact 186% higher than positive |
| EGARCH(1,1) | HY bond ETF | γ̂ = −0.063 < 0; negative shock raises σ² by 29% vs. equivalent positive shock |
| GARCH-M | Private Equity fund | δ̂ = 0.38 risk premium per σ unit; annualized return ≈ 18.07% at σ = 8% |
| GARCH(1,1)-t | Tech ETF $10M | VaR₉₅ = $353,050; VaR₉₉ = $573,760 (1-day, ν = 6.2 degrees of freedom) |

---

## Core Equations

| Concept | Formula |
|---|---|
| SARIMA general form | Φ_P(B^S) φ_p(B)(1−B)^d(1−B^S)^D y_t = Θ_Q(B^S) θ_q(B) ε_t |
| SARIMAX with exogenous | (1−Φ₁B¹²)(1−φ₁B)(1−B)(1−B¹²) y_t = β x_t + (1+θ₁B) ε_t |
| Historical volatility | σ = std(r_t) · √252 |
| GARCH(1,1) variance | σ²_t = ω + α ε²_{t-1} + β σ²_{t-1} |
| Unconditional variance | σ̄² = ω / (1 − α − β) |
| GJR-GARCH leverage | σ²_t = ω + α ε²_{t-1} + λ ε²_{t-1} · 1{ε_{t-1}<0} + β σ²_{t-1} |
| EGARCH log-variance | log σ²_t = ω + β log σ²_{t-1} + α(|z_{t-1}| − √(2/π)) + γ z_{t-1} |
| GARCH-M mean equation | r_t = μ + δ σ_t + ε_t |
| Multi-step GARCH forecast | E_t[σ²_{t+h}] = σ̄² + (α+β)^(h−1) · (σ²_{t+1} − σ̄²) |

---

## Validation Checklist (5-step GARCH protocol)

1. **Parameter significance** — t-tests on ω, α, β (and λ, γ if applicable).
2. **Stationarity** — verify α + β < 1 (GARCH), α + λ/2 + β < 1 (GJR), |β| < 1 (EGARCH).
3. **Mean autocorrelation** — Ljung-Box on standardized residuals ẑ_t (p > 0.05 desired).
4. **Variance autocorrelation** — Ljung-Box on ẑ²_t and LM-ARCH test (p > 0.05 desired).
5. **Asymmetry** — Sign-Bias, Negative-Size-Bias, Positive-Size-Bias (Engle-Ng 1993). Significant Neg-Size-Bias → switch to GJR-GARCH or EGARCH.

Additional: Jarque-Bera / Q-Q plot on ẑ_t; Kupiec (1995) and Christoffersen VaR backtesting.

---

## Basel III Recommendation

For regulatory VaR (99%, 10-day horizon, equity portfolios): **GJR-GARCH(1,1)-t**. Combines asymmetric leverage response (relevant for downside risk), heavy tails via Student-t (ν estimated), and superior AIC fit vs. symmetric GARCH. Symmetric GARCH-Normal underestimates VaR precisely during turbulent episodes when protection matters most.

---

## Installation

```bash
git clone https://github.com/usuario/Stochastic_Models
cd Stochastic_Models
pip install numpy pandas scipy matplotlib statsmodels arch yfinance
jupyter notebook
```

---

## Dependencies

- `numpy` / `pandas` — time series manipulation and vectorized computation
- `statsmodels` — ARIMA, SARIMAX, ADF test, Ljung-Box, ACF/PACF
- `arch` — GARCH, GJR-GARCH, EGARCH, GARCH-M estimation
- `scipy.stats` — Student-t VaR quantiles, Jarque-Bera
- `matplotlib` — trajectory plots, NIC curves, volatility charts
- `yfinance` — real price data (BTC, ETH, SPX, IPC, WTI, options chains)
