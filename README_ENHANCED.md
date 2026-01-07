# SHFE Silver (AG) Enhanced Market Analysis Tool

**🎯 一个专业级的上海期货交易所白银期权分析工具**

---

## 🆕 增强版本特性

### 主要改进

#### 1. **智能数据获取** (解决了原始问题！)
- ✅ **ATM聚焦过滤**: 自动过滤到现货价格附近±15-20%的期权
- ✅ **动态执行价范围**: 确保每侧至少15个执行价，无论市场在哪个价格水平
- ✅ **适配19000价格水平**: 完美处理白银19000左右的真实价格场景
- ✅ **智能合约选择**: 优先选择流动性好的ATM期权进行分析

**解决的问题**:
- 原程序：获取从6000开始的所有执行价（数据量大且不相关）
- 新程序：只获取16000-22000等ATM附近期权（精确且高效）

#### 2. **3D波动率曲面可视化**
- 📊 交互式3D图表（执行价 × 到期时间 × 隐含波动率）
- 🎨 Plotly动态可视化，支持旋转、缩放
- 📈 完整波动率期限结构展示
- 🔍 市场数据点叠加验证

#### 3. **进阶Greeks分析**
- **Delta Profile**: 看涨/看跌期权的Delta曲线
- **Gamma Profile**: 按执行价的Gamma分布（找到最大曲率点）
- **Vega Profile**: 波动率敏感性分析
- **Theta Profile**: 时间价值衰减曲线
- **OI加权Greeks**: 结合持仓量的实际市场影响

#### 4. **增强的期权分析指标**

##### 隐含波动率结构分析
```
- Deep OTM Put  (90-95%)   : 平均IV、持仓量
- OTM Put       (95-98%)   : 平均IV、持仓量
- ATM           (98-102%)  : 平均IV、持仓量
- OTM Call      (102-105%) : 平均IV、持仓量
- Deep OTM Call (105-110%) : 平均IV、持仓量
```

##### Put-Call Parity Check
- 验证市场套利机会
- 检测定价异常
- 识别流动性问题

#### 5. **更全面的策略生成**
- **基于实际regime的策略**:
  - Negative Gamma → 趋势策略（买入跨式/宽跨式）
  - Positive Gamma → 做空波动率（铁蝶、铁秃鹰）
  - Neutral → 等待确认或方向性突破
- **风险管理框架**:
  - 模型局限性说明
  - 执行风险提示
  - 仓位大小指南
- **关键技术位**:
  - 支撑/加速位（Max Negative GEX）
  - 阻力/墙位（Max Positive GEX）
  - Zero Gamma翻转点

---

## 📊 完整分析流程

### 1. 数据层
```python
# 智能获取期权链（只取ATM附近）
options = fetcher.fetch_option_chain_atm_focused(
    underlying_code="AG2604.SHF",
    spot_price=19000,           # 实时期货价格
    atm_range_pct=0.15,         # ±15%范围
    min_strikes_per_side=15     # 每侧最少15个执行价
)
# 结果: 约30-40个相关期权合约，而非几百个
```

### 2. 模型层
- **SABR校准**: 优化α, ρ, ν参数（β=0.7固定）
- **Greeks计算**: Black-Scholes-Merton完整希腊字母
- **GEX建模**: 做市商Gamma暴露分析

### 3. 可视化层
- **2D图表**: 4×4 Greeks仪表盘
- **3D曲面**: 波动率曲面（Strike × Time × IV）
- **GEX图**: 按执行价的做市商仓位

### 4. 策略层
- **动态策略生成**: 基于实时市场结构
- **风险提示**: 模型假设和执行风险
- **执行参考**: 具体的执行价和策略结构

---

## 🚀 快速开始

### 环境要求
```bash
# Python 3.9+
pip install -r requirements.txt

# 需要Wind API访问权限
# 联系 https://www.wind.com.cn/
```

### 运行分析
```bash
# 启动Jupyter
jupyter notebook analysis_enhanced.ipynb

# 或使用JupyterLab
jupyter lab analysis_enhanced.ipynb
```

### 配置参数
```python
# 在notebook顶部修改这些参数:

UNDERLYING = "AG2604.SHF"      # 标的合约
ATM_RANGE_PCT = 0.15           # ATM范围（±15%）
MIN_STRIKES = 15               # 最少执行价数量
T = 90 / 365                   # 到期时间（天数/365）
```

---

## 📈 输出示例

