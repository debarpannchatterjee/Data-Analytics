# Stock Market Analysis using Alpha Vantage
This program uses the Alpha Vnatge API to pull stock market data and contains the following python classes and functions to perform analytical tasks:
1. Class ScriptData which can fetch US Stock data using Alpha Vantage.
The class implements the following methods:

a. fetch_intraday_data: (method arguments: script)
Fetches intraday data for given “script” (Example for script: “GOOGL”,
“AAPL”) and stores as it is.

b. convert_intraday_data: (method arguments: script)
Converts fetched intraday data (in point a.) as a pandas DataFrame
(hereafter referred as “df”) with the following columns:
i. timestamp (data type: pandas.Timestamp)
ii. open (data type: float)
iii. high (data type: float)
iv. low(data type: float)
v. close (data type: float)
vi. volume (data type: int)

c. Additional methods for overloading the following operations:
i. getitem
ii. setitem
iii. contains

2. A function called indicator1. It takes “df” and ‘timeperiod’ (integer) as
inputs and give another pandas DataFrame as an output with two columns:

a. timestamp: Same as ‘timestamp’ column in ‘df’

b. indicator: Moving Average of the ‘close’ column in ‘df’. The number of
elements to be taken for a moving average is defined by ‘timeperiod’. For
example, if ‘timeperiod’ is 5, then each row in this column will be an average
of total 5 previous values (including current value) of the ‘close’ column.

3. A class Strategy, which can do the following, given a script name:

a. Fetch intraday historical day (‘df’) using ScriptData class.
We’ll refer to the ‘close’ column of ‘df’ as close_data.

b. Compute indicator data on ‘close’ of ‘df’ using indicator1 function.
We’ll refer to the ‘indicator’ column of this data as indicator_data.

c. Generate a pandas DataFrame called ‘signals’ with 2 columns:
i. ‘timestamp’: Same as ‘timestamp’ column in ‘df’
ii. ‘signal’: This column can have the following values:
1. BUY (When: If indicator_data cuts close_data upwards)
2. SELL (When: If indicator_data cuts close_data downwards)
3. NO_SIGNAL (When: If indicator_data and close_data don’t cut
each other)

Here are a few pointers regrading the code:
1. The classes are implemented in a way such that any user can create objects using their personal keys i.e. the API key is not hardcoded.
   
3. In the fetch_intraday_data function of ScriptData Class, a try-except block is used to catch two types of potential exceptions that may occur during the API request: requests.exceptions.HTTPError and requests.exceptions.RequestException.
The response.raise_for_status() method is used to raise an exception if the request returns an HTTP error status code (e.g. 400, 404, 500). If an HTTP error occurs, the method catches the exception and prints an error message indicating which script failed to fetch and the specific error that occurred.
If an exception occurs that is not an HTTP error, such as a network error or a timeout, the method catches the requests.exceptions.RequestException and prints an error message indicating which script failed to fetch and the specific error that occurred.

4. There are checks for None values throughout the code to handle errors in case data cannot be fetched.

5. The for loop in the convert_intraday_data method iterates over the dictionary object returned by the fetch_intraday_data method. This dictionary represents the intraday stock data in the form of timestamped OHLCV (Open, High, Low, Close, Volume) values.

For each time stamp in the dictionary, the for loop creates a new row in a Pandas DataFrame with columns for the timestamp, open, high, low, close, and volume. It then populates the columns of the new row with the corresponding data from the dictionary.

Here's a breakdown of how the loop works:

for timestamp, values in data.items(): iterates over the key-value pairs in the data dictionary, where data_point is the timestamp of the data point, and values is another dictionary object containing the OHLCV data.
row = {...} creates a new Python dictionary object to represent a row in the DataFrame. The keys in the dictionary correspond to the column names in the DataFrame, and the values are initialized to the appropriate data types.
 df = pd.concat([df, pd.DataFrame(row, index=[0])], ignore_index=True) creates a new dataframe with a single row and concatenates it with our existing dataframe. We use concatenate because the append method is being deprecated. The ignore_index=True parameter ensures that each row is added with a new index value.
The loop then proceeds to the next time stamp in the data dictionary and repeats the above steps until all data points have been added to the DataFrame.
At the end of the loop, the convert_intraday_data method returns the resulting DataFrame object, which can then be used for further analysis and manipulation.


6. The if and else conditions in the compute_signals method of the Strategy class are used to generate the 'signal' column of the 'signals' DataFrame.

- If the value of the 'indicator' column is greater than the value of the 'close' column for the current row and if the value of the 'indicator' column was less than the value of the 'close' column for the previous row, then the signal for the current row is 'BUY'.
- If the value of the 'indicator' column is less than the value of the 'close' column for the current row and if the value of the 'indicator' column was greater than the value of the 'close' column for the previous row, then the signal for the current row is 'SELL'.
- If neither of the above conditions are satisfied, then the signal for the current row is 'NO_SIGNAL'.

In the context of this problem, 'cut upwards' and 'cut downwards' refer to the relationship between the values in the 'close' column and the values in the 'indicator' column.

When the 'indicator' column crosses above the 'close' column, it's referred to as a 'cut upwards', as the 'indicator' line cuts upwards through the 'close' line. This indicates a potential buy signal.

On the other hand, when the 'indicator' column crosses below the 'close' column, it's referred to as a 'cut downwards', as the 'indicator' line cuts downwards through the 'close' line. This indicates a potential sell signal.

When the 'indicator' line and 'close' line don't intersect, it's referred to as 'not cutting each other'. In this case, there is no clear buy or sell signal, so the signal is labeled as 'NO_SIGNAL'.

In summary, the if and else conditions are used to check whether the indicator and close data cut each other upwards or downwards, and based on that, generate the corresponding 'BUY', 'SELL', or 'NO_SIGNAL' signal for the current row.
