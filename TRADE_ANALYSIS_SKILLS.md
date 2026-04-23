# Trade Analysis Skills

一套用于国际贸易数据分析的统计学方法技能包，专为 AI Agent 设计。

---

## 目录

1. [引力模型 (Gravity Model)](#1-引力模型-gravity-model)
2. [比较优势分析 (RCA Analysis)](#2-比较优势分析-rca-analysis)
3. [时间序列分析 (Time Series)](#3-时间序列分析-time-series)
4. [风险管理 (Risk Management)](#4-风险管理-risk-management)

---

## 1. 引力模型 (Gravity Model)

**用途**: 分析双边贸易流量，预测贸易模式，评估 FTA 效应

### 核心公式

```
Trade_ij = A × (GDP_i^α × GDP_j^β) / Distance_ij^γ × exp(δ×Controls)
```

### 变量说明

| 变量 | 含义 | 预期符号 |
|------|------|----------|
| GDP_i, GDP_j | 出口国和进口国经济规模 | + |
| Distance_ij | 两国间地理距离 | - |
| FTA_ij | 自由贸易协定 | + |
| Common Language | 共同语言 | + |
| Colonial Ties | 殖民关系 | + |

### 输入数据格式

```csv
exporter,importer,year,trade_flow,gdp_exporter,gdp_importer,distance,fta
CHN,USA,2023,500000000,17700000000000,25500000000000,12000,0
CHN,JPN,2023,300000000,17700000000000,4200000000000,3000,1
DEU,FRA,2023,400000000,4200000000000,2900000000000,500,1
```

### 使用方法

```bash
python gravity_model.py --data trade_data.csv --method ppml --fe exporter importer year
```

### 输出结果

- 回归系数及显著性
- 距离弹性、FTA 效应
- 模型拟合优度 (R²)

### 典型结果解读

```
距离弹性：-1.12 → 距离增加 10%，贸易减少 11.2%
FTA 效应：0.45 → FTA 使贸易增加 57% (exp(0.45)-1)
```

---

## 2. 比较优势分析 (RCA Analysis)

**用途**: 衡量国家产品竞争力，识别出口优势产品

### 核心公式

#### Balassa RCA 指数

```
RCA_ik = (X_ik / X_i_total) / (X_wk / X_w_total)
```

#### 对称 RCA (RSCA)

```
RSCA = (RCA - 1) / (RCA + 1)
```

### RCA 值解读

| RCA 范围 | 解读 |
|----------|------|
| RCA > 2.5 | 强比较优势 |
| 1 < RCA < 2.5 | 比较优势 |
| 0.5 < RCA < 1 | 比较劣势 |
| RCA < 0.5 | 强比较劣势 |

### 输入数据格式

```csv
country,year,product,export_value
CHN,2023,851712,50000000
CHN,2023,851713,30000000
USA,2023,851712,20000000
DEU,2023,851712,35000000
```

### 使用方法

```bash
python calculate_rca.py --data export_data.csv --output rca_results/ --report
```

### 输出结果

- 各国各产品的 RCA 指数
- 主要优势产品列表
- RCA 时间序列趋势

---

## 3. 时间序列分析 (Time Series)

**用途**: 贸易数据预测、趋势分析、季节性调整

### 核心方法

#### ARIMA 模型

```
ARIMA(p, d, q):
- p: 自回归阶数
- d: 差分阶数
- q: 移动平均阶数
```

#### GARCH 模型 (波动率)

```
GARCH(p, q):
- p: GARCH 阶数 (波动率持续性)
- q: ARCH 阶数 (冲击响应)
```

### 输入数据格式

```csv
date,value
2020-01-01,1000000
2020-02-01,1100000
2020-03-01,1050000
...
```

### 使用方法

```bash
# 自动选择最优模型并预测
python time_series_analysis.py --data trade_series.csv --auto-select --forecast 12

# 波动率分析
python time_series_analysis.py --data trade_series.csv --garch
```

### 输出结果

- 未来 12 期预测值及 95% 置信区间
- 模型选择结果 (AIC/BIC)
- 波动率估计

---

## 4. 风险管理 (Risk Management)

**用途**: 贸易风险评估、尾部风险分析、压力测试

### 核心方法

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

### 风险指标

| 指标 | 含义 | 典型范围 |
|------|------|----------|
| VaR(95%) | 95% 置信度最大损失 | 2-5% |
| VaR(99%) | 99% 置信度最大损失 | 3-7% |
| ES(95%) | 超越 VaR 的期望损失 | 1.2-1.5× VaR |
| Tail Index (ξ) | 尾部厚度 | -0.5 到 0.5 |

### 输入数据格式

```csv
date,value
2020-01-01,1000000
2020-01-02,1020000
2020-01-03,980000
...
```

### 使用方法

```bash
# 计算 VaR 和 ES
python risk_metrics.py --data trade_series.csv --var --confidence 0.99

# EVT 尾部分析
python risk_metrics.py --data trade_series.csv --evt --threshold 0.95

# Copula 相关性分析
python risk_metrics.py --data trade_series.csv --copula gaussian
```

### 输出结果

- VaR/ES 风险指标
- EVT 尾部参数
- 相关性结构 (Copula)

---

## 数据来源推荐

### 贸易数据

| 来源 | 覆盖 | URL |
|------|------|-----|
| UN Comtrade | 全球，1962+ | https://comtrade.un.org |
| World Bank WITS | 全球，1988+ | https://wits.worldbank.org |
| CEPII BACI | 全球，1989+ | http://www.cepii.fr |

### 宏观经济数据

| 来源 | 变量 | URL |
|------|------|-----|
| World Bank WDI | GDP、人口 | https://data.worldbank.org |
| IMF WEO | GDP、通胀 | https://www.imf.org/weo |
| Penn World Table | PPP GDP | https://www.rug.nl/ggdc/productivity/pwt |

### 地理与制度数据

| 来源 | 变量 | URL |
|------|------|-----|
| CEPII GeoDist | 距离 | http://www.cepii.fr |
| CEPII Gravity | FTA、语言、殖民 | http://www.cepii.fr |
| WTO RTA | 自贸协定 | https://www.wto.org/rta |

---

## 依赖安装

```bash
pip install pandas numpy statsmodels arch scipy matplotlib
```

---

## 使用示例

### 示例 1: 分析中美贸易潜力

```bash
# 1. 准备数据 (trade_data.csv)
# 2. 运行引力模型
python gravity_model.py --data trade_data.csv --method ppml

# 3. 解读结果
# 如果实际贸易 < 预测贸易 → 贸易潜力未充分释放
```

### 示例 2: 识别中国优势产品

```bash
# 1. 准备出口数据 (export_data.csv)
# 2. 计算 RCA
python calculate_rca.py --data export_data.csv --output results/

# 3. 找出 RCA > 2.5 的产品 → 强竞争优势
```

### 示例 3: 预测下季度出口

```bash
# 1. 准备月度出口序列 (export_series.csv)
# 2. 自动建模预测
python time_series_analysis.py --data export_series.csv --auto-select --forecast 3
```

### 示例 4: 评估出口风险

```bash
# 1. 准备日/周出口数据
# 2. 计算 VaR
python risk_metrics.py --data export_series.csv --var --confidence 0.95

# 3. 结果：95% VaR = 5% → 95% 把握损失不超过 5%
```

---

## License

MIT License

---

## 版本

- **v1.0** (2026-04-23): 初始版本，包含 4 个核心技能