### 市场快照
```
═══════════════════════════════════════
FETCHING MARKET DATA: AG2604.SHF
═══════════════════════════════════════

📊 FUTURES SNAPSHOT
   Price:              19,000.00
   Bid/Ask:            18,995 / 19,005
   Spread:                  5.3 bps
   Volume:                45,000
   Open Interest:        180,000
   Vol/OI Ratio:           25.0%

🔍 Fetching option chain for AG2604.SHF...
   ATM Price: 19,000
   Target Range: ±15% [16,150 - 21,850]
   ✓ Filtered to 62 contracts (31 unique strikes)
   Strike Range: 16,200 - 21,800

📈 OPTION CHAIN SUMMARY
   Total Contracts:  62
   Liquid Contracts: 58
```

### SABR校准结果
```
═══════════════════════════════════════
SABR MODEL CALIBRATION
═══════════════════════════════════════

SABR Parameters:
   α (alpha):      0.2234  ← Initial volatility
   β (beta):       0.7000  ← CEV exponent (fixed)
   ρ (rho):       -0.3156  ← Asset-Vol correlation
   ν (nu):         0.4127  ← Vol-of-vol

   Forward:       19,000.00
   Time to Exp:    0.2466 years

Calibration RMSE: 0.0089 (0.89% vol points)
```

### Skew指标
```
═══════════════════════════════════════
VOLATILITY SKEW METRICS
═══════════════════════════════════════

ATM Strike:        19,000
ATM IV:             22.34%

25Δ Call Strike:   20,200
25Δ Call IV:        20.15%

25Δ Put Strike:    17,800
25Δ Put IV:         24.89%

────────────────────────────────────────
Risk Reversal:      -4.74 vol pts
Butterfly:          +0.18 vol pts
────────────────────────────────────────

🎯 INTERPRETATION:

   📉 NEGATIVE RR (-4.7) → PUT SKEW DOMINANT
      Market is pricing downside crash protection.

   ρ (SABR): -0.316
      → Leverage effect: Vol rises when price falls
```

### GEX分析
```
═══════════════════════════════════════
DEALER GAMMA EXPOSURE (GEX) SUMMARY
═══════════════════════════════════════

Current Spot:              19,000

Max Negative GEX:          17,600  (Dealers SHORT Gamma)
   Magnitude:         -2,345,678

Max Positive GEX:          20,400  (Dealers LONG Gamma)
   Magnitude:          1,234,567

Zero Gamma Level:          18,800
Distance from Spot:           200
                            (1.1%)
```

### 交易策略建议
```
═══════════════════════════════════════
4. ACTIONABLE TRADING STRATEGIES
═══════════════════════════════════════

   📉 PRIMARY STRATEGY: Bearish Volatility Expansion

      Market Setup:
      • Strong put skew (-4.7) + Negative GEX
      • Downside acceleration risk underpriced
      • Dealer hedging will amplify moves

      Recommended Trade:
      • BUY 17,800 / 17,300 Put Spread
      • Size: Conservative (gap risk present)
      • Target: Break below 17,600
      • Stop: Above 19,000 (regime shift)

      Profit Catalyst:
      If spot breaches 17,600, dealer hedge selling
      creates non-linear price acceleration.

      Risk:
      Premium decay if market consolidates.
```

---

## 🔬 技术细节

### SABR模型公式
$$
\sigma_{IV}(K) \approx \frac{\alpha}{(FK)^{(1-\beta)/2}} \times \frac{z}{x(z)} \times [1 + T \times corrections]
$$

其中:
- $z = \frac{\nu}{\alpha}(FK)^{(1-\beta)/2}\log(F/K)$
- $x(z) = \log\left(\frac{\sqrt{1-2\rho z + z^2} + z - \rho}{1-\rho}\right) / z$

### Dealer GEX计算
$$
GEX_K = -\left( OI_{Call} \times \Gamma_{Call} + OI_{Put} \times \Gamma_{Put} \right) \times K \times 15
$$

- 负号: 做市商与客户相反（客户买，做市商卖）
- 15: SHFE白银每手15公斤

### Black-Scholes Gamma
$$
\Gamma = \frac{n(d_1)}{S \sigma \sqrt{T}}
$$

---

## 🎨 可视化示例

### 1. 波动率微笑（4象限）
- 执行价空间的Smile
- Moneyness空间的Smile
- 模型拟合质量（散点图）
- 残差分析

### 2. 3D波动率曲面
- X轴: 执行价（16,000 - 22,000）
- Y轴: 到期时间（1周 - 6个月）
- Z轴: 隐含波动率（%）
- 颜色: Viridis色谱

