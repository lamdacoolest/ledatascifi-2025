A few students have issues with yfinance downloading data.

Try this first:
1. You can try in terminal: `pip install --upgrade pandas yfinance` . My code works with yfinance version 0.2.40 and 0.2.52 (latest). After that pip install, restart jupyter lab (close and reopen the terminal, and reopen the browser), try again.
2. Some students get a "rate limit" error. Just pause and retry in a few minutes.

If that fails, 
1. Put `stock_prices_AFTERWRANGLING.csv` (in this folder) into your assignment repository folder. (Don't gitignore it!)
1. Then replace the 3 blocks of code I gave you in the assignment with this:

```python
backup_plan = 'stock_prices_AFTERWRANGLING.csv'

import os 

if os.path.exists(backup_plan):
    
    stock_prices = pd.read_csv(backup_plan)    
    
else:
    import pandas_datareader as pdr  # to install: !pip install pandas_datareader
    from datetime import datetime
    import yfinance as yf

    stocks = pd.read_csv('subsample_tickers.csv')['tic'].to_list()
    start = datetime(2020, 2, 1)
    end = datetime(2020, 4, 30)

    # load
    stock_prices =yf.download(stocks, start , end)


    # stock_prices.index = stock_prices.index.tz_localize(None)        # change yf date format to match pdr
    stock_prices = stock_prices.filter(like='Adj Close')               # reduce to just columns with this in the name
    stock_prices.columns = stock_prices.columns.get_level_values(1)    # tickers as col names, works no matter order of tics

    stock_prices = stock_prices.stack().swaplevel().sort_index().reset_index()
    stock_prices.columns = ['Firm','Date','Adj Close']

    stock_prices
    
    print('Downloaded returns for', stock_prices['Firm'].nunique(),'firms')
    print('---')

    stock_prices['ret'] = stock_prices.groupby('Firm')['Adj Close'].pct_change()
    stock_prices    
    
    # prof made one version for all students:
    #stock_prices.to_csv('stock_prices_AFTERWRANGLING.csv', index=False)
    
display(stock_prices.describe())
stock_prices    

```
