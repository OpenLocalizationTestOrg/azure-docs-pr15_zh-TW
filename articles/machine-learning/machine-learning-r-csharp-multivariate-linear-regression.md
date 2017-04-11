<properties 
    pageTitle="Multivariate 線性迴歸 |Microsoft Azure" 
    description="Multivariate 線性迴歸" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>Multivariate 線性迴歸   
 

 
假設您有資料集，並想要快速預測因變數 (y) 為根據獨立變數的每個人 (i)。 線性迴歸是用於這類預測熱門統計技巧。 以下因變數 y 會引用本身的連續的值。  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

簡單的案例可能研究人員嘗試預測個別 (y) 根據其高度 (x) 的粗細。 研究人員有 （例如寬度、 性別、 競賽） 個別的其他資訊的位置，並嘗試預測個別的粗細，可能是更進階的做法。 此[web 服務]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression)符合線性迴歸模型資料，並將預測的值 (y) 輸出的每個資料中的觀察值。

>可能由使用者 – 透過行動應用程式，透過網站，或甚至在本機電腦上，例如使用這個 web 服務。 但是 web 服務的目的也是要做為範例 Azure 電腦學習如何使用建立 web 服務 R 程式碼的上方。 以少數幾行 R 程式碼，只要按下 Azure 電腦學習 Studio 中] 按鈕的實驗可以 R 代碼以建立及發佈為 web 服務。 可以發佈至 Azure Marketplace 的 web 服務，然後由使用者和裝置世界 web 服務的作者沒有基礎結構設定。  

##<a name="consumption-of-web-service"></a>Web 服務的消耗  
這個 web 服務可讓您根據針對您所有的觀察獨立變數的因變數的預測的值。 Web 服務預期使用者輸入為字串，並以逗號 （，） 分隔列及欄並以分號 （;） 分隔位置的資料。 Web 服務預期 1 的資料列，一次，而且預期會因變數的第一欄。 範例資料集可能看起來像這樣︰

![範例資料][1]

觀察因變數不應該會輸入為 「 NA 」 y。 輸入為上述資料集的下列字串的資料: 「 10; 5; 2,18; 1; 6,6; 5.3; 2.1,7; 5; 5,22; 3; 4,12; 2; 1 NA; 3; 4 」。 輸出是根據獨立變數的資料列的每個預測的值。 

>這項服務，如下所裝載於 Azure 服務商場是 OData 服務;這些可能會被稱為透過文章或取得的方法。 

有多種方式可以使用的服務，以自動的方式 (範例應用程式是[以下](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx ))。

###<a name="starting-c-code-for-web-service-consumption"></a>啟動 C# 程式碼的 web 服務消耗︰

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




##<a name="creation-of-web-service"></a>建立 web 服務的  
>使用 Azure 電腦學習建立此 web 服務。 免費的試用版，以及建立實驗及[發佈 web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱[azure.com/ml](http://azure.com/ml)。 以下是建立 web 服務及範例程式碼的模組實驗中的每個實驗的螢幕擷取畫面。


從 Azure 電腦學習，在新的空白實驗建立和[執行 R 指令碼]的兩個[execute-r-script]模組提取拖曳至工作區。 這個 web 服務會執行 Azure 電腦學習實驗基礎的 R 指令碼。 有這個實驗 2 部分︰ 結構描述定義和訓練模型 + 分數。 第一個模組定義的輸入資料集，其中的第一個變數是因變數而剩餘的變數是獨立預期的結構。 第二個模組適合輸入資料的一般的線性迴歸模型。  
  
![實驗流程][3]

####<a name="module-1"></a>模組 1:
 
####<a name="schema-definition"></a>結構描述定義  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>模組 2:
####<a name="lm-modeling"></a>LM 模型   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>限制
這是非常簡單的多個線性迴歸 web 服務的範例。 可以看出從上述範例程式碼，實作沒有錯誤攔截並服務假設所有項目是連續變數 （無分類功能允許），服務輸入數字的值為此 web 服務建立的時間。 此外，服務目前處理有限的資料大小到期 web 服務呼叫和模型要符合的 web 服務稱為每次交易的要求/回應性質。 

##<a name="faq"></a>常見問題集
常見問題集的 web 服務或發佈至 Azure Marketplace 上消耗問題，請參閱[以下](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
