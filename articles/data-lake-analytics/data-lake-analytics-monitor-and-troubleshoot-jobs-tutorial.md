<properties 
   pageTitle="疑難排解使用 Azure 入口網站的 Azure 資料湖分析工作 |Azure" 
   description="瞭解如何使用 Azure 入口網站以疑難排解資料湖分析工作。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>疑難排解 Azure 資料湖分析工作使用 Azure 入口網站

瞭解如何使用 Azure 入口網站以疑難排解資料湖分析工作。

在本教學課程中，您會安裝缺少的來源檔案時發生問題，並使用 Azure 入口網站問題進行疑難排解。

**必要條件**

本教學課程之前，您必須具備下列項目︰

- **基本知識資料湖分析工作程序**。 請參閱[開始使用 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md)。
- **資料湖分析帳戶**。 請參閱[開始使用 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md#create-adl-analytics-account)。
- **複製到預設資料湖存放帳戶的範例資料**。  請參閱[準備來源資料](data-lake-analytics-get-started-portal.md#prepare-source-data)

##<a name="submit-a-data-lake-analytics-job"></a>提交資料湖分析工作

現在您將建立 U SQL 作業以不正確的來源檔案名稱。  

**送出工作**

1. 從 Azure 入口網站中，按一下 [ **Microsoft Azure**中的左上角。
2. 按一下 [使用您的資料湖分析帳戶名稱] 方塊。  建立帳戶時，它已釘選以下。
如果帳戶不那里 pin，請參閱[開啟分析帳戶從入口網站](data-lake-analytics-manage-use-portal.md#access-adla-account)。
3. 從上方的功能表中按一下 [**新工作**]。
4. 輸入作業名稱，然後下列 U SQL 指令碼︰

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    定義的指令碼來源檔案是**/Samples/Data/SearchLog.tsv1**，它應該是**/Samples/Data/SearchLog.tsv**的位置。
     
5. 按一下 [從上方的 [**送出工作**]。 隨後便會開啟新的 [工作詳細資料] 窗格。 在標題列，其會顯示工作狀態。 需要幾分鐘，才能完成。 您可以按一下 [取得最新狀態的 [**重新整理**]。
6. 請等待工作狀態會變更為 [**失敗**。  如果工作是**成功**，是因為您沒有移除 /Samples 資料夾。 教學課程的開頭，請參閱 [**必要**] 區段。

您可能會好奇-為什麼要花這麼久的時間的小型工作。  請記住資料湖分析設計用來處理大型資料。  它出色處理大量的資料使用的分散式的系統時。

現在就讓我們假設您送出工作，並關閉入口網站。  在下一步] 區段中，您將學習如何疑難排解工作。


## <a name="troubleshoot-the-job"></a>疑難排解工作

在最後一個區段中，您已送出的工作，然後工作失敗。  

**若要查看所有的工作**

1. 從 Azure 入口網站中，按一下 [ **Microsoft Azure**中的左上角。
2. 按一下 [使用您的資料湖分析帳戶名稱] 方塊。  摘要工作會顯示在 [**工作管理**] 方塊。

    ![Azure 資料湖分析工作管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    工作管理可讓您將工作狀態的規則。 請注意失敗的工作。
   
3. 按一下以查看工作的**工作管理**] 磚。 在**執行**、**佇列**、 和**結束**分類的工作。 您應該會看到您失敗的工作，在 [**結束**] 區段。 應該就能在清單中的第一個。 如果您有許多工作，您可以按一下 [**篩選器**可協助您找出工作。

    ![Azure 資料湖分析來篩選工作](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. 按一下 [從清單中的新刀開啟工作詳細資料失敗的工作︰

    ![Azure 資料湖分析失敗的工作](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    請注意**重新送出**] 按鈕。 修正問題之後，您可以重新送出工作。

5. 按一下 [醒目提示的組件從先前的螢幕擷取畫面，開啟錯誤詳細資料]。  您應該看到類似︰

    ![Azure 資料湖分析失敗工作詳細資料](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    它會告訴您找不到 [來源] 資料夾。
    
6. 按一下 [**複製指令碼**。
7. 更新下列**從**路徑︰

    「 / Samples/Data/SearchLog.tsv 」

8. 按一下 [**送出工作**。


##<a name="see-also"></a>另請參閱

- [Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 PowerShell 的 Azure Azure 資料湖分析快速入門](data-lake-analytics-get-started-powershell.md)
- [使用 Visual Studio U SQL Azure 資料湖分析與快速入門](data-lake-analytics-u-sql-get-started.md)
- [管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)





