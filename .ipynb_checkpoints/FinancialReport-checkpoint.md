# Financial Report

## Budget Analysis


import os
import plaid
import requests
import datetime
import json
import pandas as pd
%matplotlib inline
#!pip install plaid-python
import os
import plaid
import requests
import datetime
import json
import pandas as pd
%matplotlib inline
def pretty_print_response(response):
  print(json.dumps(response, indent=4, sort_keys=True))
PLAID_CLIENT_ID = os.getenv('PLAID_CLIENT_ID')
PLAID_SECRET = os.getenv('PLAID_SECRET')
PLAID_PUBLIC_KEY = os.getenv('PLAID_PUBLIC_KEY')
PLAID_ENV = os.getenv('PLAID_ENV', 'sandbox')
PLAID_PRODUCTS = os.getenv('PLAID_PRODUCTS', 'transactions')
Plaid Access Token
In this section, you will use the plaid-python api to generate the correct authentication tokens to access data in the free developer Sandbox. This mimics how you might connect to your own account or a customer account, but due to privacy issues, this homework will only require connecting to and analyzing the fake data from the developer sandbox that Plaid provides.

Complete the following steps to generate an access token:

Create a client to connect to plaid
Use the client to generate a public token and request the following items: ['transactions', 'income', 'assets']
Exchange the public token for an access token
Test the access token by requesting and printing the available test accounts
1. Create a client to connect to plaid
INSTITUTION_ID = "ins_109508"
# YOUR CODE HERE
client = plaid.Client(client_id=PLAID_CLIENT_ID,
                      secret=PLAID_SECRET,
                      public_key=PLAID_PUBLIC_KEY,
                      environment='sandbox'
                     ) 
2. Generate a public token
# YOUR CODE HERE
create_tkn_response = client.Sandbox.public_token.create(INSTITUTION_ID, ['transactions','income','assets'])
3. Exchange the public token for an access token
# YOUR CODE HERE
exchange_response = client.Item.public_token.exchange(create_tkn_response['public_token'])
access_token = exchange_response['access_token']
4. Fetch Accounts
# YOUR CODE HERE
client.Accounts.get(access_token)
{'accounts': [{'account_id': 'VPDzdylBxAhv18m4kmlgtqlXmmQx6RFW1NAXr',
   'balances': {'available': 100,
    'current': 110,
    'iso_currency_code': 'USD',
    'limit': None,
    'unofficial_currency_code': None},
   'mask': '0000',
   'name': 'Plaid Checking',
   'official_name': 'Plaid Gold Standard 0% Interest Checking',
   'subtype': 'checking',
   'type': 'depository'},
  {'account_id': 'wam6dXgvNVhB9AXdkXeZuagK554e1LTrwKdxa',
   'balances': {'available': 200,
    'current': 210,
    'iso_currency_code': 'USD',
    'limit': None,
    'unofficial_currency_code': None},
   'mask': '1111',
   'name': 'Plaid Saving',
   'official_name': 'Plaid Silver Standard 0.1% Interest Saving',
   'subtype': 'savings',
   'type': 'depository'},
  {'account_id': '5emXdMLkrEuQzpZ6rZqmhjGqkkoLagFZljLA4',
   'balances': {'available': None,
    'current': 1000,
    'iso_currency_code': 'USD',
    'limit': None,
    'unofficial_currency_code': None},
   'mask': '2222',
   'name': 'Plaid CD',
   'official_name': 'Plaid Bronze Standard 0.2% Interest CD',
   'subtype': 'cd',
   'type': 'depository'},
  {'account_id': 'JlDynN1J8eHWLq4jA4GmIQpA44DWVaUd3wNvp',
   'balances': {'available': None,
    'current': 410,
    'iso_currency_code': 'USD',
    'limit': 2000,
    'unofficial_currency_code': None},
   'mask': '3333',
   'name': 'Plaid Credit Card',
   'official_name': 'Plaid Diamond 12.5% APR Interest Credit Card',
   'subtype': 'credit card',
   'type': 'credit'},
  {'account_id': 'k9WRnQJvG5sRBgZDoZyds3EQ55e8JMFWnARex',
   'balances': {'available': 43200,
    'current': 43200,
    'iso_currency_code': 'USD',
    'limit': None,
    'unofficial_currency_code': None},
   'mask': '4444',
   'name': 'Plaid Money Market',
   'official_name': 'Plaid Platinum Standard 1.85% Interest Money Market',
   'subtype': 'money market',
   'type': 'depository'},
  {'account_id': 'lvnRbQq6lVHy6BpwVpXgHNMl33vbx7uZQ6AeZ',
   'balances': {'available': None,
    'current': 320.76,
    'iso_currency_code': 'USD',
    'limit': None,
    'unofficial_currency_code': None},
   'mask': '5555',
   'name': 'Plaid IRA',
   'official_name': None,
   'subtype': 'ira',
   'type': 'investment'},
  {'account_id': 'qry6K1NvBVsyBZRD7R1AH56eKKmAJLTdGBX1g',
   'balances': {'available': None,
    'current': 23631.9805,
    'iso_currency_code': 'USD',
    'limit': None,
    'unofficial_currency_code': None},
   'mask': '6666',
   'name': 'Plaid 401k',
   'official_name': None,
   'subtype': '401k',
   'type': 'investment'},
  {'account_id': 'KqDLbNpJEQTmXEZboZ46HEkyZZ1mPXIVqr3aE',
   'balances': {'available': None,
    'current': 65262,
    'iso_currency_code': 'USD',
    'limit': None,
    'unofficial_currency_code': None},
   'mask': '7777',
   'name': 'Plaid Student Loan',
   'official_name': None,
   'subtype': 'student',
   'type': 'loan'}],
 'item': {'available_products': ['auth',
   'balance',
   'credit_details',
   'identity',
   'investments',
   'liabilities'],
  'billed_products': ['assets', 'income', 'transactions'],
  'consent_expiration_time': None,
  'error': None,
  'institution_id': 'ins_109508',
  'item_id': '6QEkabopzWuGmDqwzqKlSKgbklVl6LHg978Vl',
  'webhook': ''},
 'request_id': '9PWLVPTLuMAfKHe'}
