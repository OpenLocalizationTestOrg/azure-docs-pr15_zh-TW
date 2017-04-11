<properties 
   pageTitle="開始使用 Azure 資料湖分析使用 Azure 命令列介面 |Microsoft Azure" 
   description="瞭解如何建立資料湖存放帳戶，請建立使用 U SQL 資料湖分析工作使用 Azure 命令列介面並送出工作。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>教學課程︰ 開始使用 Azure 資料湖分析使用 Azure 命令列介面 (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


瞭解如何使用 Azure CLI 建立 Azure 資料湖分析帳戶、 在[U SQL](data-lake-analytics-u-sql-get-started.md)中，定義資料湖分析工作並提交工作資料湖分析帳戶。 如需有關資料湖分析的詳細資訊，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

在本教學課程中，您會開發工作的讀取分隔值 (TSV) 檔案] 索引標籤，並將其轉換成逗點分隔值 (CSV) 檔案。 若要移到同一個教學課程中使用其他支援的工具，請按一下上方的這一節的索引標籤。

##<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure CLI**。 請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md)。
    - 下載並安裝**測試版** [Azure CLI 工具](https://github.com/MicrosoftBigData/AzureDataLake/releases)，才能完成此示範。
- **驗證**，使用下列命令︰

        azure login
    如需有關使用公司或學校帳戶驗證的詳細資訊，請參閱[連線至 Azure CLI 從 Azure 訂閱](../xplat-cli-connect.md)。
- **切換至 Azure 資源管理員模式**，請使用下列命令︰

        azure config mode arm
        
## <a name="create-data-lake-analytics-account"></a>建立資料湖分析帳戶

您必須有資料湖分析帳戶，才能執行任何工作。 若要建立資料湖分析帳戶，您必須指定下列項目︰

- **Azure 資源群組**︰ 必須 Azure 資源群組內建立資料湖分析帳戶。 [Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)可讓您使用的應用程式群組中的資源。 您可以部署、 更新或刪除的所有資源在單一、 協同作業應用程式。  

    若要列舉資源群組中您的訂閱︰
    
        azure group list 
    
    若要建立新的資源群組︰

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **資料湖分析帳戶名稱**
- **位置**︰ 其中一個支援資料湖分析 Azure 資料中心。
- **預設資料湖帳戶**︰ 每個資料湖分析帳戶具有預設資料湖帳戶。

    清單的現有資料湖帳戶︰
    
        azure datalake store account list

    若要建立新的資料湖帳戶︰

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] 小寫字母和數字，必須只包含資料湖帳戶名稱。



**若要建立資料湖分析帳戶**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![資料湖分析顯示的帳戶](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] 小寫字母和數字，必須只包含資料湖分析帳戶名稱。


## <a name="upload-data-to-data-lake-store"></a>上傳至資料湖存放區的資料

在本教學課程中，您會處理某些搜尋記錄檔。  搜尋記錄可以儲存在資料湖市集或 Azure Blob 儲存體。 

Azure 入口網站提供使用者介面複製到預設資料湖帳戶，其中包含搜尋記錄檔中的一些範例資料檔案。 請參閱[準備來源資料](data-lake-analytics-get-started-portal.md#prepare-source-data)上傳至預設資料湖存放帳戶的資料。

若要上傳檔案使用 cli，使用下列命令︰

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

資料湖分析也可以存取 Azure Blob 儲存體。  將資料的上傳至 Azure Blob 儲存體，請參閱[使用 Azure CLI 與 Azure 儲存體](../storage/storage-azure-cli.md)。

## <a name="submit-data-lake-analytics-jobs"></a>提交資料湖分析工作

資料湖分析工作語言撰寫的 U SQL。 若要進一步瞭解 U SQL，請參閱[開始使用 U SQL 語言](data-lake-analytics-u-sql-get-started.md)和[U SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**若要建立資料湖分析工作指令碼**

- 使用下列 U SQL 指令碼，建立文字檔案並將文字檔儲存到您的工作站︰

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    這個 U SQL 指令碼會讀取使用**Extractors.Tsv()**的來源資料檔案，，然後建立使用**Outputters.Csv()**csv 檔案。 
    
    請勿修改兩個路徑，除非您將來源檔案複製到不同的位置。  如果不存在，資料湖分析會建立輸出的資料夾。
    
    會更容易使用的檔案儲存在預設資料湖帳戶的相對路徑。 您也可以使用絕對的路徑。  例如 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    若要存取連結的儲存帳戶檔案，您必須使用絕對的路徑。  儲存在連結的 Azure 儲存體帳戶檔案的語法是︰
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] 目前不支援 azure Blob 容器公用 blob 或公用容器存取權限。      

    
**送出工作**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
下列命令可列出工作，取得工作詳細資料，並取消工作︰

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

完成工作後，清單的檔案]，使用下列 cmdlet，下載檔案︰
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>另請參閱

- 若要查看同一個教學課程中使用其他工具，請按一下 [上方的頁面] 索引標籤選擇器]。
- 若要查看較複雜的查詢，請參閱[使用 Azure 資料湖分析分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U SQL 應用程式，請參閱[使用資料湖工具的 Visual Studio 開發的 U SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要瞭解 U SQL，請參閱[開始使用 Azure 資料湖分析 U SQL 語言](data-lake-analytics-u-sql-get-started.md)。
- 管理工作，請參閱[管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析的概觀，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

