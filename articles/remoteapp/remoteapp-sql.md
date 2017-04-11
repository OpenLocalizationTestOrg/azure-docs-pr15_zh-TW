<properties
   pageTitle="Azure RemoteApp 與 SQL Azure |Microsoft Azure"
   description="瞭解如何使用 Azure RemoteApp SQL Azure。"
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>Azure RemoteApp 與 SQL Azure

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

客戶選擇裝載的雲端 Azure RemoteApp Windows 應用程式時，通常他們也會想要將其資料，例如 SQL server 移轉至雲端的整個雲端部署。 這可讓您可以隨時存取的任何位置使用 Azure RemoteApp 任何裝置的整個的雲端裝載解決方案。 以下是連結和參考資料以及指導方針可協助您處理此程序。  

## <a name="migrate-your-sql-data"></a>移轉您的 SQL 資料

開始移轉[到 Azure SQL 資料庫的 SQL Server 資料庫](../sql-database/sql-database-cloud-migrate.md)。 

## <a name="configure-azure-remoteapp"></a>設定 Azure RemoteApp
主控您的 Windows 應用程式中 Azure RemoteApp。 以下是更高的層級逐步︰

1.     建立[Azure RemoteApp 範本 VM](remoteapp-imageoptions.md)。 
2.     VM 上安裝必要的應用程式。
3.     設定應用程式，以便將其連線至 SQL 資料庫，並確認其運作方式。
4.     Sysprep 和關閉 VM。 擷取此為 Azure 搭配使用的圖像。 **附註︰**您必須確定應用程式可以保留完成 sysprep 程序 DB 連線資訊。 如果應用程式無法保留 DB 連線資訊，您可能會想要加入核取 [我們如何指定連線字串的應用程式的廠商。
5.     自訂圖像匯入選取適當的地理位置 SQL Azure 部署位於您 Azure RemoteApp 文件庫。 
6.     部署 RemoteApp 集合中相同的資料中心，為您使用上述範本的 SQL Azure 部署，並發佈應用程式]。 部署 Azure RemoteApp 相同的資料中心，為您的 SQL Azure 中部署可協助確保最快的連線速度，並減少延遲。 

## <a name="app-and-sql-configuration-considerations"></a>應用程式和 SQL 設定考量事項︰
有幾個點，使用 RemoteApp Azure SQL 時的考慮事項︰

瞭解[如何設定 Azure SQL 資料庫防火牆](../sql-database/sql-database-firewall-configure.md)。 摘錄文章狀態，請從 「 一開始，到 Azure SQL 資料庫伺服器的所有 access 會都封鎖防火牆。 若要開始使用您的 Azure SQL 資料庫伺服器，請您必須移至 [傳統] 入口網站，並指定一個或多個伺服器層級的防火牆規則，可讓您 Azure SQL 資料庫伺服器的存取權。 使用防火牆規則來指定允許的 IP 位址範圍，從網際網路]，以及 Azure 應用程式可以嘗試連線至 Azure SQL 資料庫伺服器。 」

此外，當的電腦嘗試從網際網路連線至資料庫伺服器，防火牆檢查是否有要求針對完整的伺服器層級的原始的 IP 位址 （如有必要） 和資料庫層級的防火牆規則。 「 要求的 IP 位址已在其中一個伺服器層級的防火牆規則中指定的範圍內，如果連線是授與您 Azure SQL 資料庫伺服器。 」 因此，請使用的 IP 範圍並不是只個別來源 IP 位址。

遵循的逐步指示[如何︰ 使用 Azure 入口網站的 SQL 資料庫設定防火牆](../sql-database/sql-database-configure-firewall-settings.md)至指定的 IP 範圍。 當您設定的 SQL 防火牆規則時，請提供子網路所指定的 IP 範圍 Azure RemoteApp 集合。 這應該讓其伺服器連線至 SQL 資料庫，即使對方會有動態指派 IP 位址。

## <a name="troubleshooting"></a>疑難排解
如果使用的用戶端應用程式的體驗裝載中 Azure RemoteApp 連線至 SQL 資料庫裝載於 Azure 的位置或者內部部署緩慢原因可能有幾個原因。  

- 從您的裝置 Azure 網路延遲是高。 為獲得最佳效能，以移至您可以最好和最快的網路連線。 若要測試您的裝置延遲 Azure 資料中心作為一般工具[azurespeed.com](http://azurespeed.com/) 。  
- 裝載於 Azure RemoteApp 的用戶端應用程式會。 選取不同的帳單方案，例如進階版的計費，藉此改善效能。 另一個訣竅是監控您的應用程式正在使用的資源︰ 在作用中的工作階段期間執行 ctrl alt 結尾的按鍵組合它會啟動 SA 畫面，選取 [工作管理員，可以一同觀看應用程式的資源使用狀況。
- SQL server 會或並未最佳化。 依照 SQL 指導方針進行疑難排解。 

