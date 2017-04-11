<properties 
   pageTitle="開始使用 Azure 資料湖分析使用 PowerShell 的 Azure |Azure" 
   description="瞭解如何使用 PowerShell 的 Azure 建立資料湖存放帳戶，請建立使用 U SQL 資料湖分析工作，並提交工作。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>教學課程︰ 開始使用 Azure PowerShell 的 Azure 資料湖狀況分析

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

瞭解如何使用 PowerShell 的 Azure 建立 Azure 資料湖分析帳戶、 在[U SQL](data-lake-analytics-u-sql-get-started.md)中，定義資料湖分析工作並提交工作資料湖分析的帳戶。 如需有關資料湖分析的詳細資訊，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

在本教學課程中，您會開發工作的讀取分隔值 (TSV) 檔案] 索引標籤，並將其轉換成逗點分隔值 (CSV) 檔案。 若要移到同一個教學課程中使用其他支援的工具，請按一下上方的這一節的索引標籤。

##<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- **使用 PowerShell 的 Azure 工作站**。 瞭解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。
    
##<a name="create-data-lake-analytics-account"></a>建立資料湖分析帳戶

您必須有資料湖分析帳戶，才能執行任何工作。 若要建立資料湖 Analytics 帳戶，您必須指定下列項目︰

- **Azure 資源群組**︰ 必須 Azure 資源群組內建立資料湖 Analytics 帳戶。 [Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)可讓您使用的應用程式群組中的資源。 您可以部署、 更新或刪除的所有資源在單一、 協同作業應用程式。  

    若要列舉資源群組中您的訂閱︰
    
        Get-AzureRmResourceGroup
    
    若要建立新的資源群組︰

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **資料湖分析帳戶名稱**
- **位置**︰ 其中一個支援資料湖分析 Azure 資料中心。
- **預設資料湖帳戶**︰ 每個資料湖分析帳戶具有預設資料湖帳戶。

    若要建立新的資料湖帳戶︰

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] 小寫字母和數字，必須只包含資料湖帳戶名稱。



**若要建立資料湖分析帳戶**

1. 從 Windows 工作站開啟 PowerShell ise [以系統。
2. 執行下列指令碼︰

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>上傳至資料湖的資料

在本教學課程中，您會處理某些搜尋記錄檔。  搜尋記錄可以儲存在資料湖市集或 Azure Blob 儲存體。 

範例搜尋記錄檔複製到公用 Azure Blob 容器。 若要將檔案下載到您的工作站，使用下列 PowerShell 指令碼，然後將檔案上傳至您的資料湖分析帳戶的預設資料湖存放帳戶。

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

下列 PowerShell 指令碼會顯示如何取得資料湖分析帳戶的預設資料湖存放名稱︰


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] Azure 入口網站提供的使用者介面，將範例資料檔案複製到預設資料湖存放帳戶。 如需相關指示，請參閱[開始使用 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account)。

資料湖分析也可以存取 Azure Blob 儲存體。  將資料的上傳至 Azure Blob 儲存體，請參閱[使用 Azure PowerShell 的 Azure 儲存體](../storage/storage-powershell-guide-full.md)。

##<a name="submit-data-lake-analytics-jobs"></a>提交資料湖分析工作

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

1. 從 Windows 工作站開啟 PowerShell ise [以系統。
2. 執行下列指令碼︰

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    在 [指令碼 U SQL 指令碼檔案會儲存在 c:\tutorials\data-lake-analytics\copyFile.usql。 適當地更新檔案路徑。
 
完成工作後，清單的檔案]，使用下列 cmdlet，下載檔案︰
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>另請參閱

- 若要查看同一個教學課程中使用其他工具，請按一下 [上方的頁面] 索引標籤選擇器]。
- 若要查看較複雜的查詢，請參閱[使用 Azure 資料湖分析分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U SQL 應用程式，請參閱[使用資料湖工具的 Visual Studio 開發的 U SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要瞭解 U SQL，請參閱[開始使用 Azure 資料湖分析 U SQL 語言](data-lake-analytics-u-sql-get-started.md)。
- 管理工作，請參閱[管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析的概觀，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

