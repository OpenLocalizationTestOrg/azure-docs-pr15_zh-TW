<properties
    pageTitle="Twitter 使用分析資料 Apache 登錄區上 HDInsight |Microsoft Azure"
    description="瞭解如何使用 Python 儲存推文包含特定關鍵字，然後使用登錄區和 Hadoop 上 HDInsight 原始 TWitter 資料轉換成可搜尋的登錄區資料表。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="larryfr"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Twitter 使用分析資料的 HDInsight 的登錄區

在此文件中，將會收到傳出 tweets 使用串流 API Twitter，然後使用 Apache 登錄區 Linux 型 HDInsight 叢集程序 JSON 格式化資料。 則結果為 Twitter 使用者傳送大部分的推文包含特定文字的使用者清單。

> [AZURE.NOTE] 雖然這份文件的各個部分可以用於 Windows 型 HDInsight 叢集 (例如 Python)，許多步驟會根據使用 Linux 型 HDInsight 叢集。 步驟特定到 windows 叢集，請參閱[使用登錄區中 HDInsight Twitter 分析資料](hdinsight-analyze-twitter-data.md)。

###<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- __Linux 型 Azure HDInsight 叢集__。 建立叢集的詳細資訊，請參閱的相關步驟建立叢集[Linux 型 HDInsight 快速入門](hdinsight-hadoop-linux-tutorial-get-started.md)。

- __SSH 用戶端__。 如需有關使用 Linux 型 HDInsight SSH 的詳細資訊，請參閱下列文章︰

    * [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__和[pip](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>取得摘要 Twitter

Twitter 可讓您透過 REST API JavaScript 物件標記法 (JSON) 文件為擷取[資料的每個推文](https://dev.twitter.com/docs/platform-objects/tweets)。 需要驗證 api [OAuth](http://oauth.net) 。 您必須同時建立包含用來存取 API 設定_Twitter 應用程式_。

###<a name="create-a-twitter-application"></a>建立 Twitter 應用程式

1. 從網頁瀏覽器，登入[https://apps.twitter.com/](https://apps.twitter.com/)。 如果您沒有 Twitter 帳戶，請按一下 [**立即註冊**] 連結。
2. 按一下 [**建立新的應用程式**]。
3. 輸入**名稱**、**描述**，**網站**。 您可以將上的 [**網站**] 欄位的 URL。 下表顯示部分範例值，使用︰

  	| 功能變數 | 值 |
  	|:----- |:----- |
  	| 名稱  | MyHDInsightApp |
  	| 描述 | MyHDInsightApp |
  	| 網站 | http://www.myhdinsightapp.com |
    
4. 核取 [**我同意**]，然後按一下 [**建立 Twitter 應用程式**。
5. 按一下 [**權限**] 索引標籤。 預設權限是**唯讀狀態**。 這是滿足本教學課程中所需。
6. 按一下 [**索引鍵和存取權杖**] 索引標籤。
7. 按一下 [**建立我存取權杖**。
8. 按一下頁面右上角的 [**測試 OAuth** ]。
9. 寫下**消費者索引鍵**、**消費者私人**、**存取權杖**，以及**存取權杖私人**。 您稍後需要值。

>[AZURE.NOTE] 當您在 Windows 中使用 [捲曲] 命令時，用於雙引號，而不是單引號括住的選項值。

###<a name="download-tweets"></a>下載推文

下列 Python 程式碼會從 Twitter 下載 10000 推文，並將它們儲存在名為__tweets.txt__的檔案。

> [AZURE.NOTE] 由於 Python 已預先安裝在 HDInsight 叢集上，執行下列步驟。

1. 連線到使用 SSH HDInsight 叢集︰

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    如果您是使用密碼保護您 SSH 的使用者帳戶，系統會提示您輸入。 如果您使用公開金鑰，您可能必須使用`-i`參數指定相符的私人索引鍵。 例如， `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`。
        
    如需有關使用 Linux 型 HDInsight SSH 的詳細資訊，請參閱下列文章︰
    
    * [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows)
    
2. 根據預設，[HDInsight 標頭節點上未安裝__pip__公用程式。 若要安裝，請使用下列，然後更新此公用程式︰

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. 若要下載傳出 tweets 的程式碼依賴[Tweepy](http://www.tweepy.org/)和[進度列](https://pypi.python.org/pypi/progressbar/2.2)。 若要安裝這些，請使用下列命令︰

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] 移除 python openssl，安裝 python 開發、 libffi 開發、 libssl 開發、 pyOpenSSL 和要求 [安全性] 相關的位元是從 Python 透過 SSL Twitter 連結時，避免 InsecurePlatform 警告。
    >
    > 若要避免[發生錯誤](https://github.com/tweepy/tweepy/issues/576)處理傳出 tweets 時可能發生的使用 Tweepy v3.2.0。

4. 若要建立新的檔名為__gettweets.py__使用下列命令︰

        nano gettweets.py

5. 您可以使用下列作為__gettweets.py__檔案的內容。 取代的版面配置區資訊__消費者\_私人__，__消費者\_鍵__，__存取 /\_token__，及__存取\_token\_私人__Twitter 應用程式中的資訊。

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. 使用__Ctrl + X__，然後__Y__來儲存檔案。

7. 若要執行的檔案，並下載推文中使用下列命令︰

        python gettweets.py

    進度指示器應該出現，並計算到 100%傳出 tweets 下載，以及儲存至檔案。

    > [AZURE.NOTE] 如果所需花費很長的時間，若要進入進度列，您應該變更篩選，以追蹤趨勢主題;當您要篩選的主題相關的傳出 tweets 許多時，您可以快速取得所需 10000 推文。

###<a name="upload-the-data"></a>上傳的資料

若要將資料的上傳到 WASB （HDInsight 所使用的分散式的檔案系統），使用下列命令︰

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

這是在叢集內所有節點可以都存取的位置儲存的資料。

##<a name="run-the-hiveql-job"></a>執行 HiveQL 工作


1. 若要建立包含 HiveQL 陳述式的檔案中使用下列命令︰

        nano twitter.hql
    
    使用下列檔案的內容︰

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
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
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
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
        
        
3. 按下__Ctrl + X__，然後按下__Y__儲存檔案。

4. 若要執行檔案中包含 HiveQL 使用下列命令︰

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    這會載入登錄區命令介面、 __twitter.hql__檔案中，執行 HiveQL 及最後傳回`jdbc:hive2//localhost:10001/>`提示。
    
5. 從 beeline 提示時，使用下列命令以確認您可以建立__twitter.hql__檔案中 HiveQL__傳出 tweets__資料表中選取資料︰
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    這會傳回 10 個傳出 tweets 包含 word __Azure__訊息文字中的最大值。

##<a name="next-steps"></a>後續步驟

在本教學課程我們瞭解如何將結構化的登錄區資料表至查詢、 探索及分析 Twitter 的資料使用 Azure HDInsight 轉換非結構化的 JSON 資料集。 若要深入瞭解，請參閱︰

- [快速入門 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [分析使用 HDInsight 航班延遲情況資料](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
