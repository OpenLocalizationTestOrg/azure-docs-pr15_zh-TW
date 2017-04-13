<properties
    pageTitle="如何使用 Azure 儲存空間的 SQL Server 備份和還原 |Microsoft Azure"
    description="瞭解如何將備份 SQL Server Azure 儲存體。 說明優點 SQL 資料庫備份到 Azure 儲存體。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>使用 SQL Server 備份與還原 Azure 儲存體

## <a name="overview"></a>概觀

開始使用 SQL Server 2012 SP1 CU2，您可以立即寫入 SQL Server 備份直接 Azure Blob 儲存體服務。 您可以使用這項功能，從內部部署的 SQL Server 資料庫的 SQL Server 資料庫 Azure 虛擬機器中的 Azure Blob 服務來備份及還原。 備份雲端提供可用性、 無限地理複製進去，且方便移轉的資料在雲端的優點。 您可以使用 TRANSACT-SQL 或 SMO 發出備份或還原陳述式。

SQL Server 2016 會介紹新功能。若要執行幾乎瞬間完成的效能的備份與還原相當快速，您可以使用[檔案快照備份](http://msdn.microsoft.com/library/mt169363.aspx)。

本主題說明為什麼您可以選擇使用 Azure 儲存空間的 SQL 備份，然後再說明 [的元件。 若要存取的逐步解說和開始您的 SQL Server 備份搭配使用這項服務的詳細資訊，您可以使用所提供的文件結尾的資源。

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>使用 Azure Blob 服務的 SQL Server 備份的優點

有數種備份 SQL Server 時，也會遇到的挑戰。 這些挑戰包含儲存管理] 中，儲存失敗，進去和硬體設定的存取權的風險。 許多這些問題的收件者使用的 SQL Server 備份 Azure Blob 儲存區服務。 請考慮下列優點︰

- **使用容易**︰ 中 Azure blob 儲存備份可方便、 彈性，並輕鬆地存取離線] 選項。 建立進去的 SQL Server 備份是，只要修改現有使用**備份到 URL**語法指令碼/工作。 進去應該是遠從生產資料庫的位置以防止單一損壞可能會影響兩個離和生產資料庫的位置。 選擇[地理複寫您 Azure blob](../storage/storage-redundancy.md)，您有了額外的保護，可能會影響整個區域損毀事件。
- **備份封存**︰ Azure Blob 儲存體服務可提供較好的選擇封存備份經常使用的膠帶貼上選項。 離線的功能和量值來保護媒體的實體交通時，可能需要膠帶貼上儲存空間。 Azure Blob 儲存體中儲存備份提供立即、 可用性和長期封存選項。
- **受管理的硬體**︰ 有無負荷的硬體管理與 Azure 服務。 Azure 服務管理硬體，並提供地理複寫重複性和保護硬體失敗。
- **不受限制的儲存空間**︰ 啟用 Azure blob 直接備份，您可以存取幾乎無限儲存空間。 或者，備份到 Azure 虛擬機器磁碟有根據電腦的大小限制。 沒有限制可附加至 Azure 虛擬機器的備份磁碟數目。 這項限制為 16 磁碟超大型執行個體和較少的較小的執行個體。
- **備份可用性**︰ 備份儲存在 Azure blob 可從任何位置，然後在任何時間，並輕鬆地可存取的內部部署的 SQL Server 或另一個 SQL Server 執行中 Azure 虛擬機器，而不需要使用資料庫附加/卸離或下載和附加 VHD 還原。
- **成本**︰ 支付僅適用於服務，可使用。 可以是效益作為離和備份封存的選項。 請參閱[Azure 價格計算機](http://go.microsoft.com/fwlink/?LinkId=277060 "價格計算機")，和[Azure 價格文章](http://go.microsoft.com/fwlink/?LinkId=277059 "價格文章︰")如需詳細資訊。
- **儲存快照**︰ 中 Azure blob 儲存資料庫檔案，然後您使用的 SQL Server 2016，您可以使用[檔案快照備份](http://msdn.microsoft.com/library/mt169363.aspx)中執行幾乎瞬間完成的效能的備份與還原相當快速。

如需詳細資訊，請參閱[SQL Server 備份與還原 Azure Blob 儲存體服務](http://go.microsoft.com/fwlink/?LinkId=271617)。

下列兩個章節會介紹 Azure Blob 儲存體服務，包括所需的 SQL Server 元件。 請務必瞭解元件和其互動，才能成功地使用備份與還原 Azure Blob 儲存體服務。

## <a name="azure-blob-storage-service-components"></a>Azure Blob 儲存體服務元件

備份 Azure Blob 儲存體服務時，會使用下列 Azure 元件。

| 元件               | 描述                          |
|---------------------|-------------------------------|
| **儲存帳戶** | 儲存帳戶是為所有的儲存空間服務的起點。 若要存取 Azure Blob 儲存體服務，請先建立 Azure 儲存體帳戶。 如需有關 Azure Blob 儲存體服務的詳細資訊，請參閱[如何使用 Azure Blob 儲存服務](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **容器** | 容器提供二進位大型物件，一組的群組，並可儲存 Blob 數量。 若要撰寫 SQL Server 備份 Azure Blob 的服務，您必須至少建立的根容器。 |
| **Blob** | 檔案中有任何類型與大小。 二進位大型物件會定址使用下列的 URL 格式︰ **https://[storage account].blob.core.windows.net/[container]/[blob]**。 更多頁面二進位大型物件的詳細資訊，請參閱[瞭解封鎖和頁面二進位大型物件](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server 元件

備份 Azure Blob 儲存體服務時，會使用下列的 SQL Server 元件。

| 元件               | 描述                          |
|---------------------|-------------------------------|
| **URL** | URL 指定唯一的備份檔案統一資源識別元 (URI)。 URL 用來提供的位置和 SQL Server 備份檔案的名稱。 URL 必須指向實際 blob，而不只是容器。 如果 blob 不存在，則會建立。 如果現有的 blob 指定備份失敗，除非 > 指定的格式選項。 以下是您想要指定備份命令中的 URL 的範例︰ **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**。 HTTPS 是建議，但並非必要。 |
| **認證** | 才能連線和驗證 Azure Blob 儲存體服務的資訊會儲存認證。  為了讓撰寫 Azure Blob 或還原的備份，從 SQL Server，必須先建立 SQL Server 認證。 如需詳細資訊，請參閱[SQL Server 認證](https://msdn.microsoft.com/library/ms189522.aspx)。 |

> [AZURE.NOTE] 如果您選擇複製並將備份檔案上傳至 Azure Blob 儲存服務，您必須使用頁面 blob 類型，為您儲存空間的選項如果您打算使用此檔案還原作業即可。 還原區塊 blob 類型錯誤會失敗。

## <a name="next-steps"></a>後續步驟

1. 如果您還沒有其中一個，請建立 Azure 帳戶。 如果您正在評估 Azure，請考慮[免費試用版](https://azure.microsoft.com/free/)。

1. 然後移到其中一個引導您建立儲存帳戶，並執行還原下列教學課程。

    - **SQL Server 2014**:[教學課程︰ SQL Server 2014 備份與還原到 Microsoft Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)。
    - **SQL Server 2016**:[教學課程︰ 使用 SQL Server 2016 資料庫中的 Microsoft Azure Blob 儲存體服務](https://msdn.microsoft.com/library/dn466438.aspx)

1. 檢閱啟動[SQL Server 備份](https://msdn.microsoft.com/library/jj919148.aspx)與還原與 Microsoft Azure Blob 儲存體服務的其他文件。

如果您有任何問題，請檢閱[SQL Server 備份 URL 的最佳作法和疑難排解](https://msdn.microsoft.com/library/jj919149.aspx)主題。

用於其他 SQL Server 備份和還原選項，請參閱[備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)。
