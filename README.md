# Portfolio-Tearsheet-Return-Generator
## Overview
This program creates a brief, two-page document for an investor to summarize the historical outcome of the investor's portfolio transactions over the selected time period. 

## Goals of this Project
1. Allow investors to input their tranaction data via a .csv file
2. Handle time-series transactions based on First-In-First-Out (FIFO) Logic
3. Utilize the QuantStats library to display portfolio statistics against a benchmark
4. Print the statistics output to a .pdf for simpler sharing

## Function Explanations

#### `` def create_market_cal(start, end) ``
- Uses the pandas_market_calendars library to find all relevant trading days within a specified timeframe
- Automatically filters out non-trading days based on the market 
- Sets NYSE as the calendar, and then standardizes the timestamps to make them easy to join on later

#### `` def get_data(stocks, start, end) ``
- Takes an array of stock tickers with a start and end date and grabs the data using the YFinance library

#### `` def position_adjust(daily_positions, sale) ``
- Create an empty dataframe called stocks_with_sales to which we'll add adjusted positions, and another dataframe holding all of the transactions labeled as buys

#### `` def portfolio_start_balance(portfolio, start_date) ``
- Add to the adjusted positions dataframe, the positions that never had sales, sales that occur in the future, and any zeroed out rows to create a record of your active holdings as of the start date

#### `` def fifo(daily_positions, sales, date) ``
- Flters sales to find any that have occurred on the current date and creates a dataframe of positions not affected by sales
- Then use `` position_adjust `` to zero-out any positions with active sales and append the positions with no changes, leaving you with an accurate daily snapshot of your porfolio positions

#### `` def time_fill(portfolio, market_cal) ``
- Provide our dataframe of active positions, find the sales, and zero-out sales against buy positions. 
- Loop through using our market_cal list with valid trading days
- Filter to positions that have occurred before or at the current date and make sure there are only buys. 
- Add a Date Snapshot column with the current date in the market_cal loop, then append it to our per_day_balance list

#### `` def modified_cost_per_share(portfolio, adj_close, start_date) ``
- Merges provided dataframes and calculates daily adjusted cost

#### `` def portfolio_end_of_year_stats(portfolio, adj_close_end) ``
- Finds end date closing prices for each ticker 

#### `` def portfolio_start_of_year_stats(portfolio, adj_close_start) ``
- Finds start date closing prices for each ticker

#### `` def calc_returns(portfolio) ``
- Applies a bunch of calculations against the data we’ve been modifying, and returns a final dataframe

#### `` def per_day_portfolio_calcs(per_day_holdings, daily_adj_close, stocks_start) ``
- Runs `` modified_cost_per_share ``, `` portfolio_end_of_year_stats ``, ``  portfolio_start_of_year_stats ``, `` calc_returns `` and returns a daily snapshot of the portfolio

#### `` def generate_report(returns, folder, rf=0.) ``
- Utilizes the QuantStats library to create a .html and .pdf of the tearsheet returns as well as display the output to the consol with quantitative statistics

## Libraries Used
- [YFinance](https://github.com/ranaroussi/yfinance)
- [QuantStats](https://github.com/ranaroussi/quantstats)
- [PdfKit](https://github.com/JazzCore/python-pdfkit)
- [Pandas](https://github.com/pandas-dev/pandas)
- [Pandas Market Calendars](https://github.com/rsheftel/pandas_market_calendars)
- [MatPlotLib](https://github.com/matplotlib/matplotlib)

## References
- https://towardsdatascience.com/modeling-your-stock-portfolio-performance-with-python-fbba4ef2ef11
- https://github.com/mattygyo/stock_portfolio_analysis/blob/master/portfolio_analysis.py
- https://github.com/ranaroussi/quantstats

