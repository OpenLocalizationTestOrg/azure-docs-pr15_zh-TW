<properties
   pageTitle="開始使用資料湖存放使用跨平台的命令列介面 |Microsoft Azure"
   description="建立資料湖存放帳戶，並執行基本作業使用 Azure 跨平台命令列"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>開始使用 Azure 資料湖存放區使用 Azure 命令列

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

瞭解如何使用 Azure 命令列介面以建立 Azure 資料湖存放帳戶，並執行基本作業，例如建立資料夾上, 傳和下載資料檔案，刪除您的帳戶、 等等。如需有關資料湖存放區的詳細資訊，請參閱[資料湖存放概觀](data-lake-store-overview.md)。

Azure CLI Node.js 中實作。 它可以使用任何支援 Node.js，包括 Windows、 Mac 和 Linux 的平台上。 Azure CLI 可開啟來源。 程式碼會在<a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>管理 GitHub 中。 本文涵蓋只將 Azure CLI 使用資料湖存放區。 如何使用 Azure CLI 的一般指南，請參閱[如何使用 Azure CLI] [azure-command-line-tools]。


##<a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- **Azure CLI** ，請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md)安裝與設定的資訊。 請確定您重新啟動您的電腦上安裝 CLI 之後。

## <a name="authentication"></a>驗證

本文會使用資料湖存放位置您使用者身分登入使用者的簡單的驗證方法。 帳戶與檔案系統然後由登入的使用者的存取層級的資料湖存放存取層級。 然而，有其他方法也驗證資料湖存放區，也就是**使用者驗證**或**服務-服務驗證**。 如需相關指示與驗證方法的詳細資訊，請參閱[驗證資料湖存放區使用 Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)。

##<a name="login-to-your-azure-subscription"></a>登入您 Azure 的訂閱

1. 請依照在[連線至 Azure 訂閱從 Azure 命令列介面 (Azure CLI)](../xplat-cli-connect.md)記錄的步驟，並連線到您的訂閱使用`azure login`方法。

2. 清單與您的帳戶使用相關聯的訂閱`azure account list`] 命令。

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    上述輸出**Azure-sub-1**目前已啟用，而其他訂閱是**Azure-sub-2**。 

3. 選取您想要使用底下的訂閱]。 如果您想要使用 Azure-sub-2 訂閱下，使用`azure account set`] 命令。

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>建立 Azure 資料湖存放區帳戶

開啟命令提示字元、 命令介面或終端機工作階段，並執行下列命令。

2. 切換至 Azure 資源管理員模式使用下列命令︰

        azure config mode arm


5. 建立新的資源群組。 在以下命令，提供您想要使用的參數值。

        azure group create <resourceGroup> <location>

    如果地點名稱包含空格，請將它放在雙引號中。 比方說 「 東亞美國 2 」。

5. 建立資料湖存放帳戶。

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>建立您的資料湖存放區中的資料夾

您可以建立資料夾，您可用於管理及儲存資料的 Azure 資料湖存放帳號] 之下。 您可以使用下列命令，建立一個名為 「 mynewfolder 」 資料湖存放區的根資料夾。

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

例如︰

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>上傳至您的資料湖存放區的資料

直接在根層級的資料湖存放區或您帳戶中建立的資料夾，您可以上傳您的資料。 下列程式碼片段示範如何上傳到您在前一節中建立資料夾 (**mynewfolder**) 的一些範例資料。

如果您要尋找的上傳的一些範例資料，您可以取得[Azure 資料湖給存放庫](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)**政策救護車資料**的資料夾。 下載檔案並將其儲存在您的電腦，例如 C:\sampledata 上的本機目錄\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

例如︰

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>清單資料湖存放區中的檔案

使用下列命令] 清單中的資料湖存放帳戶中的檔案。

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

例如︰

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

這的輸出應如下︰

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>重新命名、 下載及刪除資料湖存放區中的資料

* **若要重新命名檔案**，請使用下列命令︰

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    例如︰

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **若要下載的檔案**，請使用下列命令。 請確定您已經指定的目的路徑存在。

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    例如︰

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **若要刪除檔案**，請使用下列命令︰

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    例如︰

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    出現提示時，請輸入**Y**刪除的項目。

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>檢視的存取控制清單資料湖存放區中的資料夾

使用下列命令來檢視 Acl 資料湖存放區資料夾。 在目前的版本，可以設定 Acl 僅在資料湖存放區的根目錄。 因此，下列路徑參數一定會根 （/）。

    azure datalake store permissions show <dataLakeStoreName> <path>

例如︰

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>刪除您的資料湖存放區帳戶

若要刪除資料湖存放帳戶使用下列命令。

    azure datalake store account delete <dataLakeStoreAccountName>

例如︰

    azure datalake store account delete mynewdatalakestore

出現提示時，請輸入**Y**刪除帳戶。


## <a name="next-steps"></a>後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [使用資料湖存放 Azure 資料湖狀況分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [使用資料湖存放 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
