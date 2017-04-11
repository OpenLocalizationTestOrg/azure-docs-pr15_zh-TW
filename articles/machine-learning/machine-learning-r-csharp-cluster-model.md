<properties 
    pageTitle="叢集模型 |Microsoft Azure" 
    description="叢集模型" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>叢集模型    

我們如何預測信用卡 cardholders 行為的群組以減少信用卡簽發者收費關閉風險？ 我們要如何改善其效能作用中定義特質特性的員工的群組？ 如何可以醫生分類病患根據其疾病的特性的群組？ 原則，所有這些問題的可以回答透過叢集分析。   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
叢集分析將分類到兩個或多個互斥未知群組依據的變數組合觀察一組。 叢集分析的目的是要探索的群組，組織觀察值，通常人員或其特性，系統位置群組的成員共用的內容中的共有。 這項[服務](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model)使用 K 表示方法，常用的叢集技巧，叢集任意資料分成群組。 這個 web 服務取得資料，並 k 數叢集做為輸入，並產生預測，其中 k 所屬的群組的每個觀察值。 

>可能由使用者 – 透過行動應用程式，透過網站，或甚至在本機電腦上，例如使用這個 web 服務。 但是 web 服務的目的也是要做為範例 Azure 電腦學習如何使用建立 web 服務 R 程式碼的上方。 以少數幾行 R 程式碼，只要按下 Azure 電腦學習 Studio 中] 按鈕的實驗可以 R 代碼以建立及發佈為 web 服務。 可以發佈至 Azure Marketplace 的 web 服務，然後由使用者和裝置世界 web 服務的作者沒有基礎結構設定。  

##<a name="consumption-of-web-service"></a>Web 服務的消耗   
這個 web 服務將群組資料組成的一組 k 群組，並將輸出每個資料列的群組指派。 Web 服務預期使用者輸入為字串，並以逗號 （，） 分隔列及欄並以分號 （;） 分隔位置的資料。 Web 服務預期 1 的資料列，一次。 範例資料集可能看起來像這樣︰

![範例資料][1]

假設使用者想要分成 3 互斥群組中的資料。 輸入為上述資料集下列資料︰ 值 ="10; 5; 2,18; 1; 6,7; 5; 5,22; 3; 4,12; 2; 1,10; 3; 4 」;k ="3"。 輸出是預測的群組成員資格的每個資料列。

>這項服務，如下所裝載於 Azure 服務商場是 OData 服務;這些可能會被稱為透過文章或取得的方法。 

有多種方式可以使用的服務，以自動的方式 (範例應用程式是[以下](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx ))。

###<a name="starting-c-code-for-web-service-consumption"></a>啟動 C# 程式碼的 web 服務消耗︰

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

從 Azure 電腦學習，在新的空白實驗建立和[執行 R 指令碼]的兩個[execute-r-script]模組提取拖曳至工作區。 使用簡單的[執行 R 指令碼]所建立的資料結構描述[execute-r-script]。 然後，資料結構描述已連結至叢集模型] 區段中，再次建立含[執行 R 指令碼][execute-r-script]。 在[執行 R 指令碼][execute-r-script]用於叢集模型，web 服務然後利用 「 k 表示 「 函數，也就是將[執行 R 指令碼]預先建立[execute-r-script]Azure 電腦學習。    
   

     
![實驗流程][3]

####<a name="module-1"></a>模組 1: 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>模組 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>限制
這是叢集 web 服務的簡單範例。 可以看出從上述範例程式碼，實作沒有錯誤攔截並服務假設所有項目是連續變數 （無分類功能允許），服務輸入數字的值為此 web 服務建立的時間。 此外，服務目前處理有限的資料大小到期 web 服務呼叫和模型要符合的 web 服務稱為每次交易的要求/回應性質。 

##<a name="faq"></a>常見問題集
常見問題集的 web 服務或發佈至 Azure Marketplace 上消耗問題，請參閱[以下](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
