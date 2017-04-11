<properties 
   pageTitle="資料流分析限制表格"
   description="說明系統限制，以及建議的大小，串流分析元件和連線。"
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| 限制識別碼 | 限制       | 註解 |
|----------------- | ------------|--------- |
| 每個訂閱，每個地區串流單位的最大數目 | 50 | 連絡[Microsoft 支援](https://support.microsoft.com/en-us)可增加串流單位，超過 50 訂閱的要求。 |
| 串流單位的最大處理量 | 1 MB / s * | 最大處理量 24 每取決於此案例。 實際處理量可能是較低，且查詢複雜度和分割而定。 [若要增加處理量的縮放比例 Azure 資料流分析工作](../articles/stream-analytics/stream-analytics-scale-jobs.md)文件中找進一步的詳細資料。 |
| 每個作業的輸入的最大的數字 | 60 | 有的每個資料流分析作業的 60 輸入嚴格的限制。 |
| 每個作業的輸出的最大數目 | 60 | 有的每個資料流分析作業的 60 輸出嚴格的限制。 |
| 最大的數字的每個作業的函數 | 60 | 有 60 的函數，每個資料流分析作業的硬限制。 |
| 每個地區工作數目上限 | 1500 | 最 1500年工作，每個地理區域，可能會有每個訂閱。 |