# yfinance
本次報告為11/9論文導讀yfinance作業，用**pandas**方式撰寫，透過paper(2011_Rare Events Analysis for High‐Frequency Equity Data)中的公式先抓取yfinance ` AAPL ` 近一年的資料，其中有` Volume `、` Date `及` Close `欄位，因為資料格式問題，有先進行新資料表匯出再進行後續部分。  
變數簡介->   
V0設定為'Volume'的mean()、S_diff：最後一個值減掉現在在算的值、S_date：最後一個時間和現在時間、max(S_diff：S_diff的最大值。  
公式如下：  
![圖片參考名稱](https://github.com/hsuyuandong/yfinance/blob/08fc854ab1bccca65c77a9b5fd4eafe42566fb66/%E4%B8%8B%E8%BC%89%20(1).png)  
![圖片參考名稱](https://github.com/hsuyuandong/yfinance/blob/ca6556c9b74ad3699782df2153c0afe095ba3d57/%E4%B8%8B%E8%BC%89.png)
