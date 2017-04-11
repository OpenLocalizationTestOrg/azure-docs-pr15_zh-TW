<properties 
    pageTitle="資料流分析︰ 旋轉登入認證的輸入與輸出 |Microsoft Azure" 
    description="瞭解如何更新資料流分析的輸入與輸出的認證。"
    keywords="登入認證"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>旋轉的輸入與輸出資料流分析工作的登入認證

##<a name="abstract"></a>摘要
Azure 資料流分析今天不允許執行工作時取代上輸入輸出的憑證。

時繼續工作的最後一個輸出支援 Azure 資料流分析，我們想要共用的最小化停止之間的延隔時間和工作的開始和旋轉登入認證的整個程序。

##<a name="part-1---prepare-the-new-set-of-credentials"></a>第 1 部分-準備新的認證集︰
這是適用於下列輸入/輸出︰

* Blob 儲存體
* 事件集線器
* SQL 資料庫
* 資料表儲存體

其他的輸入/輸出，繼續第 2 部分。

###<a name="blob-storagetable-storage"></a>Blob 儲存體/資料表儲存體
1.  Azure 管理入口網站上，請移至儲存副檔名︰  
![graphic1][graphic1]
2.  找出您工作所使用的儲存空間，移至︰  
![graphic2][graphic2]
3.  按一下 [管理便捷鍵] 命令︰  
![graphic3][graphic3]
4.  之間存取主要和次要便捷鍵，**挑選其中一個不會用您的工作**。
5.  按下重新產生]:  
![graphic4][graphic4]
6.  複製新產生的金鑰︰  
![graphic5][graphic5]
7.  繼續第 2 部分。

###<a name="event-hubs"></a>事件集線器
1.  Azure 管理入口網站上，請移至 [服務匯流排副檔名為︰  
![graphic6][graphic6]
2.  找出服務匯流排命名空間使用您的工作，請移至︰  
![graphic7][graphic7]
3.  如果您的工作會使用共用的 access 原則服務匯流排命名空間，請跳至步驟 6  
4.  移至 [事件集線器] 索引標籤︰  
![graphic8][graphic8]
5.  找出 [事件] 中心上使用您的工作，請移至︰  
![graphic9][graphic9]
6.  移至 [設定] 索引標籤︰  
![graphic10][graphic10]
7.  在原則名稱] 下拉式清單，找出您工作所用的共用的存取原則︰  
![graphic11][graphic11]
8.  之間主索引鍵和 [次要鍵，**挑選其中一個不會用您的工作**。  
9.  按下重新產生]:  
![graphic12][graphic12]
10. 複製新產生的金鑰︰  
![graphic13][graphic13]
11. 繼續第 2 部分。  

###<a name="sql-database"></a>SQL 資料庫

>[AZURE.NOTE] 注意︰ 您必須連線至 SQL 資料庫服務。 我們會示範如何執行此動作 Azure 管理入口網站上使用的管理體驗，但您可以選擇使用 SQL Server Management Studio 例如一些用戶端工具以及。

