<properties 
    pageTitle="字詞基礎舉動分析 |Microsoft Azure" 
    description="字詞基礎舉動分析" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>字詞基礎舉動分析 

如何可以您測量使用者的意見，以及態度向品牌或主題線上的社交網路，例如 Facebook 文章推文、 檢閱等等。？ 舉動分析提供的分析這類問題的方法。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

通常有兩種方法可以舉動分析。 有使用受監督的學習演算法和其他可被視為不學習。 受監督的學習演算法通常是根據分類模型大型的註解中華。 其精確度主要為基礎的註釋，品質，通常訓練程序會花很長的時間。 除了，我們將演算法套用至另一個網域，結果為通常不建議。 相較於指導學習的字詞不的學習使用的是舉動字典，不需要儲存大型資料中華和訓練-這可讓您更快速的整個程序。 

我們的[服務](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis)是內建在 MPQA 主觀性字典 (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/)，也就是其中一個最常用的主觀性 lexicons。 MPQA 有 5097 負數和 2533年正數的文字。 與所有這些字會加註以加強或弱式信號。 整個中華是 [GNU 一般公用授權。 Web 服務可以套用至任何簡短的句子，例如傳出 tweets 與 Facebook 的文章。 

>無法透過行動應用程式，透過網站，或甚至在本機電腦上的使用者-由，例如使用這個 web 服務。 但是 web 服務的目的也是要做為範例 Azure 電腦學習如何使用建立 web 服務 R 程式碼的上方。 以少數幾行 R 程式碼，只要按下 Azure 電腦學習 Studio 中] 按鈕的實驗可以 R 代碼以建立及發佈為 web 服務。 可以發佈至 Azure Marketplace 的 web 服務，然後由使用者和裝置世界 web 服務的作者沒有基礎結構設定。

##<a name="consumption-of-web-service"></a>Web 服務的消耗

輸入的資料的任何文字，但簡短的句子更有效地使用 web 服務。 輸出是-1 到 1 之間的數值。 下面 0 的任何值表示舉動文字使用的是負數。如果上述 0 正數。 結果的絕對值表示相關聯的舉動強度。 

>這項服務，如下所裝載於 Azure 服務商場是 OData 服務;這些可能會被稱為透過文章或取得的方法。 

有多種方式可以使用的服務，以自動的方式 (範例應用程式是[以下](http://microsoftazuremachinelearning.azurewebsites.net/))。

###<a name="starting-c-code-for-web-service-consumption"></a>啟動 C# 程式碼的 web 服務消耗︰

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



輸入是"Today is 你好"。 輸出是 「 1 」，這表示輸入句子與相關聯的正數舉動。 

##<a name="creation-of-web-service"></a>建立 web 服務的
>使用 Azure 電腦學習建立此 web 服務。 免費的試用版，以及建立實驗及[發佈 web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱[azure.com/ml](http://azure.com/ml)。 以下是建立 web 服務及範例程式碼的模組實驗中的每個實驗的螢幕擷取畫面。


從 Azure 電腦學習，在新的空白實驗所建立。 下圖顯示實驗的流程以字詞為基礎的舉動分析。 「 Sent_dict.csv 」 檔案 MPQA 主觀性字典，並設定為[執行 R 指令碼]的輸入之一[execute-r-script]。 另一個輸入是從 Amazon 檢閱資料集的測試，我們執行選取範圍，修改資料行名稱，並分割作業取樣的檢閱。 我們使用雜湊套件的記憶體儲存主觀性字詞及加速分數計算程序。 將 「 問題 」 封裝 token 化整個文字，並將其比較舉動字典中的單字。 最後，分數會進行計算的文字中新增的每個主觀的文字粗細。 

###<a name="experiment-flow"></a>實驗流程︰

![嘗試流程][2]


####<a name="module-1"></a>模組 1:
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>安裝雜湊套件
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>限制

從演算法觀點來看，以字詞為基礎的舉動分析是定律分析工具，而可能不會優於特定欄位的 [分類] 方法。 負問題也不會處理完畢。 我們硬數個負文字，在我們的程式中，但更好的方法是使用負字典，並建立一些規則。 Web 服務執行更簡短的句子，例如傳出 tweets 及 Facebook 文章上比冗長且複雜的句子，例如 Amazon 評論。 

##<a name="faq"></a>常見問題集
常見問題集的 web 服務或發佈至 Azure Marketplace 上消耗問題，請參閱[以下](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 
