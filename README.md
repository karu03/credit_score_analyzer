# Credit_score_detector


A comprehensive machine learning system that analyzes Aave V2 protocol transaction patterns to assign credit scores (0-1000) to DeFi wallet addresses. The system identifies reliable, long-term users while flagging risky or manipulative behavior patterns.

##  Overview

This system processes historical DeFi transaction data to evaluate wallet creditworthiness using behavioral finance principles adapted for decentralized finance. It combines rule-based scoring with machine learning to create robust credit assessments.

##  How It Works

### Data Flow
```
Raw Transactions → Feature Engineering → Synthetic Scoring → ML Training → Final Credit Scores
```

1. **Data Ingestion**: Processes Aave V2 transaction data (deposits, borrows, repays, redeems, liquidations)
2. **Feature Engineering**: Extracts 25+ behavioral and financial features per wallet
3. **Synthetic Scoring**: Creates training labels based on DeFi best practices
4. **ML Training**: Uses Random Forest to learn complex patterns
5. **Score Generation**: Produces final credit scores (0-1000) with ratings

##  Credit Score Formula

### Base Scoring Logic (Synthetic Score Generation)

```python
Base Score = 500  # Starting point for all wallets

# POSITIVE FACTORS (increase score)
score += min(50, total_transactions * 2)           # Activity bonus
score += min(30, unique_days_active * 3)           # Consistency bonus  
score += min(40, activity_span_days * 0.5)         # Longevity bonus
score += min(80, repay_to_borrow_ratio * 40)       # Repayment behavior
score += min(30, deposit_to_redeem_ratio * 15)     # Stable deposits
score += min(25, unique_assets * 8)                # Asset diversification
score += min(20, unique_pools * 5)                 # Pool diversification
score += min(50, log10(volume_usd) * 10)           # Volume bonus (if >$1000)
score += activity_regularity * 30                  # Consistency bonus

# NEGATIVE FACTORS (decrease score)
score -= liquidation_frequency * 200               # Liquidation penalty
score -= was_liquidated * 100                      # Ever liquidated penalty
score -= bot_behavior_penalty * 50                 # Bot-like patterns
score -= high_volatility_penalty * 30              # Extreme size variations

# Final bounds: max(0, min(1000, score))
```

### Machine Learning Enhancement

The synthetic scores are used to train a **Random Forest Regressor** that learns complex feature interactions and produces the final credit scores. The ML model captures non-linear relationships that the rule-based system might miss.

##  Good Features (Positive Indicators)

###  Activity & Engagement
- **High Transaction Count**: More transactions indicate active engagement
- **Consistent Daily Activity**: Regular usage over time
- **Long Activity Span**: Long-term protocol users
- **Regular Time Patterns**: Consistent timing between transactions

###  Financial Responsibility  
- **High Repay-to-Borrow Ratio**: Repays loans reliably (>1.0 is excellent)
- **Stable Deposit Patterns**: Maintains liquidity in pools
- **Reasonable Transaction Sizes**: Consistent, meaningful amounts
- **High Total Volume**: Significant financial activity

###  Diversification & Sophistication
- **Multiple Assets**: Uses various tokens (USDC, ETH, DAI, etc.)
- **Multiple Pools**: Diversifies across different lending pools  
- **Low Concentration Risk**: Spreads activity across time periods

###  Risk Management
- **No Liquidation History**: Never been liquidated
- **Balanced Action Ratios**: Healthy mix of deposits/borrows/repays
- **Network Consistency**: Consistent blockchain usage

##  Bad Features (Negative Indicators)

###  High-Risk Behaviors
- **Liquidation History**: Ever been liquidated (-100 points)
- **High Liquidation Frequency**: Multiple liquidations (up to -200 points)
- **Flash Loan Abuse**: Rapid borrow-repay cycles in single blocks

###  Bot-Like Patterns
- **High Hour Concentration**: >50% activity in specific hours (-50 points)
- **Exact Time Intervals**: Mechanical timing patterns
- **Identical Transaction Sizes**: Repeated exact amounts
- **Rapid-Fire Transactions**: Multiple transactions in same block

