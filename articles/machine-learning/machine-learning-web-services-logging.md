<properties 
    pageTitle="電腦學習 web 服務的記錄 |Microsoft Azure" 
    description="瞭解如何啟用電腦學習 web 服務的記錄。 記錄可以提供協助疑難排解 Api 的其他資訊。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>啟用電腦學習 web 服務的記錄  

這份文件提供的傳統的 Web 服務的記錄功能的資訊。 Web 服務中的啟用記錄提供只錯誤碼與訊息，可協助您解決電腦學習 Api 來電以外的其他資訊。  

若要啟用 Azure 傳統入口網站中的記錄功能 Web 服務︰   

1.  登入[Azure 傳統入口網站](https://manage.windowsazure.com/)
2.  在左側的功能] 欄中，按一下 [**電腦學習**]。
3.  按一下 [工作區，然後**WEB 服務**]。
4.  在 Web 服務] 清單中，按一下 [Web 服務的名稱。
5.  在 [結束點] 清單中，按一下 [結束點名稱]。
6.  按一下 [**設定**]。
7.  設定**診斷追蹤層級***錯誤*」 或 「*所有*]，然後按一下 [**儲存**]。

若要啟用 Azure 機器學習 Web 服務入口網站中的記錄。

1. 登入[Azure 機器學習 Web 服務](https://services.azureml.net)入口網站。
2. 按一下 [傳統的 Web 服務]。
3.  在 Web 服務] 清單中，按一下 [Web 服務的名稱。
4.  在 [結束點] 清單中，按一下 [結束點名稱]。
5.  按一下 [**設定**]。
6.  設定**記錄***錯誤*」 或 「*所有*]，然後按一下 [**儲存**]。

## <a name="the-effects-of-enabling-logging"></a>效果] 的 [啟用記錄功能

啟用記錄時，所有的診斷與從所選的端點的錯誤登 Azure 儲存體帳戶連結與使用者的工作區。 您可以看到此 Azure 傳統入口網站的工作區的儀表板檢視 （[快速瀏覽] 區段底部） 中的儲存空間帳戶。  

可以使用任何一種可用的工具來探索 Azure 儲存體帳戶來檢視記錄檔。 最簡單的方法可能會直接瀏覽至 Azure 傳統入口網站中儲存的帳戶，然後按一下 [**容器**。 然後，您會看到名為**毫升診斷**容器。 此容器會保留此儲存帳戶相關聯的所有工作區的所有 web 服務端點的診斷資訊。 
 
## <a name="log-blob-detail-information"></a>記錄 blob 的詳細資訊

每個 blob 容器中的保留的診斷資訊，，完全下列其中一項動作︰

-   執行批次執行方法  
-   執行要求回應方法  
-   要求回應容器的初始設定
  
每個 blob 的名稱具有前置詞為下列格式︰ 

{工作區識別碼}-{Web 服務識別碼}-{端點識別碼} {類型記錄} /  

其中的記錄類型是其中一個下列值︰  

- 批次  
- 成績/要求  
- 成績/初始化  