Account Transactions with Plaid
In this section, you will use the Plaid Python SDK to connect to the Developer Sandbox account and grab a list of transactions. You will need to complete the following steps:

Use the access token to fetch the transactions for the last 90 days
Print the categories for each transaction type
Create a new DataFrame using the following fields from the JSON transaction data: date, name, amount, category. (For categories with more than one label, just use the first category label in the list)
Convert the data types to the appropriate types (i.e. datetimeindex for the date and float for the amount)
1. Fetch the Transactions for the last 90 days
# YOUR CODE HERE
start_date = '{:%Y-%m-%d}'.format(datetime.datetime.now() + datetime.timedelta(-90))
end_date = '{:%Y-%m-%d}'.format(datetime.datetime.now())
2. Print the categories for each transaction
# YOUR CODE HERE
transaction_response = client.Transactions.get(access_token,start_date,end_date)
for transactions in transaction_response['transactions']:
    print(json.dumps(transactions['category'], indent=4, sort_keys=True))
[
    "Payment",
    "Credit Card"
]
[
    "Travel",
    "Car Service",
    "Ride Share"
]
[
    "Transfer",
    "Debit"
]
[
    "Transfer",
    "Deposit"
]
[
    "Recreation",
    "Gyms and Fitness Centers"
]
[
    "Travel",
    "Airlines and Aviation Services"
]
[
    "Food and Drink",
    "Restaurants",
    "Fast Food"
]
[
    "Food and Drink",
    "Restaurants",
    "Coffee Shop"
]
[
    "Food and Drink",
    "Restaurants"
]
[
    "Transfer",
    "Credit"
]
[
    "Travel",
    "Airlines and Aviation Services"
]
[
    "Travel",
    "Car Service",
    "Ride Share"
]
[
    "Food and Drink",
    "Restaurants"
]
[
    "Payment"
]
[
    "Food and Drink",
    "Restaurants",
    "Fast Food"
]
[
    "Shops",
    "Sporting Goods"
]
[
    "Payment",
    "Credit Card"
]
[
    "Travel",
    "Car Service",
    "Ride Share"
]
[
    "Transfer",
    "Debit"
]
[
    "Transfer",
    "Deposit"
]
[
    "Recreation",
    "Gyms and Fitness Centers"
]
[
    "Travel",
    "Airlines and Aviation Services"
]
[
    "Food and Drink",
    "Restaurants",
    "Fast Food"
]
[
    "Food and Drink",
    "Restaurants",
    "Coffee Shop"
]
[
    "Food and Drink",
    "Restaurants"
]
[
    "Transfer",
    "Credit"
]
[
    "Travel",
    "Airlines and Aviation Services"
]
[
    "Travel",
    "Car Service",
    "Ride Share"
]
[
    "Food and Drink",
    "Restaurants"
]
[
    "Payment"
]
[
    "Food and Drink",
    "Restaurants",
    "Fast Food"
]
[
    "Shops",
    "Sporting Goods"
]
[
    "Payment",
    "Credit Card"
]
[
    "Travel",
    "Car Service",
    "Ride Share"
]
[
    "Transfer",
    "Debit"
]
[
    "Transfer",
    "Deposit"
]
[
    "Recreation",
    "Gyms and Fitness Centers"
]
[
    "Travel",
    "Airlines and Aviation Services"
]
[
    "Food and Drink",
    "Restaurants",
    "Fast Food"
]
[
    "Food and Drink",
    "Restaurants",
    "Coffee Shop"
]
[
    "Food and Drink",
    "Restaurants"
]
[
    "Transfer",
    "Credit"
]
[
    "Travel",
    "Airlines and Aviation Services"
]
[
    "Travel",
    "Car Service",
    "Ride Share"
]
[
    "Food and Drink",
    "Restaurants"
]
[
    "Payment"
]
[
    "Food and Drink",
    "Restaurants",
    "Fast Food"
]
[
    "Shops",
    "Sporting Goods"
]
3. Create a new DataFrame using the following fields from the JSON transaction data: date, name, amount, category.
(For categories with more than one label, just use the first category label in the list)

