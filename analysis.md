## Wallet Credit Score Analysis Report

## Credit Score Distribution

The distribution of wallet credit scores across the analyzed wallets shows a concentration in the mid-range, primarily between 400 and 700.
Based on the analysis of the credit score ranges:

*   **Poor (0-299):** {score_distribution.get('0-99', 0) + score_distribution.get('100-199', 0) + score_distribution.get('200-299', 0)} wallets (0.0%)
*   **Fair (300-499):** {score_distribution.get('300-399', 0) + score_distribution.get('400-499', 0)} wallets ({results_df['credit_rating'].value_counts().get('Fair', 0) / len(results_df) * 100:.1f}%)
*   **Good (500-699):** {score_distribution.get('500-599', 0) + score_distribution.get('600-699', 0)} wallets ({results_df['credit_rating'].value_counts().get('Good', 0) / len(results_df) * 100:.1f}%)
*   **Very Good (700-849):** {score_distribution.get('700-799', 0)} wallets ({results_df['credit_rating'].value_counts().get('Very Good', 0) / len(results_df) * 100:.1f}%)
*   **Excellent (850-1000):** {score_distribution.get('800-899', 0) + score_distribution.get('900-999', 0)} wallets (0.0%)

The histogram plot visually confirms this, with the highest bars in the 400-499 and 500-599 ranges, tapering off significantly towards the extreme ends.

## Behavioral Analysis of Lower vs. Higher Score Wallets

Analyzing the behavioral features of wallets in the lower (< 500) and higher (>= 600) credit score ranges reveals distinct patterns:

**Key Differences:**

*   **Total Transactions:** Higher scoring wallets have significantly more total transactions on average (mean: {higher_score_range_df['total_transactions'].mean():.1f}) compared to lower scoring wallets (mean: {lower_score_range_df['total_transactions'].mean():.1f}). This indicates more active and engaged users tend to have higher scores.
*   **Total Volume (USD):** Wallets with higher scores handle substantially larger total transaction volumes in USD (mean: ${higher_score_range_df['total_volume_usd'].mean():,.0f}) than lower scoring wallets (mean: ${lower_score_range_df['total_volume_usd'].mean():,.0f}). Higher financial activity contributes positively to the score.
*   **Repay-to-Borrow Ratio:** Higher scoring wallets show a much higher average repay-to-borrow ratio (mean: {higher_score_range_df['repay_to_borrow_ratio'].mean():.2f}) compared to lower scoring wallets (mean: {lower_score_range_df['repay_to_borrow_ratio'].mean():.2f}). This suggests that wallets that actively repay their borrowed amounts are associated with better scores.
*   **Liquidation Frequency/Was Liquidated:** Lower scoring wallets have a higher likelihood of having been liquidated (mean `was_liquidated`: {lower_score_range_df['was_liquidated'].mean():.2f}) and a higher liquidation frequency (mean: {lower_score_range_df['liquidation_frequency'].mean():.4f}) than higher scoring wallets (mean `was_liquidated`: {higher_score_range_df['was_liquidated'].mean():.2f}, mean `liquidation_frequency`: {higher_score_range_df['liquidation_frequency'].mean():.4f}). Past liquidations negatively impact the credit score.
*   **Activity Hour Concentration:** Lower scoring wallets tend to have higher activity hour concentration (mean: {lower_score_range_df['activity_hour_concentration'].mean():.2f}) compared to higher scoring wallets (mean: {higher_score_range_df['activity_hour_concentration'].mean():.2f}). This feature, potentially indicating bot-like behavior or concentrated activity, is penalized in the scoring.
*   **Activity and Longevity:** Higher scoring wallets demonstrate greater longevity (mean `activity_span_days`: {higher_score_range_df['activity_span_days'].mean():.1f} days) and are active on more unique days (mean `unique_days_active`: {higher_score_range_df['unique_days_active'].mean():.1f}) than lower scoring wallets (mean `activity_span_days`: {lower_score_range_df['activity_span_days'].mean():.1f} days, mean `unique_days_active`: {lower_score_range_df['unique_days_active'].mean():.1f}). Consistent and long-term engagement contributes positively to the score.
*   **Action Ratios (Deposit/Borrow/Repay/Redeem):** While deposit and redeem ratios are present in both groups, higher scoring wallets show higher average borrow ({higher_score_range_df['borrow_ratio'].mean():.2f}) and repay ({higher_score_range_df['repay_ratio'].mean():.2f}) ratios compared to lower scoring wallets (borrow mean: {lower_score_range_df['borrow_ratio'].mean():.2f}, repay mean: {lower_score_range_df['repay_ratio'].mean():.2f}). This further emphasizes the importance of borrowing and repaying behavior for higher scores.

In summary, wallets with higher credit scores exhibit more consistent and higher volume activity, better repayment behavior, lower incidence of liquidation, and less concentrated activity patterns, aligning with the features that were most influential in the trained model.

