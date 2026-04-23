# Trade Analysis Skills

Statistical methods toolkit for international trade data analysis, designed for AI Agents.

---

## Table of Contents

1. [Gravity Model](#1-gravity-model)
2. [RCA Analysis](#2-rca-analysis)
3. [Time Series Analysis](#3-time-series-analysis)
4. [Risk Management](#4-risk-management)

---

## 1. Gravity Model

**Purpose**: Analyze bilateral trade flows, predict trade patterns, assess FTA effects

### Core Formula

```
Trade_ij = A × (GDP_i^α × GDP_j^β) / Distance_ij^γ × exp(δ×Controls)
```

### Variables

| Variable | Meaning | Expected Sign |
|----------|---------|---------------|
| GDP_i, GDP_j | Exporter and importer economic size | + |
| Distance_ij | Geographic distance between countries | - |
| FTA_ij | Free Trade Agreement | + |
| Common Language | Shared language | + |
| Colonial Ties | Historical colonial relationship | + |

### Input Data Format

```csv
exporter,importer,year,trade_flow,gdp_exporter,gdp_importer,distance,fta
CHN,USA,2023,500000000,17700000000000,25500000000000,12000,0
CHN,JPN,2023,300000000,17700000000000,4200000000000,3000,1
DEU,FRA,2023,400000000,4200000000000,2900000000000,500,1
```

### Usage

```bash
python gravity_model.py --data trade_data.csv --method ppml --fe exporter importer year
```

### Output

- Regression coefficients with significance
- Distance elasticity, FTA effect
- Model fit (R²)

### Interpretation Example

```
Distance elasticity: -1.12 → 10% increase in distance → 11.2% decrease in trade
FTA effect: 0.45 → FTA increases trade by 57% (exp(0.45)-1)
```

---

## 2. RCA Analysis

**Purpose**: Measure country product competitiveness, identify export advantages

### Core Formulas

#### Balassa RCA Index

```
RCA_ik = (X_ik / X_i_total) / (X_wk / X_w_total)
```

#### Revealed Symmetric Comparative Advantage (RSCA)

```
RSCA = (RCA - 1) / (RCA + 1)
```

### RCA Interpretation

| RCA Range | Interpretation |
|-----------|----------------|
| RCA > 2.5 | Strong comparative advantage |
| 1 < RCA < 2.5 | Comparative advantage |
| 0.5 < RCA < 1 | Comparative disadvantage |
| RCA < 0.5 | Strong comparative disadvantage |

### Input Data Format

```csv
country,year,product,export_value
CHN,2023,851712,50000000
CHN,2023,851713,30000000
USA,2023,851712,20000000
DEU,2023,851712,35000000
```

### Usage

```bash
python calculate_rca.py --data export_data.csv --output rca_results/ --report
```

### Output

- RCA index by country and product
- Top advantage products
- RCA time series trends

---

## 3. Time Series Analysis

**Purpose**: Trade forecasting, trend analysis, seasonal adjustment

### Core Methods

#### ARIMA Model

```
ARIMA(p, d, q):
- p: Autoregressive order
- d: Differencing order
- q: Moving average order
```

#### GARCH Model (Volatility)

```
GARCH(p, q):
- p: GARCH order (volatility persistence)
- q: ARCH order (shock response)
```

### Input Data Format

```csv
date,value
2020-01-01,1000000
2020-02-01,1100000
2020-03-01,1050000
...
```

### Usage

```bash
# Auto-select best model and forecast
python time_series_analysis.py --data trade_series.csv --auto-select --forecast 12

# Volatility analysis
python time_series_analysis.py --data trade_series.csv --garch
```

### Output

- 12-period forecast with 95% confidence intervals
- Model selection results (AIC/BIC)
- Volatility estimates

---

## 4. Risk Management

**Purpose**: Trade risk assessment, tail risk analysis, stress testing

### Core Methods

#### Value at Risk (VaR)

```
VaR(α) = -μ + σ × z_α
```

#### Expected Shortfall (ES)

```
ES(α) = E[Loss | Loss > VaR(α)]
```

#### Extreme Value Theory (EVT)

```
GPD: F(x) = exp(-(1 + ξ(x-μ)/σ)^(-1/ξ))
```

### Risk Metrics

| Metric | Meaning | Typical Range |
|--------|---------|---------------|
| VaR(95%) | Maximum loss at 95% confidence | 2-5% |
| VaR(99%) | Maximum loss at 99% confidence | 3-7% |
| ES(95%) | Expected loss beyond VaR | 1.2-1.5× VaR |
| Tail Index (ξ) | Tail thickness | -0.5 to 0.5 |

### Input Data Format

```csv
date,value
2020-01-01,1000000
2020-01-02,1020000
2020-01-03,980000
...
```

### Usage

```bash
# Calculate VaR and ES
python risk_metrics.py --data trade_series.csv --var --confidence 0.99

# EVT tail analysis
python risk_metrics.py --data trade_series.csv --evt --threshold 0.95

# Copula correlation analysis
python risk_metrics.py --data trade_series.csv --copula gaussian
```

### Output

- VaR/ES risk metrics
- EVT tail parameters
- Dependence structure (Copula)

---

## Recommended Data Sources

### Trade Data

| Source | Coverage | URL |
|--------|----------|-----|
| UN Comtrade | Global, 1962+ | https://comtrade.un.org |
| World Bank WITS | Global, 1988+ | https://wits.worldbank.org |
| CEPII BACI | Global, 1989+ | http://www.cepii.fr |

### Macroeconomic Data

| Source | Variables | URL |
|--------|-----------|-----|
| World Bank WDI | GDP, Population | https://data.worldbank.org |
| IMF WEO | GDP, Inflation | https://www.imf.org/weo |
| Penn World Table | PPP GDP | https://www.rug.nl/ggdc/productivity/pwt |

### Geography & Institutions

| Source | Variables | URL |
|--------|-----------|-----|
| CEPII GeoDist | Distance | http://www.cepii.fr |
| CEPII Gravity | FTA, Language, Colonial | http://www.cepii.fr |
| WTO RTA | Free Trade Agreements | https://www.wto.org/rta |

---

## Dependencies

```bash
pip install pandas numpy statsmodels arch scipy matplotlib
```

---

## Examples

### Example 1: Analyze China-US Trade Potential

```bash
# 1. Prepare data (trade_data.csv)
# 2. Run gravity model
python gravity_model.py --data trade_data.csv --method ppml

# 3. Interpret: If actual trade < predicted → untapped potential
```

### Example 2: Identify China's Competitive Products

```bash
# 1. Prepare export data (export_data.csv)
# 2. Calculate RCA
python calculate_rca.py --data export_data.csv --output results/

# 3. Find products with RCA > 2.5 → Strong competitive advantage
```

### Example 3: Forecast Next Quarter Exports

```bash
# 1. Prepare monthly export series (export_series.csv)
# 2. Auto-model and forecast
python time_series_analysis.py --data export_series.csv --auto-select --forecast 3
```

### Example 4: Assess Export Risk

```bash
# 1. Prepare daily/weekly export data
# 2. Calculate VaR
python risk_metrics.py --data export_series.csv --var --confidence 0.95

# 3. Result: 95% VaR = 5% → 95% confidence loss won't exceed 5%
```

---

## License

MIT License

---

## Version

- **v1.0** (2026-04-23): Initial release with 4 core skills