1.  Azure 管理入口網站上，請移至 [SQL 資料庫副檔名為︰  
![graphic14][graphic14]
2.  找出您工作，然後**按一下 [在伺服器上**的連結，在同一行使用的 SQL 資料庫︰  
![graphic15][graphic15]
3.  按一下 [管理] 命令︰  
![graphic16][graphic16]
4.  輸入資料庫母片︰  
![graphic17][graphic17]
5.  輸入您使用者名稱和密碼，然後按一下記錄上︰  
![graphic18][graphic18]
6.  按一下 [新增查詢︰  
![graphic19][graphic19]
7.  輸入下列查詢 < login_name > 取代您的使用者名稱和取代<enterStrongPasswordHere>以新密碼︰  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  按一下 [執行]:  
![graphic20][graphic20]
9.  回到步驟 2 到此時間按一下資料庫︰  
![graphic21][graphic21]
10. 按一下 [管理] 命令︰  
![graphic22][graphic22]
11. 輸入您的使用者名稱，密碼，然後按一下 [登入︰  
![graphic23][graphic23]
12. 按一下 [新增查詢︰  
![graphic24][graphic24]
13. 輸入 < 電腦 > 取代，作為您想要識別此登入 （您可以提供相同的值，例如的 < login_name >，提供） 此資料庫的內容中的名稱，並以新的使用者名稱取代 < login_name > 下列查詢︰  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. 按一下 [執行]:  
![graphic25][graphic25]
15. 您現在應該具有相同的角色和權限具有您原始的使用者提供您的新使用者。
16. 繼續第 2 部分。

##<a name="part-2-stopping-the-stream-analytics-job"></a>第 2 部分︰ 停止串流分析工作
1.  Azure 管理入口網站上，請移至資料流分析副檔名為︰  
![graphic26][graphic26]
2.  找出您的工作，然後移至︰  
![graphic27][graphic27]
3.  移至 [輸入] 索引標籤或根據是否您要旋轉的認證，輸入或輸出上的 [輸出] 索引標籤。  
![graphic28][graphic28]
4.  按一下 [停止] 命令，並確認已停止工作︰  
![graphic29][graphic29]等候若要停止工作。
5.  找出要旋轉的認證，並將它輸入輸出︰  
![graphic30][graphic30]
6.  前進到第 3 部分。

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>第 3 部分︰ 編輯串流分析工作的認證

###<a name="blob-storagetable-storage"></a>Blob 儲存體/資料表儲存體
1.  找出的儲存空間帳戶金鑰] 欄位，並貼上您的新產生的金鑰︰  
![graphic31][graphic31]
2.  按一下 [儲存] 命令，然後確認儲存您的變更︰  
![graphic32][graphic32]
3.  儲存您的變更時，將會自動啟動連線測試確認就是已成功傳遞。
4.  前往第 4 部分。

###<a name="event-hubs"></a>事件集線器
1.  尋找事件中心原則索引鍵欄位，並貼上您的新產生的金鑰︰  
![graphic33][graphic33]
2.  按一下 [儲存] 命令，然後確認儲存您的變更︰  
![graphic34][graphic34]
3.  儲存您的變更時，將會自動啟動連線測試確定已成功傳遞。
4.  前往第 4 部分。

###<a name="power-bi"></a>Power BI
1.  按一下 [更新授權︰  
* ![graphic35][graphic35]
* 您會收到下列確認︰  
* ![graphic36][graphic36]
2.  按一下 [儲存] 命令，然後確認儲存您的變更︰  
![graphic37][graphic37]
3.  當您將您的變更，會自動啟動連線測試，請確定它已成功傳遞。
4.  前往第 4 部分。

###<a name="sql-database"></a>SQL 資料庫
1.  尋找 [使用者名稱和密碼] 欄位，並貼到其中的新建立的認證設定︰  
![graphic38][graphic38]
2.  按一下 [儲存] 命令，然後確認儲存您的變更︰  
![graphic39][graphic39]
3.  儲存您的變更時，將會自動啟動連線測試確定已成功傳遞。  
4.  前往第 4 部分。

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>第 4 部分︰ 將您的工作自上次停止的時間
1.  瀏覽離開輸出入︰  
![graphic40][graphic40]
2.  按一下 [開始] 命令︰  
![graphic41][graphic41]
3.  挑選 [上次停止的時間，然後按一下 [確定]:  
 ![graphic42][graphic42]
4.  前往第 5 部分。  

##<a name="part-5-removing-the-old-set-of-credentials"></a>第 5 部分︰ 移除舊的認證集
這是適用於下列輸入/輸出︰
* Blob 儲存體
* 事件集線器
* SQL 資料庫
* 資料表儲存體

###<a name="blob-storagetable-storage"></a>Blob 儲存體/資料表儲存體
第 1 部分重複先前已由您的工作用來更新現在未使用過的便捷鍵便捷鍵。

###<a name="event-hubs"></a>事件集線器
第 1 部分重複先前已由您的工作用來更新現在未使用過的索引鍵的索引鍵。

###<a name="sql-database"></a>SQL 資料庫
1.  請返回 [查詢] 視窗的組件 1 步驟 7，然後輸入下列查詢，取代 < previous_login_name > 先前使用您的工作的使用者名稱︰  
`DROP LOGIN <previous_login_name>`  
2.  按一下 [執行]:  
    ![graphic43][graphic43]  

您應該會收到下列確認︰ 

    Command(s) completed successfully.

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
