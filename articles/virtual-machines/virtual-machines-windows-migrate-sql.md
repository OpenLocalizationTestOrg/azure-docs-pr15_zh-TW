<properties
    pageTitle="將 SQL Server 資料庫移轉至虛擬機器上的 SQL Server |Microsoft Azure"
    description="了解如何在 Azure 虛擬機器移轉至 SQL Server 的內部部署使用者資料庫。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sabotta"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="carlasab"/>


# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>移轉至在 Azure 虛擬機器中的 SQL Server 的 SQL Server 資料庫

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]資源管理員模型。


有幾種方法來將內部部署 SQL Server 使用者資料庫移轉至 SQL Server 在 Azure 虛擬機器。 本文將簡短討論各種方法、 建議各種案例的最佳方法並包含透過**部署至 Microsoft Azure 虛擬機器的 SQL Server 資料庫**精靈引導您[教學課程](#azure-vm-deployment-wizard-tutorial)。 

使用**SQL Server 資料庫 Microsoft Azure 虛擬機器來部署**精靈[教學課程](#azure-vm-deployment-wizard-tutorial)中所述的方法是傳統部署模型。 

## <a name="what-are-the-primary-migration-methods"></a>什麼是主要的移轉方法？

主要的移轉方法是：

- 使用部署到 Microsoft Azure 虛擬機器] 精靈的 SQL Server 資料庫
- 執行使用壓縮的內部部署備份，然後手動將備份檔案複製到 Azure 虛擬機器
- 執行與 URL 的 URL 及 Azure 虛擬機器的還原備份
- 卸離並再將資料與記錄檔案複製到 Azure blob 儲存並再附加至 Azure 虛擬機器中的 SQL Server 與 URL
- 將內部部署實體機器轉換成 HYPER-V VHD、 上傳至 Azure Blob 存放區，然後將部署為新虛擬機器使用上傳 VHD
- 使用 Windows 匯入/匯出服務運送硬碟
- 如果您有 AlwaysOn 部署內部部署、 使用[新增 Azure 複本精靈](virtual-machines-windows-classic-sql-onprem-availability.md)在 Azure，然後容錯移轉]，指向 Azure 資料庫執行個體的使用者建立的複本
- 使用 SQL Server[交易式複寫](https://msdn.microsoft.com/library/ms151176.aspx)Azure SQL Server 執行個體設定為訂閱者，然後停用使用者指向 Azure 資料庫執行個體的複寫



## <a name="choosing-your-migration-method"></a>選擇您的移轉方法

為指定的資料傳輸效能，資料庫檔案移轉到使用壓縮的備份檔案 Azure 虛擬機器通常是最佳方法。 這是[部署到 Microsoft Azure 虛擬機器] 精靈的 SQL Server 資料庫](#azure-vm-deployment-wizard-tutorial)所使用的方法。 此精靈已移轉的內部使用者的資料庫有執行在 SQL Server 2005 或 SQL Server 到 2014 年以上或更大時壓縮的資料庫備份檔為小於 1 TB 的建議的方法。

若要在資料庫移轉程序期間大幅縮短停機時間，使用 AlwaysOn 選項或交易式複寫選項。

如果您不可以使用上述方法，以手動方式移轉您的資料庫。 使用此方法，將通常開始後面的複本的資料庫備份資料庫備份至 Azure，然後執行資料庫還原。 您可以也將自己的資料庫檔案複製到 Azure 並再附加。 依據您可以完成資料庫移轉至 Azure 虛擬機器的這個手動程序有數種方法。

> [AZURE.NOTE] 當您升級至 SQL Server 2014 或 SQL Server 2016 較舊版本的 SQL Server 時，則應考慮是否需要變更。 我們建議您解決所有相依的功能不支援新版本的 SQL Server 移轉專案的一部分。 支援的版本與案例的詳細資訊，請參閱[升級至 SQL Server](https://msdn.microsoft.com/library/bb677622.aspx)。

下表列出每個主要的移轉方法，並討論每個方法使用時最適當。

| 方法  | 來源資料庫版本  |  目的資料庫版本 | 來源資料庫備份大小限制  | 附註  |
|---|---|---|---|---|
| [使用部署到 Microsoft Azure 虛擬機器] 精靈的 SQL Server 資料庫](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 或更大 | SQL Server 2014 或更大 | < 1 TB  | 最快和最簡單的方法中使用盡可能移轉至新的或現有 SQL Server 執行個體在 Azure 虛擬機器 | 
| [使用 [新增 Azure 複本精靈]](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 或更大 | SQL Server 2012 或更大 | [Azure 虛擬機器儲存限制](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | 最小化的停機時間，當您有 AlwaysOn 內部部署使用 |
| [使用 SQL Server 交易式複寫](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 或更大 | SQL Server 2005 或更大 | [Azure 虛擬機器儲存限制](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | 當您需要降低停機時間使用不具 AlwaysOn 在內部部署 |
| [執行使用壓縮的內部部署備份，然後手動將備份檔案複製到 Azure 虛擬機器](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 或更大 | SQL Server 2005 或更大 | [Azure 虛擬機器儲存限制](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | 使用僅當您不能使用精靈] 等時的目的地資料庫版本早於 SQL Server 2012 SP1 CU2 或資料庫的備份大小大於 1 TB (與 SQL Server 2016 12.8 TB) |
| [執行備份 URL 並還原至 Azure 虛擬機器與 URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 大於或等於 | SQL Server 2012 SP1 CU2 大於或等於 | < 12.8 TB 的 SQL Server 2016，否則 < 1 TB | 通常使用 [[備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx)即等同於使用精靈的效能並不太一樣簡單 |
| [卸離，然後將資料與記錄檔複製到 Azure blob 儲存與然後附加到 SQL Server 在 Azure 虛擬機器中的 URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 或更大 | SQL Server 2014 或更大 | [Azure 虛擬機器儲存限制](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | 當您打算[使用 Azure Blob storage service 這些檔案儲存](https://msdn.microsoft.com/library/dn385720.aspx)並附加至在 Azure 虛擬機器，特別是使用非常大型的資料庫中執行 SQL Server 使用此方法 |
| [將內部部署電腦轉換成 HYPER-V Vhd、 上傳至 Azure Blob 存放區，並再部署新的虛擬機器使用 [上傳的 VHD](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 或更大 | SQL Server 2005 或更大 | [Azure 虛擬機器儲存限制](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | 當[將帶您自己的 SQL Server 授權](../sql-database/sql-database-paas-vs-sql-server-iaas.md)，當移轉資料庫上的 SQL Server 或更舊版本移轉系統及使用者時所執行資料庫一起取決於其他使用者資料庫和/或系統資料庫的資料庫移轉的一部分使用。 |
| [使用 Windows 匯入/匯出服務運送硬碟](#ship-hard-drive) | SQL Server 2005 或更大 | SQL Server 2005 或更大 | [Azure 虛擬機器儲存限制](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | 手動複製方法太慢，例如與非常大型的資料庫時使用之[Windows 匯入/匯出 Service](../storage/storage-import-export-service.md) |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Azure 虛擬機器部署精靈教學課程

使用**SQL Server 資料庫 Microsoft Azure 虛擬機器來部署**精靈] 在 Microsoft SQL Server Management Studio 移轉 SQL Server 2005、 SQL Server 2008、 SQL Server 2008 R2、 SQL Server 2012、 SQL Server 2014 或 SQL Server 2016 內部使用者在 Azure 虛擬機器至 SQL Server 2014 或 SQL Server 2016 資料庫 (最多 1 TB)。 使用此精靈來移轉現有的 Azure 虛擬機器或移轉程序期間精靈所建立的 SQL Server 與 Azure 虛擬機器的使用者資料庫。 當您將資料庫移轉至新版的 SQL Server 時、 資料庫會自動升級程序期間。

方法是傳統部署模型。 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>取得最新版本的部署到 Microsoft Azure 虛擬機器] 精靈的 SQL Server 資料庫

若要確認您具備最新版的**SQL Server 資料庫 Microsoft Azure 虛擬機器來部署**精靈] 中使用最新版的 Microsoft SQL Server 的 SQL Server Management Studio。 此精靈的最新版本併入 Azure 傳統入口網站的最新更新及支援庫中的最新的 Azure 虛擬機器映像 （舊版精靈可能無法運作）。 取得 SQL Server，[下載](http://go.microsoft.com/fwlink/?LinkId=616025)最新版的 Microsoft SQL Server Management Studio 並連線至資料庫的您規劃移轉及網際網路用戶端電腦上安裝。

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>設定現有的 Azure 虛擬機器與 SQL Server 執行個體 （如果適用）

移轉至現有的 Azure 虛擬機器，執行下列設定步驟需要：

- 設定 Azure 虛擬機器和 SQL Server 執行個體，遵循下列步驟來[連線至 SQL Server 虛擬機器執行個體從 SSMS 另一部電腦上](virtual-machines-windows-sql-connect.md)啟用從另一部電腦的連線。 如果您使用精靈來進行移轉，支援僅顯示的 SQL Server 2014 和 SQL Server 2016 圖像庫中。
- Microsoft Azure 閘道與 11435 私人連接埠上設定開啟 SQL Server 雲端配接器服務的端點。 此連接埠會建立 SQL Server 2014 或 SQL Server 2016 佈建 Microsoft Azure 虛擬機器上的一部分。 雲端配接器也會建立允許其在預設連接埠 11435 的傳入 TCP 連線 Windows 防火牆規則。 此端點可讓使用從內部部署執行個體的備份檔案複製到 Azure 虛擬機器的雲端介面卡服務精靈]。 如需詳細資訊，請參閱 ＜ [SQL Server 的雲端介面卡](https://msdn.microsoft.com/library/dn169301.aspx)。

    ![建立雲端配接器端點](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Microsoft Azure 虛擬機器精靈來執行使用 Deploy SQL Server 資料庫

1. Microsoft SQL Server 2016 開啟 Microsoft SQL Server Management Studio 並連線至包含您要移轉至 Azure 虛擬機器的使用者資料庫的 SQL Server 執行個體。
2. 以滑鼠右鍵按一下您要移轉的資料庫，並指向 [工作然後按一下 [部署至 Microsoft Azure 虛擬機器。

    ![啟動精靈](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. 在 [簡介] 頁面上按一下 [下一步]。
4. 在 [來源設定] 頁面上連線至包含您要移轉 Azure 虛擬機器至資料庫的 SQL Server 執行個體。
5. 指定備份檔案的暫存位置。 如果連線至遠端伺服器，您必須指定網路磁碟機。

    ![來源設定](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. 按一下 [下一步]。
7. 在 Microsoft Azure 登入] 頁面上，按一下 [登入及登入您 Azure 的帳戶。
8. 選取您想要使用並按一下 [下一步] 的訂閱。

    ![Azure 登入](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. 在 [部署設定] 頁面上，您可以指定新的或現有的雲端服務名稱和虛擬機器名稱：

 - 指定新的雲端服務名稱和虛擬機器名稱來建立新的雲端服務搭配使用的 SQL Server 2014 或 SQL Server 2016 圖庫圖像新 Azure 虛擬機器。

     - 如果您指定新雲端服務名稱，指定您要使用的儲存空間帳戶。

     - 如果您指定現有的雲端服務名稱，將會擷取並輸入您儲存帳戶。

 - 指定現有的雲端服務名稱和新的虛擬機器名稱在現有的雲端服務中建立新的 Azure 虛擬機器。 只能指定的 SQL Server 2014 或 SQL Server 2016 庫 （英文） 圖像。
 - 指定現有 Cloud Service 名稱和要使用現有的 Azure 虛擬機器的虛擬機器名稱。 這必須使用 SQL Server 2014 或 SQL Server 2016 庫映像建置映像。

        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. 按一下 [設定]
  - 如果您指定現有的雲端服務名稱和虛擬機器名稱，系統會提示您提供的使用者名稱和密碼。

        ![Azure 電腦的設定](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

    - 如果您指定新的虛擬機器名稱，將會出現提示圖庫圖像清單中選取映像，並提供下列資訊：
      - 圖像-選取僅 [SQL Server 2014] 或 [SQL Server 2016
        - 使用者名稱
        - 新密碼
        - 確認密碼
        - 位置
        - 大小。
    - 此外，按一下 [接受自我產生的憑證針對此新 Microsoft Azure 虛擬機器與然後按一下 [確定]。

    ![Azure 新電腦的設定](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. 如果不同的來源資料庫名稱會指定目標資料庫名稱。 如果目標資料庫已存在，系統會自動遞增的資料庫名稱，而覆寫現有的資料庫。
12. 按一下 [下一步] 和 [完成]。

    ![結果](./media/virtual-machines-windows-migrate-sql/results.png)

13. 精靈完成時，連線至虛擬機器，並確認您的資料庫已移轉。
14. 如果您建立新的虛擬機器，請遵循下列步驟來[連線至 SQL Server 虛擬機器執行個體從 SSMS 另一部電腦上](virtual-machines-windows-sql-connect.md)設定 Azure 虛擬機器與 SQL Server 執行個體。

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>檔案並複製到虛擬機器與還原的備份

您不能使用 Deploy 到 Microsoft Azure 虛擬機器] 精靈的 SQL Server 資料庫時使用這個方法是因為您移轉至 SQL Server 2014 之前的 SQL Server 的版本或您的備份檔案大於 1 TB。 如果您備份檔案大於 1 TB，您必須等量它因為 VM 磁碟大小上限為 1 TB。 移轉使用者資料庫使用此手動方法使用下列一般步驟：

1.  執行完整資料庫備份至內部部署位置。
2.  建立或上傳虛擬機器與想要的 SQL Server 的版本。
3.  安裝程式根據自己的連線。 請參閱[連線至 SQL Server 虛擬機器在 Azure （資源管理員）](virtual-machines-windows-sql-connect.md)。
4.  在備份檔案複製到您的虛擬機器使用遠端桌面、 Windows 檔案總管或從命令提示字元中的 [複製] 命令。

## <a name="backup-to-url-and-restore"></a>URL 及還原的備份

您無法使用部署到 Microsoft Azure 虛擬機器] 精靈的 SQL Server 資料庫，因為您的備份檔案大於 1 TB 與您要移轉 from 和 to SQL Server 2016 時使用[備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx)的方法。 資料庫小於 1 TB 或執行 SQL Server 2016 之前的 SQL server 版本，建議使用的精靈。 與 SQL Server 2016、 2x 備份集支援、 建議的效能，並且需要超過大小限制每個 blob。 非常大的資料庫，建議使用[Windows 匯入/匯出服務](../storage/storage-import-export-service.md)。

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>卸離並複製到 URL 並附加從 URL

當您打算[使用 Azure Blob storage service 這些檔案儲存](https://msdn.microsoft.com/library/dn385720.aspx)並附加至在 Azure 虛擬機器，特別是使用非常大型的資料庫中執行 SQL Server 使用此方法。 移轉使用者資料庫使用此手動方法使用下列一般步驟：

1.  卸離資料庫檔案從內部資料庫執行個體。
2.  將卸離的資料庫檔案複製到 Azure blob 儲存使用[AZCopy 命令列公用程式](../storage/storage-use-azcopy.md)。
3.  附加資料庫檔案中的 Azure URL 在 Azure 虛擬機器中的 SQL Server 執行個體。

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>將轉換為虛擬機器及上傳至 URL 並部署為新的虛擬機器

使用此方法可將內部部署 SQL Server 執行個體中的所有系統及使用者資料庫都移轉至 Azure 虛擬機器。 移轉使用這個手動方法整個 SQL Server 執行個體使用下列一般步驟：

1.  使用[Microsoft 虛擬機器轉換程式](http://technet.microsoft.com/library/dn873998.aspx)將實體或虛擬機器轉換成 HYPER-V Vhd。
2.  使用[Add AzureVHD cmdlet](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)，VHD 檔案上傳至 Azure 存放區。
3.  使用上傳的 VHD 部署新的虛擬機器。

> [AZURE.NOTE] 若要移轉的整個應用程式，考慮使用[Azure 網站復原](../site-recovery/site-recovery-overview.md)]。

## <a name="ship-hard-drive"></a>隨附的硬碟

若要將大量的檔案資料傳輸至 Azure Blob 儲存在網路上傳所在極為昂貴或不合適的情況下使用[Windows 匯入/匯出服務方法](../storage/storage-import-export-service.md)。 與此服務中，您可以傳送一或多個硬碟包含該資料 Azure 資料中心，您的資料要上傳至您儲存的帳戶。

## <a name="next-steps"></a>後續步驟

為執行 SQL Server Azure 虛擬機器上的詳細資訊，請參閱[在 Azure 虛擬機器概觀 （英文） 上的 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)。

如需建立 Azure SQL Server 的虛擬機器從擷取映像的指示，請參閱 CSS SQL Server 工程師部落格上的[秘訣與技巧上複製 Azure SQL 虛擬機器從擷取的圖像](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/)。
