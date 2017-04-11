<properties
    pageTitle="Twitter 使用分析資料的 HDInsight Hadoop |Microsoft Azure"
    description="瞭解如何使用登錄區來分析 Twitter 上 Hadoop 中尋找特定文字的使用狀況頻率 HDInsight 的資料。"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Twitter 使用分析資料的 HDInsight 的登錄區

社交網站是主要的行駛強制的大型資料採用。 公用網站，例如 Twitter 所提供的 Api 是很有用的資料來源來分析及瞭解常用的趨勢。 在本教學課程中，您會使用 Twitter 串流 API，取得傳出 tweets，然後使用 Apache 登錄區 Azure HDInsight 上以取得 Twitter 使用者傳送大部分的推文包含特定文字的使用者清單。

> [AZURE.NOTE] 這份文件中的步驟需要 Windows 型 HDInsight 叢集。 步驟特定 Linux 叢集，請參閱[使用登錄區 HDInsight (Linux) 中的分析 Twitter 資料](hdinsight-analyze-twitter-data-linux.md)。


##<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- 使用 PowerShell 的 Azure**工作站**安裝和設定。 

    若要執行 Windows PowerShell 指令碼，您必須以系統管理員身分執行 PowerShell 的 Azure，並將*RemoteSigned*執行原則。 請參閱[執行 Windows PowerShell 指令碼][powershell-script]。

    之前執行的 Windows PowerShell 指令碼，請先確認您已連線到您 Azure 的訂閱，使用下列 cmdlet:

        Login-AzureRmAccount

    如果您有多個 Azure 訂閱，請使用下列 cmdlet 設定目前的訂閱︰

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Azure HDInsight 叢集**。 在 [叢集佈建的指示，請參閱[開始使用 HDInsight] [hdinsight-get-started]或[佈建 HDInsight 叢集] [hdinsight-provision]。 稍後在本教學課程中，您將需要叢集名稱。

下表列出此教學課程中所使用的檔案︰

檔案|描述
---|---
/tutorials/twitter/data/tweets.txt|群組工作的來源資料。
/tutorials/twitter/output|群組工作的輸出資料夾。 預設登錄區工作輸出檔案名稱是**000000_0**。
tutorials/twitter/twitter.hql|HiveQL 指令碼檔中。
/tutorials/twitter/jobstatus|Hadoop 工作狀態。


##<a name="get-twitter-feed"></a>取得 Twitter 摘要

在此教學課程中，您將使用[Twitter 串流 Api][twitter-streaming-api]。 特定 Twitter 的[篩選]狀態/串流的 API 會使用[twitter-statuses-filter]。

>[AZURE.NOTE] 公用 Blob 容器中已上傳檔案包含 10000 傳出 tweets 和登錄區指令碼檔案 （下一節所述）。 如果您想要使用上傳的檔案，您可以略過此節。

[傳出 tweets 資料](https://dev.twitter.com/docs/platform-objects/tweets)會儲存於 JavaScript 物件標記法 (JSON) 格式所包含的複雜的巢狀的結構。 而不是使用傳統的程式設計語言撰寫程式碼的行數，您可以將轉換為此巢狀的結構的登錄區資料表，使其可查詢的結構化查詢語言 (SQL)-等稱為 HiveQL 語言。

Twitter 使用 OAuth 提供其 API 未經授權的存取。 OAuth 是驗證通訊協定，讓使用者可以核准應用程式，而不必共用他們的密碼做代表自己。 可以找到更多資訊[oauth.net](http://oauth.net/)或絕佳[OAuth 入門指南](http://hueniverse.com/oauth/)中從 Hueniverse。

使用 OAuth 的第一個步驟是 Twitter 開發人員網站上建立新的應用程式。

**若要建立 Twitter 應用程式**

1. 登入[https://apps.twitter.com/](https://apps.twitter.com/)。 如果您沒有 Twitter 帳戶，請按一下 [**立即註冊**] 連結。
2. 按一下 [**建立新的應用程式**]。
3. 輸入**名稱**、**描述**，**網站**。 您可以將上的 [**網站**] 欄位的 URL。 下表顯示部分範例值，使用︰

    功能變數|值
    ---|---
    名稱|MyHDInsightApp
    描述|MyHDInsightApp
    網站|http://www.myhdinsightapp.com

4. 核取 [**我同意**]，然後按一下 [**建立 Twitter 應用程式**。
5. 按一下 [**權限**] 索引標籤。 預設權限是**唯讀狀態**。 這是滿足本教學課程中所需。
6. 按一下 [**索引鍵和存取權杖**] 索引標籤。
7. 按一下 [**建立我存取權杖**。
8. 按一下頁面右上角的 [**測試 OAuth** ]。
9. 寫下**消費者索引鍵**、**消費者私人**、**存取權杖**，以及**存取權杖私人**。 您必須稍後的教學課程中的值。

在本教學課程中，您會使用 Windows PowerShell 進行通話的 web 服務。 .NET C# 範例中，請參閱[使用 HBase HDInsight 中分析即時 Twitter 舉動][hdinsight-hbase-twitter-sentiment]。 若要打電話 web 服務的其他常用工具是[*Curl*][curl]。 您可以從[這裡]下載捲曲[curl-download]。

>[AZURE.NOTE] 當您在 Windows 中使用 [捲曲] 命令時，用於雙引號，而不是單引號括住的選項值。

**若要取得推文**

1. 開啟 Windows PowerShell 的整合指令碼環境 （ise [以系統）。 （在 Windows 8 [開始] 畫面中，輸入**PowerShell_ISE** ，然後按一下**Windows PowerShell ise [以系統**。 請參閱[啟動 Windows 8 上的 Windows PowerShell 和 Windows][powershell-start]。)

2. 將下列指令碼複製到 [指令碼] 窗格中︰

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. 設定第五到八個變數的指令碼︰


    變數|描述
    ---|---
    $clusterName|這是您要執行應用程式 HDInsight 叢集的名稱。
    $ oauth_consumer_key|這是您寫下較舊版本建立的 Twitter 應用程式時 Twitter 應用程式**消費者鍵**。
    $ oauth_consumer_secret|這是您寫下較舊版本 Twitter 應用程式**消費者密碼**。
    $ oauth_token|這是您寫下較舊版本 Twitter 應用程式**存取權杖**。
    $ oauth_token_secret|這是您寫下較舊版本 Twitter 應用程式**存取權杖密碼**。
    $destBlobName|這是輸出 blob 的名稱。 預設值是**tutorials/twitter/data/tweets.txt**。 如果您變更預設值，您必須適當地更新的 Windows PowerShell 指令碼。
    $trackString|Web 服務將會傳回與這些關鍵字相關的推文。 預設值是**Azure、 雲端 HDInsight**。 如果您變更預設值，您會依此更新的 Windows PowerShell 指令碼。
    $lineMax|值會決定指令碼會讀取多少推文。 閱讀 100 傳出 tweets 花三分鐘。 您可以設定較大的數字，但它會採取更多時間來下載。

5. 按**F5**執行指令碼。 如果您遇到問題，因應措施，選取所有行，然後按 [ **F8**。
6. 您應該會看到 「 完成 ！ 」 輸出的結尾。 以紅色，就會顯示任何錯誤訊息。

為驗證程序，您可以檢查輸出檔案， **/tutorials/twitter/data/tweets.txt**，在您使用 Azure 儲存檔案總管] 或 [Azure PowerShell 的 Azure Blob 儲存空間。 列出檔案範例 Windows PowerShell 指令碼，請參閱[使用 Blob 儲存體，與 HDInsight][hdinsight-storage-powershell]。



##<a name="create-hiveql-script"></a>建立 HiveQL 指令碼

使用 PowerShell 的 Azure，您可以執行多個 HiveQL 陳述式一次，或封裝成指令碼檔 HiveQL 陳述式。 在本教學課程中，您將建立 HiveQL 指令碼。 指令碼必須上傳檔案至 Azure Blob 儲存體。 在下一個區段中，您將使用 PowerShell 的 Azure 執行指令碼檔。

>[AZURE.NOTE] 公用 Blob 容器中已上傳的登錄區指令碼檔以及檔案包含 10000 推文。 如果您想要使用上傳的檔案，您可以略過此節。

HiveQL 指令碼會執行下列動作︰

1. 表格的大小寫的 [**卸除 tweets_raw 資料表**已經存在。
2. **建立 tweets_raw 登錄區資料表**。 這項暫時群組進一步的結構化的表格保留的資料擷取、 轉換，以及載入 (ETL) 處理。 在磁碟分割區上的資訊，請參閱[登錄區教學課程][apache-hive-tutorial]。  
3. 從 [來源] 資料夾，/tutorials/twitter/data**載入資料**。 大型的推文中的資料集巢狀 JSON 格式具有現在已轉換成暫存的登錄區資料表結構中。
3. 表格的大小寫的 [**卸除傳出 tweets 資料表**已經存在。
4. **建立傳出 tweets 表格**。 您可以使用登錄區查詢對傳出 tweets 資料集之前，您需要執行其他 ETL 處理程序。 此 ETL 程序定義更詳細的資料表結構描述 」 twitter_raw 」 資料表中儲存的資料。  
5. **插入覆寫表格**。 這個複雜的登錄區指令碼會啟動，Hadoop 叢集長 MapReduce 工作的一組。 視您的資料集和叢集的大小，而定，這可能需要 10 分鐘。
6. **插入覆寫目錄**。 執行查詢，輸出檔案的資料集。 此查詢會傳回 「 Azure 」 的 Twitter 使用者傳送大部分傳出 tweets 包含文字的使用者清單。

**若要建立登錄區指令碼，並將它上傳至 Azure**

1. 開啟 Windows PowerShell ise [以系統。
2. 將下列指令碼複製到 [指令碼] 窗格中︰

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. 設定的前兩個變數的指令碼︰

    變數|描述
    ---|---
    $clusterName|輸入您要執行應用程式的 HDInsight 叢集名稱。
    $subscriptionID|輸入您的 Azure 訂閱 id。
    $sourceDataPath|Azure Blob 儲存位置的登錄區查詢會讀取資料的位置。 您不需要變更此變數。
    $outputPath|Azure Blob 儲存位置的登錄區查詢輸出結果。 您不需要變更此變數。
    $hqlScriptFile|位置和檔案名稱 HiveQL 指令碼檔。 您不需要變更此變數。

5. 按**F5**執行指令碼。 如果您遇到問題，因應措施，選取所有行，然後按 [ **F8**。
6. 您應該會看到 「 完成 ！ 」 輸出的結尾。 以紅色，就會顯示任何錯誤訊息。

為驗證程序，您可以檢查輸出檔案， **/tutorials/twitter/twitter.hql**，在您使用 Azure 儲存檔案總管] 或 [Azure PowerShell 的 Azure Blob 儲存空間。 列出檔案範例 Windows PowerShell 指令碼，請參閱[使用 Blob 儲存體，與 HDInsight][hdinsight-storage-powershell]。  


##<a name="process-twitter-data-by-using-hive"></a>使用登錄區程序 Twitter 資料

您已準備的所有工作。 現在，您可以叫用的登錄區指令碼，並檢查結果。

### <a name="submit-a-hive-job"></a>提交登錄區工作

若要執行的登錄區指令碼中使用下列 Windows PowerShell 指令碼。 您必須設定第一個變數。

>[AZURE.NOTE] 若要使用傳出 tweets 和 HiveQL 指令碼您上傳的最後兩個區段中，設定 $hqlScriptFile 」 / tutorials/twitter/twitter.hql 」。 若要使用已上傳至公用 blob 為您的項目，設定為 $hqlScriptFile "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"。

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>檢查結果

您可以使用下列 Windows PowerShell 指令碼來檢查登錄區工作成果。 您必須設定前兩個變數。

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] 登錄區資料表的欄位分隔符號使用 \001。 輸出中看不到 [分隔符號。

分析結果已置於 Azure Blob 儲存體之後，您可以將資料匯出至 Azure SQL 資料庫/SQL server、 將資料匯出至 Excel，使用 Power Query，或使用登錄區 ODBC 驅動程式連線至資料的應用程式。 如需詳細資訊，請參閱[使用 Sqoop 與 HDInsight][hdinsight-use-sqoop]，[分析航班延遲情況資料增添使用 HDInsight][hdinsight-analyze-flight-delay-data]，[將 Excel 連接至使用 Power Query HDInsight][hdinsight-power-query]，並[將 Excel 連接至 Microsoft 登錄區 ODBC 驅動程式 HDInsight][hdinsight-hive-odbc]。

##<a name="next-steps"></a>後續步驟

在本教學課程我們瞭解如何將結構化的登錄區資料表至查詢、 探索及分析 Twitter 的資料使用 Azure HDInsight 轉換非結構化的 JSON 資料集。 若要深入瞭解，請參閱︰

- [快速入門 HDInsight][hdinsight-get-started]
- [分析即時 Twitter 舉動 HBase HDInsight 中使用][hdinsight-hbase-twitter-sentiment]
- [分析使用 HDInsight 航班延遲情況資料][hdinsight-analyze-flight-delay-data]
- [將 Excel 連接至 HDInsight 使用 Power Query][hdinsight-power-query]
- [將 Excel 連接至 Microsoft 登錄區 ODBC 驅動程式 HDInsight][hdinsight-hive-odbc]
- [使用 HDInsight Sqoop][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