# YOUR CODE HERE
transactions = transaction_response['transactions']
transactions_by_categories = pd.DataFrame(transactions, columns=['date', 'name', 'amount'])
# YOUR CODE HERE
category = []
for transaction in transactions:
    category.append(transaction['category'][0])
​
transactions_by_categories['category'] = category
transactions_by_categories
date	name	amount	category
0	2020-01-16	CREDIT CARD 3333 PAYMENT *//	25.00	Payment
1	2020-01-16	Uber 063015 SF**POOL**	5.40	Travel
2	2020-01-15	ACH Electronic CreditGUSTO PAY 123456	5850.00	Transfer
3	2020-01-15	CD DEPOSIT .INITIAL.	1000.00	Transfer
4	2020-01-14	Touchstone Climbing	78.50	Recreation
5	2020-01-14	United Airlines	-500.00	Travel
6	2020-01-13	McDonald's	12.00	Food and Drink
7	2020-01-13	Starbucks	4.33	Food and Drink
8	2020-01-12	SparkFun	89.40	Food and Drink
9	2020-01-11	INTRST PYMNT	-4.22	Transfer
10	2020-01-01	United Airlines	500.00	Travel
11	2019-12-30	Uber 072515 SF**POOL**	6.33	Travel
12	2019-12-27	Tectra Inc	500.00	Food and Drink
13	2019-12-26	AUTOMATIC PAYMENT - THANK	2078.50	Payment
14	2019-12-26	KFC	500.00	Food and Drink
15	2019-12-26	Madison Bicycle Shop	500.00	Shops
16	2019-12-17	CREDIT CARD 3333 PAYMENT *//	25.00	Payment
17	2019-12-17	Uber 063015 SF**POOL**	5.40	Travel
18	2019-12-16	ACH Electronic CreditGUSTO PAY 123456	5850.00	Transfer
19	2019-12-16	CD DEPOSIT .INITIAL.	1000.00	Transfer
20	2019-12-15	Touchstone Climbing	78.50	Recreation
21	2019-12-15	United Airlines	-500.00	Travel
22	2019-12-14	McDonald's	12.00	Food and Drink
23	2019-12-14	Starbucks	4.33	Food and Drink
24	2019-12-13	SparkFun	89.40	Food and Drink
25	2019-12-12	INTRST PYMNT	-4.22	Transfer
26	2019-12-02	United Airlines	500.00	Travel
27	2019-11-30	Uber 072515 SF**POOL**	6.33	Travel
28	2019-11-27	Tectra Inc	500.00	Food and Drink
29	2019-11-26	AUTOMATIC PAYMENT - THANK	2078.50	Payment
30	2019-11-26	KFC	500.00	Food and Drink
31	2019-11-26	Madison Bicycle Shop	500.00	Shops
32	2019-11-17	CREDIT CARD 3333 PAYMENT *//	25.00	Payment
33	2019-11-17	Uber 063015 SF**POOL**	5.40	Travel
34	2019-11-16	ACH Electronic CreditGUSTO PAY 123456	5850.00	Transfer
35	2019-11-16	CD DEPOSIT .INITIAL.	1000.00	Transfer
36	2019-11-15	Touchstone Climbing	78.50	Recreation
37	2019-11-15	United Airlines	-500.00	Travel
38	2019-11-14	McDonald's	12.00	Food and Drink
39	2019-11-14	Starbucks	4.33	Food and Drink
40	2019-11-13	SparkFun	89.40	Food and Drink
41	2019-11-12	INTRST PYMNT	-4.22	Transfer
42	2019-11-02	United Airlines	500.00	Travel
43	2019-10-31	Uber 072515 SF**POOL**	6.33	Travel
44	2019-10-28	Tectra Inc	500.00	Food and Drink
45	2019-10-27	AUTOMATIC PAYMENT - THANK	2078.50	Payment
46	2019-10-27	KFC	500.00	Food and Drink
47	2019-10-27	Madison Bicycle Shop	500.00	Shops
4. Convert the data types to the appropriate types
(i.e. datetimeindex for the date and float for the amount)

