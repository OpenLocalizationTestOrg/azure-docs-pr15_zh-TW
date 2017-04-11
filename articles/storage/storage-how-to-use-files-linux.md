<properties
    pageTitle="如何使用 Linux Azure 檔案 |Microsoft Azure"
        description="建立 Azure 檔案共用此逐步教學課程中的雲端。 管理您的檔案共用的內容，並裝載執行 Linux] 或 [內部部署應用程式的支援中小企業 3.0 從 Azure 虛擬機器 (VM) 檔案共用。"
        services="storage"
        documentationCenter="na"
        authors="mine-msft"
        manager="aungoo"
        editor="tysonn" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="10/18/2016"
      ms.author="minet" />


# <a name="how-to-use-azure-file-storage-with-linux"></a>如何使用 Linux Azure 檔案儲存空間

## <a name="overview"></a>概觀

Azure 檔案儲存空間提供使用標準 SMB 通訊協定雲端中的檔案共用。 Azure 檔案，您可以移轉 Azure 檔案伺服器所依賴的企業應用程式。 Azure 中執行的應用程式從 Azure 虛擬機器執行 Linux，可以輕鬆地裝載檔案共用。 然後使用最新版本的檔案儲存空間，您可以也裝載檔案共用從內部部署應用程式的支援中小企業 3.0。

您可以建立使用[Azure 入口網站](https://portal.azure.com)、 Azure 儲存體 PowerShell cmdlet、 Azure 儲存體用戶端文件庫或 Azure 儲存體 REST API 的 Azure 檔案共用。 此外，因為檔案共用 SMB 共用，您可以透過標準檔案系統的 Api 進行存取。

檔案儲存為內建 Blob、 表格和佇列中的儲存空間，為相同的技術，所以檔案儲存空間提供可用性、 持續性、 延展性和 Azure 儲存平台內建的地理重複。 如需檔案儲存空間效能目標與限制的詳細資訊，請參閱[Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

現在已通常可使用的檔案儲存與支援中小企業 2.1 和中小企業 3.0。 其他檔案儲存空間的詳細資訊，請參閱[檔案服務 REST API](https://msdn.microsoft.com/library/azure/dn167006.aspx)。

>[AZURE.NOTE] Linux SMB 用戶端還不支援加密，以便從 Linux 裝載檔案共用仍需要用戶端作為檔案共用相同的 Azure 區域中。 不過，Linux 加密支援是 Linux 開發人員負責中小企業功能的藍圖。 支援加密在未來的 Linux 散佈無法裝載的 Azure 檔案共用區以及任何位置。

## <a name="video-using-azure-file-storage-with-linux"></a>影片︰ 使用 Linux Azure 檔案儲存空間

以下是將示範如何建立及使用 Azure 檔案共用上 Linux 的視訊。

> [AZURE.VIDEO azure-file-storage-with-linux]

## <a name="choose-a-linux-distribution-to-use"></a>選擇要使用 Linux 分配 ##

在建立 Linux 虛擬機器 Azure 中時，您可以指定 Linux 圖像支援從 Azure 圖像庫中小企業 2.1 或更新版本。 以下是建議 Linux 圖像的清單︰

- Ubuntu 伺服器 14.04 +
- RHEL 7 +
- CentOS 7 +
- Debian 8
- openSUSE 13.2 +
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 （Premium 圖像）

## <a name="mount-the-file-share"></a>裝載檔案共用 ##

若要從虛擬機器執行 Linux 裝載檔案共用，您可能需要安裝中小企業/CIFS 用戶端，如果您使用的的分配沒有內建的用戶端。 這是 [安裝一個選擇 cifs utils Ubuntu] 命令︰

    sudo apt-get install cifs-utils

接下來，您需要進行連接點 (mkdir mymountpoint)，然後發出裝載命令，就像這樣︰

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

您也可以新增您 /etc/fstab 裝載共用中的設定。

請注意以下 0777年，代表目錄/檔案的權限程式碼，讓所有使用者執行/讀/寫權限。 您可以與其他檔案的權限的程式碼後 Linux 檔案權限的文件加以取代。

也要保留檔案共用裝載重新啟動後，您可以在您 /etc/fstab 新增像下面的設定︰

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

例如，如果您建立 Azure VM 使用 Linux 圖像 Ubuntu 伺服器 15.04 （可從 Azure 圖像庫），您可以裝載為下列檔案︰

    azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

如果您使用 CentOS 7.1 時，您可以裝載為下列檔案︰

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

如果您使用開啟 SUSE 13.2 時，您可以裝載為下列檔案︰

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## <a name="manage-the-file-share"></a>管理檔案共用 ##

[Azure 入口網站](https://portal.azure.com)提供使用者介面管理 Azure 檔案儲存空間。 您可以從網頁瀏覽器中執行下列動作︰

- 上傳和下載的檔案，以便向您的檔案共用。
- 監控每個檔案共用實際的使用的方式。
- 調整檔案共用大小配額。
- 複製`net use`使用從 Windows 用戶端裝載您的檔案共用] 命令。

您也可以從 Linux 使用 Azure 跨平台的命令列介面 (Azure CLI)，來管理檔案共用。 Azure CLI 會提供一組開啟來源]，您可以使用 Azure 儲存體，包括檔案儲存空間的跨平台命令。 它會提供許多相同的功能中 Azure 入口網站以及豐富的資料存取功能。 如需範例，請參閱[使用 Azure CLI 與 Azure 儲存體](storage-azure-cli.md)。

## <a name="develop-with-file-storage"></a>開發的檔案儲存空間 ##

為開發人員]，您可以使用[Azure 儲存用戶端文件庫 java](https://github.com/azure/azure-storage-java)建置應用程式與檔案儲存空間。 程式碼範例，請參閱[如何使用從 Java 檔案儲存空間](storage-java-how-to-use-file-storage.md)。

您也可以使用[Azure 儲存用戶端文件庫的 Node.js](https://github.com/Azure/azure-storage-node)開發對檔案儲存空間。

## <a name="feedback-and-more-information"></a>意見反應及其他資訊 ##

Linux 使用者我們希望瞭解您的想法 ！

Azure 檔案儲存空間 Linux 使用者的群組會提供論壇，在您共用的意見反應，以及您評估採用 Linux 上的檔案儲存空間。 若要加入的使用者群組的電子郵件[Azure 檔案儲存空間 Linux 使用者](mailto:azurefileslinuxusers@microsoft.com)。

## <a name="next-steps"></a>後續步驟

請參閱這些連結，如需有關 Azure 檔案儲存空間。

### <a name="conceptual-articles-and-videos"></a>概念性文章和視訊

- [Azure 的檔案儲存空間︰ frictionless 雲端 for Windows 和 Linux 中小企業檔案系統](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [開始使用 windows Azure 檔案儲存空間](storage-dotnet-how-to-use-files.md)

### <a name="tooling-support-for-file-storage"></a>工具支援的檔案儲存空間

- [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
- [建立及管理檔案共用](storage-azure-cli.md#create-and-manage-file-shares)使用 Azure CLI

### <a name="reference"></a>參照

- [檔案服務 REST API 參考](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Azure 檔案疑難排解文章](storage-troubleshoot-file-connection-problems.md)

### <a name="blog-posts"></a>部落格文章

- [Azure 檔案儲存空間，現在推出](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
- [內 Azure 檔案儲存空間](https://azure.microsoft.com/blog/inside-azure-file-storage/)
- [介紹 Microsoft Azure 檔案的服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [保存連線到 Microsoft Azure 檔案](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
