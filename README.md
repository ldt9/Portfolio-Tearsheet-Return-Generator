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

#### `` def read_csv(folder, csv_name) ``
- Takes a folder name and filename for where the .csv if located and creates a dataframe out of it

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

#### `` def format_returns(pdpc, metric_to_group) ``
- Formats the per day portfolio calculations for use with the QuantStats library

#### `` def generate_report(returns, folder, rf=0.) ``
- Utilizes the QuantStats library to create a .html and .pdf of the tearsheet returns as well as display the output to the consol with quantitative statistics

## How to use this project in Google Colab
1. Connect to a Runtime
2. Press `` Ctrl + F9 ``

## Example Quick Start Main
``` Python
  from google.colab import drive
  drive.mount('/content/drive')
  
  portfolio_df = read_csv('Tearsheet Generator', 'TransactionHistory2020-2022')

  symbols = portfolio_df.Symbol.unique()
  stocks_start = '2020-04-30' # REPLACE WITH 1 DAY AFTER YOUR PORTFOLIO START DATE
  today = datetime.datetime.today()
  stocks_end = today.strftime("%Y-%m-%d")

  daily_adj_close = get_data(symbols, stocks_start, stocks_end)
  daily_adj_close = daily_adj_close[['Close']].reset_index() 

  market_cal = create_market_cal(stocks_start, stocks_end)

  active_portfolio = portfolio_start_balance(portfolio_df, stocks_start)
  
  positions_per_day = time_fill(active_portfolio, market_cal)
  
  pdpc = per_day_portfolio_calcs(positions_per_day, daily_adj_close, stocks_start)
  
  portReturns = format_returns(pdpc, 'Ticker Share Value')

  generate_report(portReturns, 'Tearsheet Generator')
```

## Example Input .csv
| Symbol        | Qty           | Type          | Open date      | Adj cost per share | Adj cost      |
| ------------- | ------------- | ------------- | ---------------| ------------------ | ------------- | 
| AAPL          | 10            | Buy           | 4/29/2020      | 71                 | 710           |
| MSFT          | 10            | Buy           | 5/1/2020       | 175                | 1750          |
| AAPL          | 5             | Sell.FIFO     | 5/15/2020      | 77                 | 385           |
|...|...|...|...|...|...|

## Example Output

### HTML
![quantstats-tearsheet](https://user-images.githubusercontent.com/84938803/210924445-0e251786-f38b-46d3-a78d-2d2b9e6539b5.jpg)

### PDF
[Tearsheet_2023-01-06.pdf](https://github.com/ldt9/Portfolio-Tearsheet-Return-Generator/files/10357085/Tearsheet_2023-01-06.pdf)

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

