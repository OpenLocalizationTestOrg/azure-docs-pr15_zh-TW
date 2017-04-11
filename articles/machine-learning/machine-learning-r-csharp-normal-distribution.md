<properties 
    pageTitle="常態分配 Web 服務套件 |Microsoft Azure" 
    description="常態分配 Web 服務套件" 
    services="machine-learning" 
    documentationCenter="" 
    authors="ireiter" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="ireiter"/> 

#<a name="normal-distribution-suite"></a>常態分配套件



常態分配套件是一組範例 web 服務 （[產生器]( https://datamarket.azure.com/dataset/aml_labs/ndg7)， [Quantile 計算機]( https://datamarket.azure.com/dataset/aml_labs/ndq5)，[機率計算機]( https://datamarket.azure.com/dataset/aml_labs/ndp5)） 可協助產生及處理常態分配。 服務可讓產生常態分配序列的長度，計算 quantiles 從指定的機率，以及計算從指定的 quantile 的機率。 每個服務會發出不同的輸出根據所選的服務 （請參閱下方的描述）。 常態分配套件根據 R 函數 qnorm、 rnorm 及 pnorm，R stats 套件中包含的。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>可能由使用者 – 透過行動應用程式，透過網站，或甚至在本機電腦上，例如使用這個 web 服務。 但是 web 服務的目的也是要做為範例 Azure 電腦學習如何使用建立 web 服務 R 程式碼的上方。 以少數幾行 R 程式碼，只要按下 Azure 電腦學習 Studio 中] 按鈕的實驗可以 R 代碼以建立及發佈為 web 服務。 可以發佈至 Azure Marketplace 的 web 服務，然後由使用者和裝置世界 web 服務的作者沒有基礎結構設定。  
 

##<a name="consumption-of-web-service"></a>Web 服務的消耗
常態分配套件包含下列 3 服務。

###<a name="normal-distribution-quantile-calculator"></a>常態分配 Quantile 計算機
此服務可接受的常態累加分配的 4 個引數，並計算相關聯的 quantile。

輸入引數是︰

* p-單一機率為常態分配的事件。 
* 這表示-常態分配平均值。
* SD-常態分配的標準差。 
* 側邊的通訊群組的右下方的 L 和分配右上方的 U。

服務的輸出是指定的機率與相關聯的導出的 quantile。

###<a name="normal-distribution-probability-calculator"></a>常態分配的機率計算機
此服務可接受的常態累加分配的 4 個引數，並計算相關聯的機率。

輸入引數是︰

* 問-使用常態分配事件單一 quantile。 
* 這表示-常態分配平均值。
* SD-常態分配的標準差。 
* 側邊的通訊群組的右下方的 L 和分配右上方的 U。

服務的輸出是計算指定 quantile 與相關聯的機率。

###<a name="normal-distribution-generator"></a>常態分配產生器
此服務可接受的常態累加分配的 3 個引數，並產生隨機順序的數字以正常方式散發。 要求內，應該對其提供下列引數︰

* n-觀測值的數目。 
* 這表示-常態分配平均值。
* sd-常態分配的標準差。 

服務的輸出是以常態分配的平均值，sd 引數為基礎的長度 n 的一系列。

>這項服務，如下所裝載於 Azure 服務商場是 OData 服務;這些可能會被稱為透過文章或取得的方法。 

有多種方式可以使用的服務，以自動的方式 (範例應用程式在此處︰[產生器](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx)，[機率計算機](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx)， [Quantile 計算機](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx))。

###<a name="starting-c-code-for-web-service-consumption"></a>啟動 C# 程式碼的 web 服務消耗︰

###<a name="normal-distribution-quantile-calculator"></a>常態分配 Quantile 計算機
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="normal-distribution-probability-calculator"></a>常態分配的機率計算機
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="normal-distribution-generator"></a>常態分配產生器
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>使用 Azure 電腦學習建立此 web 服務。 免費的試用版，以及建立實驗及[發佈 web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱[azure.com/ml](http://azure.com/ml)。 

以下是建立 web 服務及範例程式碼的模組實驗中的每個實驗的螢幕擷取畫面。

###<a name="normal-distribution-quantile-calculator"></a>常態分配 Quantile 計算機

實驗流程︰

![實驗流程][2]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-probability-calculator"></a>常態分配的機率計算機
實驗流程︰

![實驗流程][3]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-generator"></a>常態分配產生器
實驗流程︰

![實驗流程][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>限制 

這些是非常簡單範例周圍常態分配。 小錯誤攔截實作可以看出從上述範例程式碼。

##<a name="faq"></a>常見問題集
常見問題集的 web 服務或發佈至 Azure Marketplace 上消耗問題，請參閱[以下](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 
