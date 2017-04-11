<properties 
    pageTitle="Azure 機器學習終極分析 |Microsoft Azure" 
    description="終極分析事件發生的機率" 
    services="machine-learning" 
    documentationCenter="" 
    authors="zhangya" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="zhangya"/> 


#<a name="survival-analysis"></a>終極分析 

在許多情況下，在評估主要的結果會是感興趣的事件的時間。 換句話說，問題 」 時就會發生此事件？ 」 系統要求。 做為範例，請考慮資料其中描述期間 （天、 年、 里程數等） 的情況下直到疾病 relapse、 收到博士角度 （煞車鍵台失敗） 感興趣的事件，就會發生。 資料中的每個執行個體表示特定物件 （病人，學生、 汽車等）。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

此[web 服務]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis)回答 「 什麼是機率感興趣的事件發生的物件 x? 時間 n 」 提供的終極分析模型，此 web 服務可讓使用者提供資料給訓練模型，並進行測試。 模型經過的時間長度，直到感興趣的事件發生時可實驗的主要佈景主題。 

>可能由使用者 – 透過行動應用程式，透過網站，或甚至在本機電腦上，例如使用這個 web 服務。 但是 web 服務的目的也是要做為範例 Azure 電腦學習如何使用建立 web 服務 R 程式碼的上方。 以少數幾行 R 程式碼，只要按下 Azure 電腦學習 Studio 中] 按鈕的實驗可以 R 代碼以建立及發佈為 web 服務。 可以發佈至 Azure Marketplace 的 web 服務，然後由使用者和裝置世界 web 服務的作者沒有基礎結構設定。  

##<a name="consumption-of-web-service"></a>Web 服務的消耗

下表中顯示的 web 服務的輸入的資料結構描述。 輸入所需的六個部分資訊︰ 訓練資料、 測試資料，感興趣的 「 時間 」 索引的時間維度、 「 活動 」 維度和變數類型的索引 (連續或因子變異數)。 訓練資料表示為字串，並以逗號分隔列及欄並以分號分隔位置。 資料的功能數目是非常有彈性。 輸入字串中的所有項目必須是數字。 訓練資料，在 「 時間 」 維度代表 （天、 年、 里程數等） 的時間單位的數字後的研究 （病人接收製藥處理程式、 學生起始博士研究、 買車開始驅動、 等） 的起點過直到感興趣 （病人返回製藥使用狀況，取得博士角度，不必煞車鍵台失敗汽車學生的事件等等。)就會發生。 「 活動 」 維度指出感興趣的事件是否發生研究的結尾。 值為 「 事件 = 1 」 所代表的意義感興趣的事件發生時所指出的 「 時間 」 維度。「 事件 = 0 」 感興趣的事件發生的 「 時間 」 維度所指定的時間表示。

- trainingdata-將字元字串。 以逗號分隔列和欄並以分號分隔。 每個資料列包含 「 時間 」 維度、 「 活動 」 維度和預測變數。
- testingdata-一列包含特定物件的預測變數的資料。
- time_of_interest-n 感興趣的經過的時間。
- index_time-（從 1 開始） 的 「 時間 」 維度的資料行索引。
- index_event-（從 1 開始） 的 「 活動 」 維度的資料行索引。
- variable_types-以分號做為分隔符號中將字元字串。 0 代表連續變數而 1 代表因子變異數的變數。


輸出是事件發生的特定時間的機率。 

>這項服務，如下所裝載於 Azure 服務商場是 OData 服務;這些可能會被稱為透過文章或取得的方法。 

有多種方式可以使用的服務，以自動的方式 (範例應用程式是[以下](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx))。 

###<a name="starting-c-code-for-web-service-consumption"></a>啟動 C# 程式碼的 web 服務消耗︰
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




這項測試的解譯如下所示。 假設資料的目標是直到返回製藥使用規定的兩個處理程式病患的模型經過的時間。 Web 服務讀取的輸出︰ 正在 35 歲病患有先前藥品處理 2 的時間，花的時間有多長住家處理程式，，heroin 和 cocaine 使用返回製藥使用方式的機率 95.64%的天 500。

##<a name="creation-of-web-service"></a>建立 web 服務的

>使用 Azure 電腦學習建立此 web 服務。 免費的試用版，以及建立實驗及[發佈 web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱[azure.com/ml](http://azure.com/ml)。 以下是建立 web 服務及範例程式碼的模組實驗中的每個實驗的螢幕擷取畫面。

從 Azure 電腦學習，在新的空白實驗建立和[執行 R 指令碼]的兩個[execute-r-script]模組提取拖曳至工作區。 使用簡單的[執行 R 指令碼]所建立的資料結構描述[execute-r-script]，其定義的 web 服務的輸入的資料結構描述。 本單元會連結至第二個[執行 R 指令碼][execute-r-script]模組，會主要工作。 本單元沒有前置處理資料模型建立與預測。 在資料前置處理步驟中，輸入完整的字串表示的資料轉換，而轉換成資料範圍。 在模型建置步驟中，將外部 R 套件 」 survival_2.37 7.zip 「 第一次安裝進行終極分析。 然後系列資料處理工作後執行 「 coxph 」 函數。 終極分析 「 coxph 」 函數的詳細資料可以讀取 R 文件。 在預測步驟中，提供的測試執行個體至訓練模型 」 surfit 」 函數，且此測試執行個體的終極曲線產生為 「 曲線 」 變數。 最後，取得感興趣的時間的機率。 

###<a name="experiment-flow"></a>實驗流程︰

![嘗試流程][1]

####<a name="module-1"></a>模組 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
    
####<a name="module-2"></a>模組 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##<a name="limitations"></a>限制

這個 web 服務可能需要只數值為功能變數 （欄）。 「 事件 」 欄中可以採取只值 0 或 1。 「 時間 」 欄必須是正整數。

##<a name="faq"></a>常見問題集
常見問題集的 web 服務或發佈至 Azure Marketplace 上消耗問題，請參閱[以下](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