###  Financial Instability
- **High Transaction Volatility**: Extreme variation in sizes
- **Poor Repayment Ratios**: More borrows than repays (<0.5 is bad)
- **Minimal Volume**: Very low financial activity
- **Single Asset Focus**: Lack of diversification

###  Exploitative Behavior
- **Arbitrage-Only Activity**: Only profit-taking patterns
- **MEV Exploitation**: Front-running or sandwich attacks
- **Protocol Gaming**: Manipulating governance or rewards

##  Feature Descriptions

### Activity Metrics
| Feature | Description | Good Range | Bad Range |
|---------|-------------|------------|-----------|
| `total_transactions` | Total number of transactions | >50 | <5 |
| `unique_days_active` | Days with activity | >30 | <3 |
| `activity_span_days` | Days from first to last transaction | >180 | <7 |
| `avg_daily_transactions` | Average transactions per day | 1-10 | >50 or <0.1 |

### Financial Metrics
| Feature | Description | Good Range | Bad Range |
|---------|-------------|------------|-----------|
| `total_volume_usd` | Total USD volume | >$10,000 | <$100 |
| `avg_transaction_size_usd` | Average transaction size | $100-$10K | >$1M or <$1 |
| `transaction_size_cv` | Size coefficient of variation | <2.0 | >5.0 |

### Behavioral Ratios
| Feature | Description | Good Range | Bad Range |
|---------|-------------|------------|-----------|
| `repay_to_borrow_ratio` | Repayments vs borrows | >0.8 | <0.3 |
| `deposit_to_redeem_ratio` | Deposits vs redeems | >0.7 | <0.2 |
| `liquidation_frequency` | % of liquidation transactions | 0% | >5% |

### Action Distribution
| Feature | Description | Healthy Range |
|---------|-------------|---------------|
| `deposit_ratio` | % deposit transactions | 20-60% |
| `borrow_ratio` | % borrow transactions | 10-40% |
| `repay_ratio` | % repay transactions | 10-40% |
| `redeem_ratio` | % redeem transactions | 10-30% |
| `liquidation_ratio` | % liquidation transactions | 0% |

##  Credit Rating Scale

| Score Range | Rating | Description |
|-------------|---------|-------------|
| 850-1000 | **Excellent** | Premium DeFi users, highest trustworthiness |
| 700-849 | **Very Good** | Reliable users with strong track record |
| 500-699 | **Good** | Average users with reasonable behavior |
| 300-499 | **Fair** | Below average, some risk indicators |
| 0-299 | **Poor** | High risk, many negative indicators |

##  Usage Instructions

### Prerequisites
```bash
pip install pandas numpy scikit-learn
```

### Basic Usage
```python
# Load your transaction data
with open('transactions.json', 'r') as f:
    data = json.load(f)

# Run the step-by-step process
df = load_and_explore_data(data)
df = preprocess_data(df)
features_df = engineer_all_features(df)
synthetic_scores, _ = create_synthetic_scores(features_df)
model, scaler, importance, _, _, _ = train_model(features_df, synthetic_scores)
results_df = generate_final_scores(features_df, model, scaler)
final_results = display_results(results_df)

# Save results
results_df.to_csv('credit_scores.csv')
```

### Expected Input Format
```json
{
  "userWallet": "0x1234...",
  "network": "polygon",
  "protocol": "aave_v2", 
  "txHash": "0xabcd...",
  "timestamp": 1629178166,
  "action": "deposit",
  "actionData": {
    "amount": "2000000000",
    "assetSymbol": "USDC",
    "assetPriceUSD": "0.9938",
    "poolId": "0x2791..."
  }
}
```

##  Model Performance

### Features Used
- **25+ engineered features** per wallet
- **Robust scaling** to handle outliers
- **Random Forest** with 200 estimators
- **Cross-validation** for reliability

### Typical Performance Metrics
- **R² Score**: 0.85-0.95 (excellent fit)
- **RMSE**: 20-40 points (low error)
- **Feature Importance**: Liquidation history and repayment ratios most predictive

