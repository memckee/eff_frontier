# How Durable is the Heuristic?
## Measuring Efficient Portfolio Allocation Against a 60/40 Allocation

# Introduction

Nobel laureate Harry Markowitz's work provides a compelling representation of the trade-off between risk and return. His most well-known visual illustration of this trade-off is the "efficient frontier" (EF). Given a set of investment selections with varying weights in a portfolio, the EF represents the most efficient allocations for each possible return. The highest efficiency, in this case, is measured by the lowest volatility for a given return level. For decades, investors have benefitted from Markowitz's insights into asset allocation.

Investors not as inclined to elegant theoretical models have also benefitted from allocation heuristics for portfolio construction. One of the most enduring allocation models for US investors is portfolio comprised of equities and bonds in a 60%/40% allocation, respectively. 

How well does the theoretical map onto practical decision making? This project explores using the EF for real portfolio allocations. Return data for exchange-traded funds (ETFs) is used in modeling the EF and choosing weights for a portfolio of those ETFs. Performance is then measured against the "60/40" portfolio.

# The Efficient Frontier in Python

Given historical returns of component portfolios, the set of efficient portfolios can be calculated using optimization function in Python. For a given return expectation, the minimum variance portfolio can be found through finding the investment weights that minimize portfolio variance.

The scipy library's minimize function allows for simplistic coding of this problem. To generate the frontier, this analysis makes some assumptions about a hypothetical retail investor:

* The portfolio will be long only.
* The investor is not able to borrow at the risk-free rate.  

This hypothetical investor looks at highly liquid, well-established ETFs as low-cost investment vehicles to form her portfolio:

| Ticker | Name                                          | Asset Class                            |
|--------|-----------------------------------------------|----------------------------------------|
| SPY    | SPDR S&P 500 ETF                              | US Equity                              |
| EFA    | iShares MSCI EAFE ETF                         | International Equity                   |
| AGG    | iShares Core U.S. Aggregate Bond ETF          | Government and Investment Grade Credit |
| HYG    | iShares iBoxx $ High Yield Corporate Bond ETF | High Yield Credit                      |
| GLD    | SPDR Gold Shares                              | Gold                                   |
| VNQ    | Vanguard Real Estate ETF                      | Real Estate                            |

## Plotting the Frontier

We can explore the frontier visually using two methods.

The first method uses random guesses of investment weights and plots the resulting portfolio's return and annualized volatility. 

The second method uses scipy's *minimize* to plot the frontier for each return level as described above. 

The below plot shows both methods using return data through December 2019. The randomly chosen portfolios are shaded according to their Sharpe ratio. The expected returns in this case are the average annual return since ETF inception. The maximum return in this frontier, because we assume a long-only portfolio, is a portfolio invested 100% in VNQ, which has the highest expected return. The lowest return is a portfolio invested 100% in AGG.

![Current Frontier](/Images/current_frontier.png)

## Constructing Efficient Portfolios

How do we use this to select and evaluate portfolios? We can assume that our hypothetical investor would prefer to be compensated for investment risk and use minimum volatility portfolios where possible. In evaluating portfolio selections for retail investors, many firms use risk tolerances to determine investment allocations. This analysis adopts that approach by segregating the upper section of the frontier "parabola" and selecting five portfolios based on volatility. The five portfolios are selected based on quantiles of volatility as outlined below:

| Risk Tolerance Level    | Abbreviation | Quantile                |
|-------------------------|--------------|-------------------------|
| Conservative            | cons         | 0% (Minimum Volatility) |
| Moderately Conservative | mod_cons     | 25%                     |
| Moderate                | mod          | 50%                     |
| Moderately Aggressive   | mod_agg      | 75%                     |
| Aggressive              | agg          | 90%                     |

The efficient frontier of portfolios with individual ETFs plotted looks like this:

![2019 Frontier](/Images/eff_port_frontier_2019.png)

A portfolio is said to "dominate" another if it provides a higher compensation for risk. In visual terms, a portfolio dominates another if it is above and to the right on the frontier plot. The five portfolios dominate most of the individual ETFs.

## Efficient Portfolio Performance in 2019

What if our hypothetical investor had used this analysis to construct her portfolio? Investing at the beginning of 2019 with the above allocations above would result in the following allocations:

![2019 Weights](/Images/eff_weights_2019.PNG)

![2019 Weight Graph](/Images/eff_port_weight_graph_2019.png)

We see an increasing allocation to gold and real estate as risk tolerance increases. GLD, which performed well as a counter-cyclical asset during the great recession, is given higher weight due to the higher return assumptions. A conservative allocation, predictably, is almost all fixed income. No other instrument in the analysis matches the low volatility of AGG. EFA is dominated by other securities, so its absence in the five portfolios is expected given the return assumptions. 

These allocations provide result in the following portfolio performance for 2019:

![2019 Performance](/Images/eff_port_returns_2019.png)

An aggressive allocation consisting mostly of VNQ and GLD would have provided the highest returns of the five options. However, many retail investors look at the S&P 500 as a performance benchmark. Considering that firms such as Fidelity and Charles Schwab have introduced US equity ETFs at no cost, retail investors have access to free beta. In more rigorous performance analysis, more appropriate benchmarks would be used, but here we consider a retail investor looking longingly at SPY, which returned 31.22% in 2019.

![2019 Statistics](/Images/eff_port_stats_2019.PNG)

