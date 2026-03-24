<img width="1632" height="918" alt="Screenshot 2026-03-24 at 11 55 34 AM" src="https://github.com/user-attachments/assets/d750d5c4-460f-4093-b60f-b090cfbc958c" />




<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif; line-height: 1.6; color: #24292e; max-width: 850px; margin: 0 auto; padding: 30px; border: 1px solid #e1e4e8; border-radius: 6px; background-color: #ffffff;">

    <h1 style="border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; font-size: 2em; margin-top: 0;">Steps to Create a Correlation Matrix and Heatmap in Python</h1>

    <h2 style="border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; margin-top: 24px; font-size: 1.5em;">Overview</h2>
    <p>This is a technical demonstration of how to extract data, calculate a correlation matrix, and visualize it using Python. The steps cover data ingestion, transformation, and plotting a heatmap.</p>

    <h2 style="border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; margin-top: 24px; font-size: 1.5em;">Data Used for Demonstration</h2>
    <ul style="padding-left: 2em; margin-bottom: 16px;">
        <li style="margin-bottom: 4px;"><strong>Source:</strong> <code style="background-color: #f6f8fa; padding: 0.2em 0.4em; border-radius: 3px; font-family: monospace;">yfinance</code> API</li>
        <li style="margin-bottom: 4px;"><strong>Timeframe:</strong> 5-year historical daily closing prices.</li>
        <li style="margin-bottom: 4px;"><strong>Tickers:</strong> Banks listed in the Indian <strong>Bank Nifty</strong> index.</li>
    </ul>

    <h2 style="border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; margin-top: 24px; font-size: 1.5em;">Tech Stack</h2>
    <ul style="padding-left: 2em; margin-bottom: 16px;">
        <li style="margin-bottom: 4px;">Python (<code style="background-color: #f6f8fa; padding: 0.2em 0.4em; border-radius: 3px; font-family: monospace;">pandas</code>, <code style="background-color: #f6f8fa; padding: 0.2em 0.4em; border-radius: 3px; font-family: monospace;">yfinance</code>, <code style="background-color: #f6f8fa; padding: 0.2em 0.4em; border-radius: 3px; font-family: monospace;">seaborn</code>, <code style="background-color: #f6f8fa; padding: 0.2em 0.4em; border-radius: 3px; font-family: monospace;">matplotlib</code>)</li>
    </ul>

    <hr style="height: 1px; background-color: #e1e4e8; border: none; margin: 24px 0;">

    <h2 style="border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; margin-top: 24px; font-size: 1.5em;">Step-by-Step Implementation</h2>

    <h3 style="margin-top: 24px; font-size: 1.25em;">Step 1: Import Required Libraries</h3>
    <pre style="background-color: #f6f8fa; padding: 16px; border-radius: 6px; overflow: auto; border: 1px solid #eaecef; font-family: Consolas, 'Courier New', monospace; font-size: 14px; color: #24292e;"><code>import pandas as pd
import yfinance as yf
import matplotlib.pyplot as plt
import seaborn as sns</code></pre>

    <h3 style="margin-top: 24px; font-size: 1.25em;">Step 2: Fetch the Data</h3>
    <p>Loop through the list of Bank Nifty tickers to extract 5 years of historical data using the <code style="background-color: #f6f8fa; padding: 0.2em 0.4em; border-radius: 3px; font-family: monospace;">yfinance</code> API, then combine it into a single dataframe.</p>
    <pre style="background-color: #f6f8fa; padding: 16px; border-radius: 6px; overflow: auto; border: 1px solid #eaecef; font-family: Consolas, 'Courier New', monospace; font-size: 14px; color: #24292e;"><code># Define Bank Nifty constituents
nifty_banks = ["HDFCBANK.NS", "ICICIBANK.NS", "SBIN.NS", "AXISBANK.NS", "KOTAKBANK.NS"]

data_frames = []

for bank in nifty_banks:
    stock = yf.Ticker(bank)
    df = stock.history(period="5y").reset_index()
    
    # Keep relevant columns and tag with the symbol
    df['Symbol'] = bank
    df = df[['Date', 'Symbol', 'Close']].rename(columns={'Close': 'Stock_Close'})
    data_frames.append(df)

master_df = pd.concat(data_frames)</code></pre>

    <h3 style="margin-top: 24px; font-size: 1.25em;">Step 3: Calculate Percentage Returns</h3>
    <p>To find the correlation, calculate the day-over-day percentage change for each stock.</p>
    <pre style="background-color: #f6f8fa; padding: 16px; border-radius: 6px; overflow: auto; border: 1px solid #eaecef; font-family: Consolas, 'Courier New', monospace; font-size: 14px; color: #24292e;"><code># Calculate daily returns
master_df['Return'] = master_df.groupby('Symbol')['Stock_Close'].pct_change() * 100

# Drop NaN values 
master_df.dropna(inplace=True)</code></pre>

    <h3 style="margin-top: 24px; font-size: 1.25em;">Step 4: Pivot the Dataframe</h3>
    <p>Reshape the data so that the Dates are the index, the Stock Symbols are the columns, and the values are the Returns.</p>
    <pre style="background-color: #f6f8fa; padding: 16px; border-radius: 6px; overflow: auto; border: 1px solid #eaecef; font-family: Consolas, 'Courier New', monospace; font-size: 14px; color: #24292e;"><code>pivot_df = master_df.pivot(index='Date', columns='Symbol', values='Return')</code></pre>

    <h3 style="margin-top: 24px; font-size: 1.25em;">Step 5: Compute and Visualize the Matrix</h3>
    <p>Calculate the correlation mathematically using pandas, and plot it using seaborn.</p>
    <pre style="background-color: #f6f8fa; padding: 16px; border-radius: 6px; overflow: auto; border: 1px solid #eaecef; font-family: Consolas, 'Courier New', monospace; font-size: 14px; color: #24292e;"><code># Generate the correlation matrix
correlation_matrix = pivot_df.corr()

# Plot the heatmap
plt.figure(figsize=(12, 8))
sns.heatmap(
    correlation_matrix, 
    annot=True,               
    cmap='coolwarm',          
    fmt='.2f',                
    linewidths=0.5
)

plt.title('Correlation Matrix - Bank Nifty (5 Years)')
plt.show()</code></pre>

</div>

