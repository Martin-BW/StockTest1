import streamlit as st
import yfinance as yf
import pandas as pd
from ta.momentum import RSIIndicator
from ta.trend import SMAIndicator

st.title("AI-Powered Growth Stock Screener")
st.write("Identify high-growth stocks using technical and fundamental indicators.")

# Input: list of tickers
tickers = st.text_input("Enter comma-separated tickers (e.g., AAPL, MSFT, NVDA)", "AAPL, MSFT, NVDA")
ticker_list = [ticker.strip().upper() for ticker in tickers.split(",")]

# Define screening function
def screen_stock(ticker):
    try:
        stock = yf.Ticker(ticker)
        hist = stock.history(period="6mo")

        if len(hist) < 200:
            return ticker, "Not enough data"

        hist["RSI"] = RSIIndicator(close=hist["Close"]).rsi()
        hist["SMA50"] = SMAIndicator(close=hist["Close"], window=50).sma_indicator()
        hist["SMA200"] = SMAIndicator(close=hist["Close"], window=200).sma_indicator()

        latest = hist.iloc[-1]
        fundamentals = stock.info

        # Fundamentals check (limited from yfinance)
        pe_ratio = fundamentals.get("trailingPE", None)
        eps_growth = fundamentals.get("earningsQuarterlyGrowth", None)

        is_technical_match = (
            latest["Close"] > latest["SMA50"]
            and latest["Close"] > latest["SMA200"]
            and 40 < latest["RSI"] < 70
        )

        is_fundamental_match = (
            pe_ratio is not None and pe_ratio < 25
            and eps_growth is not None and eps_growth > 0.2
        )

        if is_technical_match and is_fundamental_match:
            return ticker, "✅ High-growth candidate"
        else:
            return ticker, "❌ Does not meet criteria"
    except Exception as e:
        return ticker, f"Error: {str(e)}"

if st.button("Run Screener"):
    results = [screen_stock(ticker) for ticker in ticker_list]
    for ticker, result in results:
        st.write(f"{ticker}: {result}")
