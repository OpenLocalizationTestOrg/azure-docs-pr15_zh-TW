<properties
    pageTitle="連線到 SQL 資料庫使用 SQL Server Management Studio 中 Azure RemoteApp |Microsoft Azure"
    description="若要瞭解如何使用 SQL Server Management Studio 中 Azure RemoteApp 安全性和效能連線至 SQL 資料庫時使用此教學課程"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>使用 SQL Server Management Studio 中 Azure RemoteApp 連線至 SQL 資料庫

## <a name="introduction"></a>簡介  
本教學課程教您如何使用 SQL Server 管理 Studio (SSMS) 中 Azure RemoteApp 連線至 SQL 資料庫。 它逐步引導您進行的 SQL Server Management Studio 中 Azure RemoteApp 設定程序，說明優勢，並顯示 [Azure Active Directory 中的 [安全性功能，您可以使用。

**估計完成時間︰** 45 分鐘

## <a name="ssms-in-azure-remoteapp"></a>在 [Azure RemoteApp SSMS

Azure RemoteApp 是 RDS 服務可提供應用程式的 Azure 中。 您可以進一步瞭解以下︰ [RemoteApp 是什麼？](../remoteapp/remoteapp-whatis.md)

SSMS Azure RemoteApp 中執行，可讓您在本機執行 SSMS 為相同的經驗。

![螢幕擷取畫面顯示 SSMS Azure RemoteApp 中執行][1]



## <a name="benefits"></a>優點

有使用 SSMS Azure RemoteApp 中有許多優點包括︰

- Azure SQL Server 的連接埠 1433年沒有公開外部 （外部 Azure)。
- 不是需要保留新增及移除 Azure SQL Server 防火牆中的 IP 位址。
- 所有 Azure RemoteApp 連線都發生透過 HTTPS 連接埠 443 使用加密遠端桌面通訊協定
- 多位使用者，並可以不按比例縮放。
- 有相同的 SQL 資料庫地區不必 SSMS 效能提升。
- 您可以稽核使用 Azure RemoteApp 使用進階版的 Azure Active Directory 有使用者活動報表。
- 您可以啟用多重因素驗證 (MFA)。
- 存取 SSMS 使用任何支援的 Azure RemoteApp 用戶端包括 iOS、 Android、 Mac、 Windows Phone 中，以及 Windows 電腦上時，任何一處。


## <a name="create-the-azure-remoteapp-collection"></a>建立 Azure RemoteApp 集合

若要建立 Azure RemoteApp 集合 SSMS 與步驟如下︰


### <a name="1-create-a-new-windows-vm-from-image"></a>1.建立新的 Windows VM 從的圖像
使用 「 Windows Server 遠端桌面工作階段 host （主機) Windows Server 2012 R2 」，讓您新增 VM 圖庫中的圖像。


### <a name="2-install-ssms-from-sql-express"></a>2.從 SQL Express 安裝 SSMS

移至新的 VM，然後瀏覽此下載頁面︰ [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

有僅下載 SSMS 的選項。 後下載時，請移至安裝目錄並執行安裝程式安裝 SSMS。

您也需要安裝 SQL Server 2014 Service Pack 1。 您可以在這裡下載︰ [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 包含使用 Azure SQL 資料庫的基本功能。


### <a name="3-run-validate-script-and-sysprep"></a>3.執行驗證指令碼及 Sysprep

在桌面上 VM 的 PowerShell 指令碼稱為驗證。 按兩下以執行此項。 它會驗證 VM 已可用於遠端裝載的應用程式。 完成驗證時，它會要求執行 sysprep-選擇要執行。

Sysprep 完成時，它會關閉 VM。

若要瞭解建立 Azure RemoteApp 圖像的詳細資訊，請參閱︰[如何建立 Azure 中的 RemoteApp 範本圖像](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4.擷取圖像

當 VM 已停止執行時，在目前的入口網站中尋找，並擷取它。

若要進一步瞭解擷取圖像，請參閱[擷取圖像的與傳統部署模型建立 Azure Windows 虛擬機器](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5.新增至 Azure RemoteApp 範本圖像

在 [目前的入口網站的 [Azure RemoteApp] 區段中，移至 [範本圖像] 索引標籤，然後按一下 [新增]。 在快顯的方塊中，選取 [匯入圖像您虛擬機器文件庫]，然後選擇您剛剛建立的圖像。



### <a name="6-create-cloud-collection"></a>6。 建立雲端集合

在目前的入口網站中建立新的 Azure RemoteApp 雲端集合。 選擇 SSMS 已經安裝與您剛才匯入的範本圖像。

![建立新的雲端集合][2]


### <a name="7-publish-ssms"></a>7。 發佈 SSMS

在 [發佈] 索引標籤的 [新的雲端集合，選取發佈應用程式從 [開始] 功能表，然後從清單中選擇 [SSMS。

![發佈應用程式][5]

### <a name="8-add-users"></a>8。 新增使用者

在 [使用者存取權限] 索引標籤上，您可以選取可以只包含 SSMS 這個 Azure RemoteApp 集合存取的使用者。

![新增使用者][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9。 安裝 Azure RemoteApp 用戶端應用程式

您可以下載及安裝的 Azure RemoteApp 用戶端︰[下載 |Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>設定 Azure SQL Server

僅限所需的設定可確保 Azure 服務已啟用防火牆。 如果您使用此方案，然後您不需要新增任何 IP 位址，以開啟防火牆。 允許 SQL Server 網路流量是來自其他 Azure 服務。


![Azure 允許][4]



## <a name="multi-factor-authentication-mfa"></a>多重因素驗證 (MFA)

MFA 可以特別啟用此應用程式。 移至您的 Azure Active Directory 中的 [應用程式] 索引標籤。 您會找到 Microsoft Azure RemoteApp 項目。 如果您按一下該應用程式]，然後設定，您會看到您可以在其中啟用此應用程式的 MFA 頁面下方。

![啟用 MFA][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>稽核與 Azure Active Directory 進階版的使用者活動

如果您沒有 Azure AD 進階版，您必須開啟目錄的 [授權] 區段中。 以啟用進階版，您可以為使用者指派進階版層級。

當您移至使用者在您的 Azure Active Directory 中時，您可以然後前往 [活動] 索引標籤以查看 Azure RemoteApp 登入資訊。



## <a name="next-steps"></a>後續步驟

完成所有上述步驟之後，您必須能夠執行 Azure RemoteApp 用戶端和登入與指定的使用者。 您將會看到與 SSMS 做為其中一個應用程式，您可以執行即如果您有權存取 Azure SQL Server 的電腦上已安裝。

如需有關如何建立連線到 SQL 資料庫的詳細資訊，請參閱[連線至 SQL Server Management Studio 與 SQL 資料庫並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)。


這是所有項目現在。 享受 ！



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