As measured by the Sharpe ratio, four of the five portfolios constructed using the efficient frontier provided higher compensation for investment risk, which is the point of the minimum variance analysis. An investor purely chasing returns will not monitor volatility well enough. This is one of Markowitz's key contributions to asset allocation analysis. Sure, our efficient portfolios underperformed the market, but a *risk-adjusted* basis reveals more nuance.

The other statistics provided above detail other nuances of performance analysis. The Sortino ratio, which measures compensation for downside deviation, shows that the efficient portfolios also provided greater excess return over the risk-free rate relative to periods of negative performance. 

The aggressive portfolio, which provided impressive alpha over SPY, suffered because of significant underperformance resulting in a very poor down capture for the year. Again, SPY would not be the most appropriate benchmark for a portfolio of gold and real estate, but when retail investors hear quotes of the S&P 500 Index's performance multiple times a day, these measurements can matter. 

# Backtesting the Efficient Frontier

As a model of portfolio allocation optimization, the minimum variance approach allows for backtesting the returns over time.

Taking the same hypothetical investor, we assume that she rebalances a portfolio of ETFs annually using the frontier framework. Each December, she invests based on her risk tolerance in an efficient portfolio. 

## Efficient Allocation Weights Over Time

With these assumptions, the five portfolios change weights over time as shown below. AGG composes over 90% of the conservative portfolio each year, and the aggressive portfolio comprises a shifting combination portfolios heavily weighted to GLD. 

SPY sees very little exposure in any portfolio, and EFA sees none. This means that the minimum variance optimization sees little risk-adjusted value in equities for the expected returns and covariances provided.

![Cons Weights](/Images/port_all_cons.png)

![Mod-Cons Weights](/Images/port_all_mod_cons.png)

![Mod Weights](/Images/port_all_moder.png)

![Mod-Agg Weights](/Images/port_all_mod_agg.png)

![Agg Weights](/Images/port_all_agg.png)

## Evaluating Performance

How would our hypothetical investor do if she decided to eschew the minimum variance analysis and opt for an heuristic? This analysis constructs an annually rebalancing 60% SPY/40% AGG portfolio that uses no optimization whatsoever and makes no consideration for risk tolerance.

The visualizations below show the results. All relative performance measures again use SPY as a benchmark for consistency. The violin plots show rolling statistics measured on a monthly basis for each portfolio. The box plots inside the individual distributions show the quartiles of the rolling values.

Clearly, the 60/40 portfolio outperforms by almost every measure. The annualized return is almost double that of the highest efficient portfolio.

The rolling Sharpe ratios for the 60/40 portfolio exceed those of the efficient portfolio for almost all period. The right skew of the rolling three year Sharpe distribution, provided without any negative value and no left skew, demonstrates superior risk-adjusted performance. On a five year basis, only the conservative portfolio comes close because of the upper half of the distribution.

The highly variable annualized volatility of the more aggressive portfolios bear out the poor Sharpe ratios. The conservative portfolio maintained low volatility throughout the period due to its almost exclusive investment in AGG. 

The below visualizations also show maximum drawdown. The more aggressive portfolios, with higher exposure to GLD and VNQ, were punished by concentrating in counter-cyclical assets in one of the longest equity bull markets in history.

![Performance Graph](/Images/port_returns.png)

![Rolling Returns](/Images/port_stat_return.png)

![Rolling Vol](/Images/port_stat_vol.png)

![Rolling Sharpe](/Images/port_stat_sharpe.png)

![Rolling Beta](/Images/port_stat_beta.png)

![Rolling Alpha](/Images/port_stat_alpha.png)

![Rolling Sortino](/Images/port_stat_sortino.png)

![Rolling Up Capture](/Images/port_stat_up_cap.png)

![Rolling Down Capture](/Images/port_stat_down_cap.png)

![Rolling Max DD](/Images/port_stat_max_dd.png)

# Discussion and Next Steps

As a theoretical model for discussing the trade-off between risk and return, the minimum variance framework is both elegant and visually compelling. As a model for predicting optimal investment decisions, it leaves much to be desired. The 60/40 heuristic outperformed a backtested model using diversified ETFs on an absolute and a risk-adjusted basis.

This analysis made certain assumptions and structured data in a certain way. Further steps would need to be addressed prior to adopting this framework for practical use. Like other models, the efficient frontier allocations are highly sensitive to inputs. Parameter optimization and research would need to be undertaken for:

* Expected return calculation
* Monthly vs daily returns
* Utilizing different securities to form a portfolio
* Obtaining data to extend investment history

This project is limited to free data available from public sources. With expanded market data access, such as that provided by a Bloomberg terminal or FactSet, one can expand time series with market index data to make covariance and expected return calculations more robust. This analysis is limited by history limits, so most of the statistics are calculated using data over one of the longest equity bull markets in history. Would the minimum variance framework be more compelling over a full market cycle?

That said, this analysis reveals the sturdiness of a well-used allocation. The 60/40 heuristic is a heuristic for a reason. However, an extended bull market might cause over-concentration in that allocation, so we should not hastily call it undisputed.

The appendix in the Jupyter notebook shows additional simulations of this approach. Because the above analysis resulted in extremely high allocations to GLD, one simulation removes GLD and causes over-allocation to VNQ. The final simulation takes SPY and AGG to look at what allocations the frontier calls efficient. This allows access to more history that extends through the 2008 recession. The resulting portfolios do not outperform on an absolute basis, but four of the five portfolios outperform on a risk-adjusted basis.