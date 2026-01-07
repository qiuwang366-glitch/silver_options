# SHFE Silver (AG) Advanced Market Analysis Tool

**A quantitative framework for derivatives trading on Shanghai Futures Exchange Silver contracts.**

---

## 🎯 Project Overview

This tool provides institutional-grade analysis for SHFE Silver futures and options, combining:

- **SABR Volatility Modeling** with rigorous mathematical calibration
- **Market Microstructure Analysis** via Dealer Gamma Exposure (GEX)
- **Volatility Skew Metrics** (Risk Reversal, Butterfly)
- **Actionable Trading Strategies** synthesized from multiple signals

**Target Users:** Quantitative traders, portfolio managers, and financial mathematics researchers working in commodity derivatives.

---

## 📊 Features

### 1. **Data Ingestion (Wind API)**
- Snapshot data for AG2604 futures contract
- Full option chain with real-time implied volatility
- Robust error handling for illiquid/non-trading contracts

### 2. **SABR Model Calibration**
- Hagan et al. formula implementation with numerical stability
- Optimized parameter estimation (α, β, ρ, ν)
- Volatility surface visualization

### 3. **Skew Analysis**
- Risk Reversal (25Δ): Measures directional volatility bias
- Butterfly (25Δ): Quantifies tail risk premium
- Correlation interpretation (ρ parameter)

### 4. **Dealer Gamma Exposure (GEX)**
- Strike-by-strike gamma positioning
- Identifies:
  - **Gamma Walls**: Price magnets (dealers long gamma)
  - **Zero Gamma Levels**: Volatility regime transition points
  - **Negative GEX Zones**: Volatility acceleration regions

### 5. **Strategic Output**
- Dynamic trading strategies based on live market conditions
- Regime-dependent recommendations (mean reversion vs. momentum)
- Risk warnings and model limitations

---

## 🚀 Quick Start

### Prerequisites

1. **Python 3.9+**
2. **Wind API Access** (WindPy)
   - Contact [Wind Information](https://www.wind.com.cn/) for API credentials
   - Install WindPy per Wind's instructions

### Installation

```bash
# Clone or download this repository
cd silver_options

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook analysis.ipynb
```

### Running the Analysis

1. **Ensure Wind API is connected**:
   ```python
   from WindPy import w
   w.start()  # Should return "Welcome to Wind API"
   ```

2. **Execute all cells** (Cell > Run All)

3. **Review outputs**:
   - Futures market diagnostics
   - SABR calibration results
   - Volatility smile charts
   - GEX profiles
   - **Trader's Note** (strategic summary)

---

## 📂 Project Structure

```
silver_options/
├── analysis.ipynb          # Main Jupyter notebook
├── requirements.txt        # Python dependencies
└── README.md              # This file
```

---

## 🔬 Methodology

### SABR Model

The Stochastic Alpha Beta Rho model describes forward price and volatility dynamics:

```
dF_t = σ_t F_t^β dW_t^F
dσ_t = ν σ_t dW_t^σ
dW_t^F · dW_t^σ = ρ dt
```

**Parameters:**
- **α** (alpha): Initial volatility level
- **β** (beta): CEV exponent (0=Normal, 1=Lognormal)
- **ρ** (rho): Correlation between asset price and volatility
- **ν** (nu): Volatility of volatility

**Calibration:** Minimize sum of squared errors between market and model implied volatilities.

### Gamma Exposure (GEX)

**Assumption:** Market makers (dealers) are net short client option positions.

- **Negative GEX**: Dealers short gamma → Must chase price → Amplifies volatility
- **Positive GEX**: Dealers long gamma → Provide liquidity → Dampens volatility
- **Zero Gamma**: Regime transition point

**Formula:**
```
GEX_strike = - (Call_OI × Γ_call + Put_OI × Γ_put) × Contract_Size
```

---

## 📈 Sample Outputs

### Volatility Smile
![Volatility Smile](https://via.placeholder.com/800x400.png?text=SABR+Volatility+Surface)

### GEX Profile
![GEX Profile](https://via.placeholder.com/800x400.png?text=Dealer+Gamma+Exposure)

*(Actual outputs generated from live data)*

---

## ⚠️ Risk Warnings

### Model Limitations
1. **SABR Assumptions**:
   - Continuous hedging (unrealistic in practice)
   - No jumps (commodities can gap)
   - Fixed β parameter (may vary over time)

2. **GEX Assumptions**:
   - Dealers are net short (not always true)
   - Ignores transaction costs and slippage
   - Assumes delta hedging behavior

### Execution Risks
- **Illiquid options**: Wide bid-ask spreads erode profitability
- **Overnight gaps**: Commodity markets prone to discontinuous moves
- **Model risk**: Historical patterns may not persist

**Always validate signals with fundamental analysis and risk controls.**

---

## 🛠️ Customization

### Adjusting Parameters

**SABR Beta (CEV exponent):**
```python
# In the calibration cell:
sabr_params = SABRModel.calibrate(
    strikes=calib_calls['strike'].values,
    market_vols=calib_calls['iv'].values,
    forward=futures.price,
    time_to_expiry=T,
    beta=0.5  # Change here: 0 (Normal) to 1 (Lognormal)
)
```

**Time to Expiry:**
```python
# Calculate actual days to maturity:
from datetime import datetime
expiry_date = datetime(2026, 4, 15)  # AG2604 maturity
T = (expiry_date - datetime.now()).days / 365
```

**GEX Contract Size:**
```python
# Adjust multiplier in GEX calculation:
call_gex = -call_oi * gamma_val * 15  # SHFE Silver = 15 kg/contract
```

---

## 📚 References

### Academic
1. Hagan, P. et al. (2002). "Managing Smile Risk." *Wilmott Magazine*.
2. Gatheral, J. (2006). *The Volatility Surface: A Practitioner's Guide*. Wiley.
3. Perlin, M. et al. (2017). "Gamma Exposure and Market Dynamics." *Journal of Financial Markets*.

### Market Data
- [Shanghai Futures Exchange](http://www.shfe.com.cn/)
- [Wind Information](https://www.wind.com.cn/)

---

## 🤝 Contributing

This tool is designed for research and educational purposes. For improvements:

1. **Bug Reports**: Open an issue with reproducible example
2. **Feature Requests**: Describe use case and expected behavior
3. **Pull Requests**: Follow PEP 8, include tests

---

## 📄 License

**For Educational and Research Use Only.**

This code is provided "as-is" without warranty. The author assumes no liability for trading losses. Always conduct independent verification and risk management.

---

## 👤 Author

**Senior Quantitative Researcher**
Specialized in commodity derivatives and volatility modeling.

*For questions or collaboration: Contact via repository issues.*

---

## 🙏 Acknowledgments

- Wind Information for market data infrastructure
- SABR model pioneers (Hagan, Kumar, Lesniewski, Woodward)
- The quantitative finance community

---

**Disclaimer:** Past performance does not guarantee future results. Derivatives trading involves substantial risk of loss. Consult professional advisors before trading.
