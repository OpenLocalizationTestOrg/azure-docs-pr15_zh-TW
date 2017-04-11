<properties 
    pageTitle="預測︰ Autoregressive 整合移動平均 (ARIMA) |Microsoft Azure" 
    description="預測-Autoregressive 整合移動平均 (ARIMA)" 
    services="machine-learning" 
    documentationCenter="" 
    authors="yijichen" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="yijichen"/> 

 
#<a name="forecasting---autoregressive-integrated-moving-average-arima"></a>預測-Autoregressive 整合移動平均 (ARIMA)

這項[服務]( https://datamarket.azure.com/dataset/aml_labs/arima)實作 Autoregressive 整合式移動平均 (ARIMA) 來產生使用者所提供的歷史資料進行預測。 特定產品的會增加今年嗎？ 可以我預測產品銷售的聖誕節佳節，讓我有效計劃我庫存嗎？ 若要解決此問題會預測的模型。 指定過去的資料，這些模型檢查隱藏的趨勢和季節性預測未來的趨勢。 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

>可能由使用者 – 透過行動應用程式，透過網站，或甚至在本機電腦上，例如使用這個 web 服務。 但是 web 服務的目的也是要做為範例 Azure 電腦學習如何使用建立 web 服務 R 程式碼的上方。 以少數幾行 R 程式碼，只要按下 Azure 電腦學習 Studio 中] 按鈕的實驗可以 R 代碼以建立及發佈為 web 服務。 可以發佈至 Azure Marketplace 的 web 服務，然後由使用者和裝置世界 web 服務的作者沒有基礎結構設定。

##<a name="consumption-of-web-service"></a>Web 服務的消耗 

這項服務接受 4 引數，並計算 ARIMA 預測。
輸入引數是︰

* 頻率-指示的頻率 （每日/週/月/每季/年度） 的原始資料。
* 水平-未來預測時間範圍。
* 日期-時間新增新的時間系列資料。
* 值-新增新的時間數列的資料值。

服務的輸出是計算的預測的值。 

範例輸入可能是︰ 

* 頻率-12
* 水平-12
* 日期-1/15/2012年; 2/15/2012年; 3/15/2012年; 4/15/2012年; 5/15/2012年; 6/15/2012年; 7/15/2012年; 8 /15/2012年; 9/15/2012年; 10/15/2012年; 11/15/2012年; 12/15/2012。1/15/2013年; 2013/2/15; 3/15/2013年; 4/15/2013年; 5/15/2013年; 6/15/2013年; 7/15/2013年; 8 /15/2013年; 9/15/2013年; 10/15/2013年; 11/15/2013年; 12/15/2013;1/15/2014年; 2/15/2014年; 3/15/2014年; 4/15/2014年; 5/15/2014年; 2014/6/15; 7/15/2014年; 8 /15/2014年; 2014/9/15
* 值-3.479; 3.68; 3.832; 3.941; 3.797; 3.586; 3.508; 3.731; 3.915; 3.844; 3.634; 3.549; 3.557; 3.785; 3.782; 3.601; 3.544; 3.556; 3.65; 3.709; 3.682; 3.511;3.429; 3.51; 3.523; 3.525; 3.626; 3.695; 3.711; 3.711; 3.693; 3.571; 3.509
 
>這項服務，如下所裝載於 Azure 服務商場是 OData 服務;這些可能會被稱為透過文章或取得的方法。 

有多種方式可以使用的服務，以自動的方式 (範例應用程式是[以下](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx))。

###<a name="starting-c-code-for-web-service-consumption"></a>啟動 C# 程式碼的 web 服務消耗︰

    public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

       
    void Main()
    {
        var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
        var json = JsonConvert.SerializeObject(input);
        var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
           
        var httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        var query = httpClient.PostAsync(acitionUri,new StringContent(json));
        var result = query.Result.Content;
        var scoreResult = result.ReadAsStringAsync().Result;
    }

##<a name="creation-of-web-service"></a>建立 web 服務的 

>使用 Azure 電腦學習建立此 web 服務。 免費的試用版，以及建立實驗及[發佈 web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱[azure.com/ml](http://azure.com/ml)。 以下是建立 web 服務及範例程式碼的模組實驗中的每個實驗的螢幕擷取畫面。

從 Azure 電腦學習，在新的空白實驗所建立。 使用預先定義的資料結構描述，範例輸入的資料已上傳。 連結至資料結構描述是[執行 R 指令碼][execute-r-script]模組，會產生 ARIMA 預測模型使用 「 auto.arima 」 和 「 預測' 函數從。 

###<a name="experiment-flow"></a>實驗流程︰

![建立工作區][2]

####<a name="module-1"></a>模組 1:
 
    # Add in the CSV file with the data in the format shown below 
![建立工作區][3]  

####<a name="module-2"></a>模組 2:
    # data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- auto.arima(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)
    
    # produce forecasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # data output
    maml.mapOutputPort("data.forecast");


##<a name="limitations"></a>限制 

這是非常簡單的範例 ARIMA 預測。 能夠看到在上述範例程式碼，沒有錯誤攔截實作，而且服務假設所有變數都是正數連續/值，而頻率應大於 1 的整數。 日期及值向量的長度應相同。 日期變數應該遵守 「 mm/dd/yyyy ' 的格式。

##<a name="faq"></a>常見問題集
常見問題集的 web 服務或發佈至市場上消耗問題，請參閱[以下](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