# YOUR CODE HERE
transactions_by_categories['date'] = pd.to_datetime(transactions_by_categories['date'])
transactions_by_categories['amount'] = transactions_by_categories['amount'].astype(float, inplace=True)
transactions_by_categories.set_index('date', inplace=True)
# Income Analysis with Plaid

# Income Analysis with Plaid
​
In this section, you will use the Plaid Sandbox to complete the following:
1. Determine the previous year's gross income and print the results
2. Determine the current monthly income and print the results
3. Determine the projected yearly income and print the results
# YOUR CODE HERE
income_response = client.Income.get(access_token)
income_response
{'income': {'income_streams': [{'confidence': 0.99,
    'days': 720,
    'monthly_income': 500,
    'name': 'UNITED AIRLINES'}],
  'last_year_income': 6000,
  'last_year_income_before_tax': 7285,
  'max_number_of_overlapping_income_streams': 1,
  'number_of_income_streams': 1,
  'projected_yearly_income': 6085,
  'projected_yearly_income_before_tax': 7389},
 'request_id': 'lVUhhDCTPfxLfgR'}
gross_income = income_response['income']['last_year_income_before_tax']
current_monthly_income = income_response['income']['income_streams'][0]['monthly_income']
project_yearly_income = income_response['income']['projected_yearly_income_before_tax']
​
print(f"The previous year's gross income was {gross_income}.")
print(f"The current monthly income is {current_monthly_income}.")
print(f"The projected yearly income is {project_yearly_income}.")
The previous year's gross income was 7285.
The current monthly income is 500.
The projected yearly income is 7389.
Budget Analysis
In this section, you will use the transactions DataFrame to analyze the customer's budget

Calculate the total spending per category and print the results (Hint: groupby or count transactions per category)
Generate a bar chart with the number of transactions for each category
Calculate the expenses per month
Plot the total expenses per month
Calculate the expenses per category
# YOUR CODE HERE
transaction_spending = transactions_by_categories.groupby('category').sum()
transaction_spending.plot(kind='pie', subplots=True)
array([<matplotlib.axes._subplots.AxesSubplot object at 0x11ca1c4d0>],
      dtype=object)