### 3. Greeks仪表盘
- Delta曲线（C vs P）
- Gamma分布（peak at ATM）
- Vega profile
- Theta decay

### 4. GEX图表
- Net GEX柱状图（红色=负，蓝色=正）
- OI分布（Call vs Put）
- 现货价格标记线
- 关键技术位标注

---

## ⚠️ 重要注意事项

### 模型假设
1. **SABR模型**:
   - 假设连续对冲（实际有交易成本）
   - 不考虑跳跃（商品市场会跳空）
   - β参数固定（可能随时间变化）

2. **GEX分析**:
   - 假设做市商是客户的对手方（不总是成立）
   - 忽略交易摩擦和滑点
   - 假设Delta对冲行为

### 执行风险
- **流动性**: 检查bid-ask价差
- **滑点**: 大单会影响成交价
- **隔夜风险**: 商品市场容易跳空
- **保证金**: 确保账户有足够保证金

### 数据质量
- **实时性**: Wind数据有轻微延迟
- **异常值**: 非流动执行价可能有陈旧报价
- **持仓量**: OI数据日更新（非实时）

---

## 📚 与原版本对比

| 特性 | 原版 (analysis.ipynb) | 增强版 (analysis_enhanced.ipynb) |
|------|----------------------|----------------------------------|
| **数据获取** | 全部期权（6000-7100+） | ATM聚焦（±15%） ✨ |
| **执行价数量** | ~20-30个（可能不相关） | 30-40个（全在ATM附近） ✨ |
| **适配价格** | 6500左右 | 19000左右 ✨ |
| **Greeks分析** | 基础计算 | 完整Profile + 可视化 ✨ |
| **可视化** | 2D图表 | 2D + 3D曲面 ✨ |
| **IV分析** | Skew metrics | Skew + Structure + Parity ✨ |
| **策略建议** | 通用策略 | 基于regime的动态策略 ✨ |
| **风险管理** | 基础提示 | 完整框架 ✨ |
| **代码组织** | 单文件 | 模块化（可扩展） ✨ |

---

## 🛠️ 自定义和扩展

### 修改ATM范围
```python
# 更宽的范围（±20%）
ATM_RANGE_PCT = 0.20

# 更窄的范围（±10%）
ATM_RANGE_PCT = 0.10
```

### 调整SABR的β参数
```python
# β=0.5 (更接近Normal模型)
sabr_params = SABRModel.calibrate(
    ...,
    beta=0.5
)

# β=1.0 (纯Lognormal模型)
sabr_params = SABRModel.calibrate(
    ...,
    beta=1.0
)
```

### 修改到期时间
```python
# 计算实际到期日
from datetime import datetime
expiry_date = datetime(2026, 4, 15)  # AG2604到期日
T = (expiry_date - datetime.now()).days / 365
```

---

## 📖 使用建议

### 日常工作流
1. **每日早盘**: 运行分析获取最新市场结构
2. **监控关键位**: 关注GEX的Zero Gamma和Max GEX位置
3. **Regime变化**: 价格突破关键位时重新分析
4. **策略调整**: 根据Skew和GEX变化调整仓位

### 策略验证
1. 查看历史数据验证策略有效性
2. 小仓位测试执行质量
3. 记录IV实现值vs预测值
4. 定期校准模型参数

### 风险控制
1. 单笔风险≤账户2%
2. Negative Gamma regime减少仓位
3. 设置明确的止损位
4. 监控隔夜仓位（gap风险）

---

## 🤝 贡献和反馈

### 报告问题
- 在GitHub开issue
- 提供复现步骤
- 附上数据快照（如可能）

### 功能请求
- 描述使用场景
- 说明预期行为
- 提供示例数据

### Pull Requests
- 遵循PEP 8
- 添加docstrings
- 包含测试用例

---

## 📄 许可证

**仅供教育和研究使用**

本代码按"原样"提供，不提供任何保证。作者不对交易损失承担责任。
请务必进行独立验证和风险管理。

---

## 👤 作者

**Senior Quantitative Researcher**
专注于商品衍生品和波动率建模

*如有问题或合作，请通过repository issues联系*

---

## 🙏 致谢

- Wind Information提供的市场数据基础设施
- SABR模型先驱者（Hagan, Kumar, Lesniewski, Woodward）
- 量化金融社区的支持

---

**免责声明**: 过去表现不代表未来结果。衍生品交易涉及重大亏损风险。
交易前请咨询专业顾问。

---

**最后更新**: 2026-01-07
**版本**: Enhanced v2.0
**适用合约**: SHFE AG2604及后续月份

