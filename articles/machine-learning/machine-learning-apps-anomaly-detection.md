<properties 
    pageTitle="電腦學習應用程式︰ 異常偵測服務 |Microsoft Azure" 
    description="異常偵測 API 是使用 Microsoft Azure 電腦學習時間數列資料一致地為時間單行間距的數值與中偵測到異常的範例。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>電腦學習異常偵測服務#

##<a name="overview"></a>概觀

[異常偵測 API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)是使用 Azure 電腦學習時間數列資料一致地為時間單行間距的數值與中偵測到異常的範例。 

此 API 可以偵測下列類型的異常模式中時間數列資料︰

* **正數和負的趨勢**:，例如，當監控運算向上走向記憶體使用量可能會感興趣的作為可能表示記憶體遺漏，

* **動態值的範圍中的變更**︰ 例如，監視擲回雲端服務例外狀況、 動態值的範圍中的任何變更可能表示不穩定的服務健康狀況和

* **尖峰和 Dips**︰ 例如，監視服務中的登入失敗的數目或的取出電子商務網站中的數字、 特殊圖文集或 dips 可能表示異常行為。

這些電腦學習偵測器這類中追蹤修訂值時間和報表進行中的變更，其值中透過為異常分數。 不需要調整臨機操作閾值和其分數可以用來控制 false 正數的工資率。 API 很有用等服務監視追蹤 Kpi 一段時間，以數種案例異常偵測透過指標，例如搜尋的數字、 數字，只要按下效能監視計數器記憶體，CPU，例如透過檔案讀取、 的等監控一段時間的使用方式。

異常偵測提供隨附實用的工具，協助您入門。 