trans_per_category = transactions_by_categories.groupby('category').count()
trans_per_category.drop(columns='name', inplace=True)
trans_per_category.plot(kind = 'bar')
<matplotlib.axes._subplots.AxesSubplot at 0x11d887cd0>

Calculate the expenses per month
# YOUR CODE HERE
monthly_spending = transactions_by_categories['amount'].resample('M').sum()
monthly_spending.plot(kind='bar')
<matplotlib.axes._subplots.AxesSubplot at 0x11dc768d0>


## Retirement Planning


import numpy as np
import pandas as pd
from datetime import datetime, timedelta
import matplotlib.pyplot as plt
from pathlib import Path
%matplotlib inline
np.random.seed(42)
Portfolio Planner
In this activity, you will use the iexfinance api to grab historical data for a 60/40 portfolio using SPY to represent the stock portion and AGG to represent the bonds.

#from iexfinance.stocks import get_historical_data
#import iexfinance as iex
csv_path = Path("../Resources/IEXCLOUD_data.csv")
portfolio_df = pd.read_csv(csv_path, index_col='date')
portfolio_df
SPY	AGG
date		
2019-01-22	262.86	106.44
2019-01-23	263.41	106.55
2019-01-24	263.55	106.73
2019-01-25	265.78	106.68
2019-01-28	263.76	106.62
...	...	...
2020-01-13	327.95	112.91
2020-01-14	327.45	113.01
2020-01-15	328.19	113.18
2020-01-16	330.92	113.18
2020-01-17	331.95	113.05
251 rows × 2 columns

Data Collection
In this step, you will need to use the IEX api to fetch closing prices for the SPY and AGG tickers. Save the results as a pandas DataFrame

list_of_tickers = ["SPY", "AGG"]
# YOUR CODE HERE
#end_date = datetime.now()
#start_date = end_date + timedelta(-365)
#portfolio_df = get_historical_data(list_of_tickers, start_date, end_date,  output_format='pandas')
#portfolio_df.drop(columns=['open', 'high', 'low', 'volume'], level=1, inplace=True)
#portfolio_df.head()
Monte Carlo Simulation
In this step, you will run Monte Carlo Simulations for your portfolio to model portfolio performance at different retirement ages.

Complete the following steps:

Calculate the daily returns for the SPY and AGG closing prices.
Calculate volatility for both the SPY and AGG closing prices.
Find the last day's closing price for both stocks and save those as variables.
Run a Monte Carlo Simulation of at least 500 iterations and generate at least 30 years of closing prices
HINTS:
There are 252 trading days per year, so the number of records to generate for each Monte Carlo run will be 252 days * 30 years

# Calculate the daily roi for the stocks
daily_returns = portfolio_df.pct_change()
daily_returns.head()
SPY	AGG
date		
2019-01-22	NaN	NaN
2019-01-23	0.002092	0.001033
2019-01-24	0.000531	0.001689
2019-01-25	0.008461	-0.000468
2019-01-28	-0.007600	-0.000562
# Calculate volatility
# YOUR CODE HERE
avg_daily_return_spy = daily_returns['SPY'].mean()
avg_daily_return_agg = daily_returns['AGG'].mean()
std_dev_daily_return_spy = daily_returns['SPY'].std()
std_dev_daily_return_agg = daily_returns['AGG'].std()
# Save the last day's closing price
# YOUR CODE HERE
spy_last_price = portfolio_df['SPY'][-1]
agg_last_price = portfolio_df['AGG'][-1]
# Setup the Monte Carlo Parameters
number_simulations = 500
number_records = 252 * 30
monte_carlo = pd.DataFrame()
simulated_price_df = pd.DataFrame()
portfolio_cumulative_returns = pd.DataFrame()
# Run the Monte Carlo Simulation
​
# Run the simulation of projecting stock prices for the next trading year, `500` times
for x in range(number_simulations):
​
    # Initialize the simulated prices list with the last closing price 
    simulated_spy_prices = [spy_last_price]
    simulated_agg_prices = [agg_last_price]
    
    # Simulate the returns for 252 days
    for i in range(number_records):
        
        # Calculate the simulated price using the last price within the list
        simulated_spy_price = simulated_spy_prices[-1] * (1 + np.random.normal(avg_daily_return_spy, std_dev_daily_return_spy))
        simulated_agg_price = simulated_agg_prices[-1] * (1 + np.random.normal(avg_daily_return_agg, std_dev_daily_return_agg))
        
        # Append the simulated price to the list
        simulated_spy_prices.append(simulated_spy_price)
        simulated_agg_prices.append(simulated_agg_price)
    
    # Append a simulated prices of each simulation to DataFrame
    simulated_price_df["SPY prices"] = pd.Series(simulated_spy_prices)
    simulated_price_df["AGG prices"] = pd.Series(simulated_agg_prices)
    
    # Calculate the daily returns of simulated prices
    simulated_daily_returns = simulated_price_df.pct_change()
    
    # Set the portfolio weights (60% JNJ; 40% MU)
    weights = [0.60, 0.40]
