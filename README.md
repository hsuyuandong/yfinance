## Rare Events Analysis for High‐Frequency Equity Data
#### Dragos Bozdog, Ionut¸ Florescu, Khaldoun Khashanah, and Jim Wang
---
```上課作業內容簡述：  
本次作業為 **2022/11/9** 論文導讀課 yfinance作業  
→使用 **python** 語言中的 ` pandas ` 方式撰寫  
透過paper(2011_Rare Events Analysis for High‐Frequency Equity Data)中的兩個公式來撰寫內容  
→先抓取 ` AAPL ` 近一年的資料，其中有` Volume `、` Date `及` Close `欄位，且因為資料格式問題，有先進行新資料表匯出再進行後續code部分。  
```
→**變數簡介**   
| V0        | S_diff           | S_date            | max            |
| ------------- |:-------------:| :-----:| :-----:|
| 'Volume'的mean()        | 最後一個值減掉現在在算的值      | 最後一個時間和現在時間 |S_diff的最大值|

---
### Abstract
In this work we present a methodology to detect rare events which are
defined as large price movements relative to the volume traded. We analyze
the behavior of equity after the detection of these rare events. We provide
methods to calibrate trading rules based on the detection of these events and
illustrate for a particular trading rule. We apply the methodology to tick data
for thousands of equities over a period of five days. In order to draw comprehensive
conclusions, we group the equities into classes and calculate probabilities
of price recovery after these rare events for each class. The methodology
that we have developed is based on non-parametric statistics and makes
no assumption about the distribution of the random variables in the study.   

---
### Objectives
• Develop a method to detect rare events in real time where the movement
of price is large with relatively small volume of shares traded.   
開發一種實時檢測**罕見事件方法**，價格大而股票交易量相對較小。   

• Analyze the price behavior after these rare events and study the probability
of price recovery. What is the expected return if a trade is placed
at the detected observation?   
分析這些罕見事件後的**價格行為**並研究概率的價格恢復。如果進行交易，預期回報是多少在檢測到的觀察？

---
### Methodology
In this analysis we use tick-by-tick data of 5,369 equities traded on NYSE,
NASDAQ, and AMEX over five days. We need the most detailed possible dataset;
however, since our discovery is limited to past trades we do not require
the use of a more detailed level-2 order data. We perform model-free statistical
analysis on this multivariate dataset.
For any given equity in the dataset, an observation represents a trade.
Each trade records the price P of the transaction, the volume V of the shares
traded and the time t at which the transaction takes place. In this study we
are primarily interested in large price movement with small volume, thus
for any two observations in the dataset we construct a two-dimensional
random vector (ΔP, ΔV). Here ΔP is the change in price, ΔV is the change in
volume.
The reason for considering any pair of trades and not only consecutive
trades is that in general the price movement occurs over several consecutive
trades. The main objective of our study is the conditional distribution:  

### ` Prob(max ΔP|ΔV < V0) `
---
### Justification of the Method
**1. Why restrict the distribution to be conditional on V0?**   
According to our declared objective, we are interested in the price movement
corresponding to a small volume. Therefore, by conditioning the
distribution we are able to provide answers while keeping the number of
computations manageable.   

**2. Why should V0 be constant in time and dependent only on the equity?**   
Indeed, this is a very important question. There is no reason for V0 to
be constant other than for practical reasons. A valid objection is that the
dynamics of the equity change over time. A time-changing model is beyond
the scope of the current study, though in this work we investigate several
(fixed) levels of this parameter.   

**3. Why not the more traditional approach of price and volume evolution in time?**   
It is known from the literature that the duration defined as the time
between consecutive trades has an impact on the price evolution. Tsay and
Ting (2006) consider the distribution of price change conditional on duration
and volume (ΔP|d,V) using only consecutive trades. As illustration they
use tick data for four NYSE stocks. The authors conclude that the duration
affects prices, but the effect is stronger when the volume is high. They find
that at the intraday frequency the volume has an influence, even after controlling
for duration. Similar results are documented in Manganelli (2005),
who extends the ACD model of Engle. Generally, these types of model use
consecutive trades or sampled consecutive trades. Rare events, as defined in
our context, normally happen over several trades not consecutively. Because
of this, we need to consider all trades within a moving window. We analyze
over 5,000 equities. The large number of combinations makes the study of
duration and volume difficult. Furthermore, according to the cited results,
in our case (conditional volume is small) the influence of duration on price
is weak. All these considerations led us to the study of the distribution ΔP|V.

---
### Sampling Method: Rare Event Detection
### ` (Sn – Sj|vk + vk+1 + . . . + vn < V0) for k ≤ j ≤ n `
### ` Δpn = max{Sn – Sk, Sn – Sk+1, . . ., Sn – Sn–1} `
---
### Code
##### 抓取yfinance上 AAPL 近一年的資料
```python
!pip install yfinance  
import yfinance as yf
msft = yf.Ticker("AAPL")  

# get stock info
msft.info  

# get historical market data
hist = msft.history(period="1y")
hist[['Volume'][0]]

V0 = hist['Volume'].mean()
data = hist[["Close", "Volume"]]
```
##### 匯出資料表
```python
import pandas as pd

#因為是三維(xyz)資料，所以先取出第一張表
df = pd.DataFrame(hist[['Volume'][0]]) 
df.to_csv('test.csv') 
```
##### 用 enumerate 撰寫
```python
sum = 0 # V0到V251的加總
V0 = hist['Volume'].mean() # 特定值範圍 = Volume資料的中位數
S_diff = [] # 最後一個值減掉現在在算的值
S_date = [] # 最後一個時間和現在時間

for n,value in enumerate(df['Volume']): # 用enumerate函數取得key值(編號)和value值(Volume)
  
  sum+=int(value) # V0到V251的加總
  if sum < V0: # 如果目前加總值 < 中位數
    S_diff.append(int(df['Volume'][len(df)-1])-int(value)) # 最後一個值減掉現在在算的值
    S_date.append([df['Date'][len(df)-1], df['Date'][n]]) # 最後一個時間和現在時間

p = max(S_diff) # S_diff的最大值 
print(p)
D = S_date 
print(D)

# 因為第一個Volume+第二個Volume就已經大於中位數，所以只有跑出兩筆資料
# 64170900-88807000 = -24636100
# 因為原本兩個迴圈都用range，所以用一個enumerate寫
```
##### 用 兩個for迴圈 撰寫
```python
sum = 0
V0 = hist['Volume'].mean()
S_diff = []
S_date = []
volume1 = []

for n in range(0, len(df['Volume'])):

  volume1.append(df['Volume'][n])

  #print(df['Volume'][n])
for j in volume1:
  sum+=int(j)
  if sum < V0:
    S_diff.append(int(df['Volume'][len(df)-1])-int(j))
    S_date.append([df['Date'][len(df)-1], df['Date'][]])

p = max(S_diff)
print(p)
D = S_date
print(sorted(D))
```