* [Web 應用程式](http://anomalydetection-aml.azurewebsites.net/)可協助您評估，並以視覺化方式呈現資料的異常偵測 Api 的結果。 

* [程式碼範例](http://adresultparser.codeplex.com/)會示範如何以程式控制方式存取 API 及剖析 C# 中的結果。

>[AZURE.NOTE]
>請嘗試**IT 異常獲得深入見解解決方案**由[這個 API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>若要取得此部署 Azure 訂閱<a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">的端對端解決方案**從這裡開始 >**</a>


##<a name="api-definition"></a>API 定義

服務，提供一個的 REST API 透過 HTTPS 都可以使用不同的方式，包括在 web 或行動應用程式，R、 Python Excel 等。 您透過打電話 REST API，此服務傳送時間數列資料，然後執行上述三個異常類型的組合。 流暢地調整至您的業務需求，並提供 Sla Azure 電腦學習平台上，執行服務。

###<a name="headers"></a>頁首
確保正確的標頭納入您的要求，應如下︰

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

您可以從您的帳戶中找到您的帳戶金鑰， [Azure 資料市場](https://datamarket.azure.com/account/keys)中。 

###<a name="score-api"></a>成績 API

成績 API 用於非季節性時間數列資料上執行異常偵測。 API 執行異常偵測的數字的資料，並傳回其異常分數。 下圖顯示異常分數 API 可以偵測的範例。 此時間序列有 2 不同層級的變更，然後 3 的波峰。 紅色閃動的層級的變更偵測到，而點顯示偵測到的波峰上的時間。

![成績 API][1]
    
**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**範例邀請內文**

在下面的邀請中，我們會傳送時間序列 （顯示截斷） 的特殊圖文集] 偵測參數從 3/1/2016 透過 3/10/2016年的資料點與偵測異常如果有任何這些資料點的 api。 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

會是此回應︰ 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>ScoreWithSeasonality API

ScoreWithSeasonality API 用於上有季節性圖樣的時間序列執行異常偵測。 此 API 很有用的季節性模式偵測誤差。  

下圖中的季節性時間序列偵測到的異常的範例。 時間序列有一個特殊圖文集 （第 1 的黑色圓點），兩個 dips （第 2 的黑色圓點與一個結尾處），一個層級變更 （紅色圓點）。 請注意，這兩個 dip 中間時間序列和層級的變更只明顯之後季節性元件移除數列。

![季節性 API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**範例邀請內文**

在下面的邀請中，我們會偵測異常如果有任何這些資料點的 api 傳送時間序列 （顯示截斷） 從 3/1/2016 透過 3/10/2016年的資料點與參數。 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

會是此回應︰ 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>偵測器

異常偵測 API 支援 3 大類保護。 下表中找特定輸入的參數和輸出的每個偵測器的詳細資訊。

|偵測器類別|偵測器|描述|輸入的參數|輸出
|---|---|---|---|---|
|特殊圖文集] 偵測|TSpike 偵測器|偵測到特殊圖文集和 dips 根據目前的值是從第一個和第三個四|*tspikedetector.sensitivity:*所需的時間範圍內的整數值 1-10，預設︰ 3;較高的值會掌握更多極大的值，因此很敏感度|TSpike︰ 二進位值-'1' 如果偵測到特殊圖文集/dip、 '0' 否則|
||ZSpike 偵測器|偵測特殊圖文集和 dips 根據正值資料操作技巧的平均值|*zspikedetector.sensitivity:*採取範圍中的整數值 1-10，預設︰ 3;較高的值會掌握更多極大的值，讓它不區分|ZSpike︰ 二進位值-'1' 如果偵測到特殊圖文集/dip、 '0' 否則|
|變得很慢趨勢偵測器|變得很慢趨勢偵測器|偵測依照設定敏感度變得很慢正趨勢|*trenddetector.sensitivity:*上偵測分數閾值 (預設︰ 3.25，3.25 – 5 是合理的範圍，若要選取此選項。高較少的區分）|TScore︰ 浮動數字代表趨勢上的異常成績|
|保護層級的變更|單向層級的變更偵測器|偵測向上層級設定敏感度依照變更|*upleveldetector.sensitivity:*上偵測分數閾值 (預設︰ 3.25，3.25 – 5 是合理的範圍，若要選取此選項。高較少的區分）|PScore︰ 浮動數字，代表異常分數上，向上層級變更|
||雙向層級變更偵測器|偵測向上和向下層級的變更，依照設定區分大小寫|*bileveldetector.sensitivity:*上偵測分數閾值 (預設︰ 3.25，3.25 – 5 是合理的範圍，若要選取此選項。高較少的區分）|RPScore︰ 浮動分數向上和向下層級變更的數字代表異常

###<a name="parameters"></a>參數

下表中，會列出這些輸入參數的詳細的資訊︰

|輸入的參數|描述|預設設定|類型|有效範圍|建議的範圍|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|彙總間隔彙總的秒數輸入時間序列|0 （執行彙總）|整數|0︰ 否則略過彙總，> 0|5 分鐘，1 天，時間序列相依性
|preprocess.aggregationFunc|使用資料到指定的 AggregationInterval 彙總函數|意義|列舉|平均數，加總] 長度|N/A|
|preprocess.replaceMissing|用來 impute 遺失的資料值|lkv （上次的值）|列舉|零 lkv、 平均值|N/A|
|detectors.historyWindow|用於異常分數計算的記錄 （在 [資料點的 #）|500|整數|2000 年 10 月|時間序列相依性|
|upleveldetector.sensitivity|向上層級的敏感度變更偵測器。 |3.25|雙引號|無|3.25 5(Lesser values mean more sensitive)|
|bileveldetector.sensitivity|雙向層級的敏感度變更偵測器。 |3.25|雙引號|無|3.25 5(Lesser values mean more sensitive)|
|trenddetector.sensitivity|正數趨勢偵測器的敏感度。 |3.25|雙引號|無|3.25 5(Lesser values mean more sensitive)|
|tspikedetector.sensitivity |區分大小寫的 TSpike 偵測器|3|整數|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.sensitivity |區分大小寫的 ZSpike 偵測器|3|整數|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.enable|是否要執行季節性分析|true|布林值|為 true，則為 false|時間序列相依性|
|seasonality.numSeasonality |偵測到的定期週期的數目上限|1|整數|1、 2|1-2|
|seasonality.transform |是否季節性 （及） 趨勢元件都應該會移除套用異常偵測之前|deseason|列舉|無，deseason deseasontrend|N/A|
|postprocess.tailRows |若要保存在輸出結果最新的資料點的數目|0|整數|0 （保留所有資料點），或指定要都保留在結果中點的數目|N/A|

###<a name="output"></a>輸出
API 時間數列資料執行所有偵測器，傳回異常分數和每個資料點的二進位特殊圖文集指標中時間。 下表列出從 API 輸出。 

|輸出|描述|
|---|---|
|時間|從原始資料或彙總 （及/或） imputed 資料的時間戳記如果彙總 （及/或） 遺失資料 imputation 套用|
|OriginalData|從原始資料或 imputed 彙總 （/） 的資料值，如果彙總 （及/或） 遺失資料 imputation 套用|
|ProcessedData|下列其中一項︰ <ul><li>如果嚴重的季節性已偵測到和 deseason 選項已選取; seasonally 調整時間序列</li><li>seasonally 調整和 detrended 時間序列偵測到嚴重季節性如果和選取 deseasontrend 選項</li><li>否則，這是 OriginalData 相同</li>|
|TSpike|二進位表示是否偵測到的 TSpike 偵測器的特殊圖文集] 指標|
|ZSpike|二進位表示是否偵測到的 ZSpike 偵測器的特殊圖文集] 指標|
|Pscore|浮動的數字，代表異常分數向上層級變更|
|Palert|1/0 的值，指出向上的層級變更異常根據輸入區分大小寫|
|RPScore|浮動的數字代表異常分數雙向層級的變更|
|RPAlert|1/0 的值，指出雙向層級變更異常根據輸入區分大小寫|
|TScore|浮動的數字代表異常分數上正數的趨勢|
|TAlert|1/0 的值，指出有是根據輸入敏感度正趨勢異常|


可以使用[簡單的剖析器](https://adresultparser.codeplex.com/)剖析輸出-有顯示如何連線至 API 和剖析輸出的範例。 偵測到異常可以視覺化儀表板上及/或傳遞至人力專家矯正或整合票券系統。


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 