​
    # Use the `dot` function with the weights to multiply weights with each column's simulated daily returns
    portfolio_daily_returns = simulated_daily_returns.dot(weights)
    
    # Calculate the normalized, cumulative return series
    portfolio_cumulative_returns[x] = (1 + portfolio_daily_returns.fillna(0)).cumprod()
​
# Print records from the DataFrame
portfolio_cumulative_returns.head()
0	1	2	3	4	5	6	7	8	9	...	490	491	492	493	494	495	496	497	498	499
0	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	...	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000	1.000000
1	1.002765	0.996222	0.993929	0.999896	1.005468	1.006505	1.008854	0.996870	1.001856	0.999339	...	1.002160	0.996121	1.006918	1.000176	0.997978	1.003057	0.996650	0.994530	1.003012	0.997636
2	1.007598	1.000038	0.993478	0.999056	1.008655	0.998236	1.003838	0.987116	1.001268	1.007326	...	1.004317	1.003128	1.009988	1.001913	0.999475	1.000177	0.996370	0.995111	1.003086	1.001348
3	1.007033	1.000251	0.996933	0.993854	1.008206	1.001209	1.007665	0.981654	1.001242	1.015214	...	1.004537	1.005779	1.014524	1.003520	0.993478	0.999223	1.000070	1.000454	1.006429	1.000479
4	1.015419	1.003153	0.993831	0.999488	1.009844	0.996768	1.006748	0.979196	1.008793	1.006011	...	1.006454	1.004336	1.013382	1.008449	0.999416	0.996601	0.994983	1.001323	1.010826	1.003931
5 rows × 500 columns

# Visualize the Simulation
plot_title = f"{x+1} Simulations of Cumulative Portfolio Return Trajectories Over the Next 252 Trading Days over 30 years"
portfolio_cumulative_returns.plot(legend=None, title=plot_title)
<matplotlib.axes._subplots.AxesSubplot at 0x1158afbd0>

# Select the last row for the cumulative returns (cumulative returns at 30 years)
# YOUR CODE HERE
ending_cumulative_returns = portfolio_cumulative_returns.iloc[-1, :]
ending_cumulative_returns
0      172.632754
1      143.860225
2      193.515796
3      202.454611
4      185.436230
          ...    
495    254.921807
496     78.888951
497    166.441114
498    294.108973
499     81.142151
Name: 7560, Length: 500, dtype: float64
# Select the last row for the cumulative returns (cumulative returns at 20 years)
# YOUR CODE HERE
portfolio_cumulative_returns.tail(1).T
7560
0	172.632754
1	143.860225
2	193.515796
3	202.454611
4	185.436230
...	...
495	254.921807
496	78.888951
497	166.441114
498	294.108973
499	81.142151
500 rows × 1 columns

# Display the 90% confidence interval for the ending returns
# YOUR CODE HERE
ending_cumulative_returns.value_counts(bins=10) / len(ending_cumulative_returns)
confidence_interval = ending_cumulative_returns.quantile(q=[0.05, 0.95])
confidence_interval
0.05     75.651312
0.95    280.071048
Name: 7560, dtype: float64
# Visualize the distribution of the ending returns
# YOUR CODE HERE
plt.figure();
ending_cumulative_returns.plot(kind='hist', density=True, bins=10)
plt.axvline(confidence_interval.iloc[0], color='r')
plt.axvline(confidence_interval.iloc[1], color='r')
<matplotlib.lines.Line2D at 0x12bf01d90>

Retirement Analysis
In this section, you will use the monte carlo model to answer the following retirement planning questions:

What are the expected cumulative returns at 30 years for the 10th, 50th, and 90th percentiles?
Given an initial investment of $20,000, what is the expected portfolio return in dollars at the 10th, 50th, and 90th percentiles?
Given the current projected annual income from the Plaid analysis, will a 4% withdraw rate from the retirement portfolio meet or exceed that value at the 10th percentile?
How would a 50% increase in the initial investment amount affect the 4% retirement withdrawal?
What are the expected cumulative returns at 30 years for the 10th, 50th, and 90th percentiles?
# YOUR CODE HERE
all_confidence_intervals = ending_cumulative_returns.quantile(q=[0.1,0.5,0.9])
all_confidence_intervals
0.1     92.882554
0.5    147.042900
0.9    237.622657
Name: 7560, dtype: float64
Given an initial investment of $20,000, what is the expected portfolio return in dollars at the 10th, 50th, and 90th percentiles?
# YOUR CODE HERE
initial_investment = 20000
​
investment_pnl_lower_bound = initial_investment + (initial_investment * all_confidence_intervals.iloc[0])
investment_pnl_mid_bound = initial_investment + (initial_investment * all_confidence_intervals.iloc[1])
investment_pnl_upper_bound = initial_investment + (initial_investment * all_confidence_intervals.iloc[2])
​
print(f'The expected portfolio returns for the 10th percentile at and initial investment of $20,000.00 would be ${investment_pnl_lower_bound}.')
print(f'The expected portfolio returns for the 50th percentile at and initial investment of $20,000.00 would be ${investment_pnl_mid_bound}.')
print(f'The expected portfolio returns for the 10th percentile at and initial investment of $20,000.00 would be ${investment_pnl_upper_bound}.')
The expected portfolio returns for the 10th percentile at and initial investment of $20,000.00 would be $1877651.0827897505.
The expected portfolio returns for the 50th percentile at and initial investment of $20,000.00 would be $2960857.993288653.
The expected portfolio returns for the 10th percentile at and initial investment of $20,000.00 would be $4772453.136497456.
Given the current projected annual income from the Plaid analysis, will a 4% withdraw rate from the retirement portfolio meet or exceed that value at the 10th percentile?
Note: This is effectively saying that 90% of the expected returns will be greater than the return at the 10th percentile, so this can help measure the uncertainty about having enough funds at retirement

# YOUR CODE HERE
plaid_projected_annual_income =  7389
​
retirement_income = .04 * (initial_investment * ending_cumulative_returns.quantile(q=[.1])).iloc[0]
if retirement_income >= plaid_projected_annual_income:
    print(f"You will have ${retirement_income:.2f} in retirement income which is sufficient")
else:
    print(f"You will have ${retirement_income:.2f} in retirement income which is not sufficient")
You will have $74306.04 in retirement income which is sufficient
How would a 50% increase in the initial investment amount affect the 4% retirement withdrawal?
# YOUR CODE HERE
.04 * (1.5 * initial_investment * ending_cumulative_returns.quantile(q=[.1])).iloc[0]
111459.06496738503
Optional Challenge
In this section, you will calculate and plot the cumulative returns for the median and 90% confidence intervals. This plot shows the expected cumulative returns for any given day between the first day and the last day of investment.

# YOUR CODE HERE
projected_returns = portfolio_cumulative_returns.quantile(q=[0.05, .5, .95], axis=1, numeric_only=True).T
projected_returns.head()
0.05	0.50	0.95
0	1.000000	1.000000	1.000000
1	0.993296	1.000748	1.008319
2	0.991599	1.001655	1.011768
3	0.989933	1.002294	1.015242
4	0.988084	1.003219	1.016812
projected_returns.plot(title="Cumulative Returns over Time")
<matplotlib.axes._subplots.AxesSubplot at 0x12c0551d0>

(initial_investment * projected_returns).plot(title="Portfolio Performance over Time")
<matplotlib.axes._subplots.AxesSubplot at 0x11ad80cd0>
