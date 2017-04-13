<properties
    pageTitle="建立執行 MySQL VM |Microsoft Azure"
    description="建立執行 Windows Server 2012 R2 和使用傳統的部署模型 MySQL 資料庫 Azure 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>建立與執行 Windows Server 2012 R2 傳統部署模型虛擬機器上安裝 MySQL

[MySQL](http://www.mysql.com)是熱門開啟來源、 SQL 資料庫。 本教學課程教您如何安裝及執行社群版本 MySQL 5.6.23 MySQL 伺服器上執行 Windows Server 2012 R2 虛擬機器。 如需安裝 MySQL Linux 上的指示，請參閱︰[如何安裝 MySQL Azure 上的](virtual-machines-linux-mysql-install.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>建立執行 Windows Server 2012 R2 虛擬機器

如果您還沒有 VM 執行 Windows Server 2012 R2，您可以使用此[教學課程中](virtual-machines-windows-classic-tutorial.md)建立虛擬機器。 

## <a name="attach-a-data-disk"></a>附加資料磁碟

建立虛擬機器之後，您可以選擇性地附加其他資料磁碟。 建議的生產負載，並以避免 OS 磁碟機 (C)，其中包括作業系統上執行的空間。

請參閱[如何附加到 Windows 虛擬機器資料磁碟](virtual-machines-windows-classic-attach-disk.md)，然後依照附加空的磁碟的指示進行。 設定主機快取設定為 [**無**或**唯讀狀態**。

## <a name="log-on-to-the-virtual-machine"></a>登入虛擬機器

接下來，您需[登入虛擬機器](virtual-machines-windows-classic-connect-logon.md)，讓您可以安裝 MySQL。

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>安裝及執行 MySQL 社群伺服器虛擬機器

請遵循這些步驟來安裝、 設定和執行社群版本的 MySQL 伺服器︰

> [AZURE.NOTE] 這些步驟適用於 5.6.23.0 MySQL 和 Windows Server 2012 R2 社群版本。 您的體驗可能不同版本的 MySQL 或 Windows Server 不同。

1.  您已連線至虛擬機器使用遠端桌面之後，請從 [開始] 畫面中按一下**Internet Explorer** 。
2.  在右上角 （cogged 的齒輪圖示） 上，選取 [**工具**] 按鈕，然後按一下 [**網際網路選項]**。 按一下 [**安全性**] 索引標籤，按一下 [**信任的網站**] 圖示，然後按一下 [**網站**] 按鈕。 新增 http://*。 mysql.com 的信任網站清單。按一下 [ **關閉**，然後按一下 [ **確定**。
3.  地址的 Internet Explorer 列中，輸入 http://dev.mysql.com/downloads/mysql/。
4.  若要找出並下載最新版的 MySQL 安裝程式在 Windows 版中使用 MySQL 網站。 在選擇 MySQL 安裝程式時，下載具有完整的檔案設定 (例如，mysql-安裝程式集社群-5.6.23.0.msi 282.4 MB 的檔案大小)，並儲存安裝程式版本。
5.  完成下載後，安裝程式，請按一下 [啟動安裝程式的 [**執行**]。
6.  在 [**授權合約**] 頁面接受授權合約，然後按一下 [**下一步**。
7.  在上**選擇 [安裝類型**] 頁面上，按一下 [安裝類型]，然後按 [**下一步**。 下列步驟假設**僅限伺服器**設定類型的選取範圍。
8.  在 [**安裝**] 頁面上，按一下 [**執行**]。 安裝完成後，按一下 [**下一步**]。
9.  在 [**產品設定**] 頁面上，按一下 [**下一步**]。
10. **類型與網路**頁面上，指定所需的設定類型與連線選項，如有需要包括 TCP 連接埠。 選取 [**顯示進階選項**]，然後按一下 [**下一步**。

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. 在 [**帳戶和角色**] 頁面上指定強式 MySQL 根密碼。 視需要新增其他 MySQL 使用者帳戶，然後按一下 [**下一步**。

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. 在 [ **Windows 服務**] 頁面中，指定為預設設定變更為 Windows 服務視需要執行 MySQL 伺服器，然後再按 [**下一步**]。

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. 在 [**進階選項**] 頁面中，指定登入選項的變更，視需要然後按 [**下一步**。

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. 在 [**套用伺服器設定**] 頁面中，按一下 [**執行**]。 完成設定步驟時，請按一下 [**完成**]。
15. 在 [**產品設定**] 頁面上，按一下 [**下一步**]。
16. **安裝完成**] 頁面上，按一下 [**複製到剪貼簿] 的記錄檔**如果您想要更新版本中，檢查它，然後按一下 [**完成]**。
17. 從 [開始] 畫面中，輸入**mysql**，，然後按一下**MySQL 5.6 命令列用戶端**。
18. 輸入您在步驟 11 指定根密碼，就會看到並且提示您可以在此發行命令以設定 MySQL。 命令和語法的詳細資訊，請參閱[MySQL 參考手冊](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. 您也可以設定伺服器設定預設設定，例如基底] 和 [資料目錄及磁碟機，以 C:\Program Files (x86) \MySQL\MySQL 伺服器 5.6\my-default.ini 檔案中的項目。 如需詳細資訊，請參閱[5.1.2 伺服器設定預設](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。

## <a name="configure-endpoints"></a>設定結束點

若要能夠在網際網路上的 MySQL 用戶端電腦的 MySQL 伺服器服務，您必須設定要在其上接聽 MySQL 伺服器服務的 TCP 連接埠端點，並建立其他的 Windows 防火牆規則。 除非您指定不同的連接埠 （上述程序的步驟 10） 中的 [**類型和網路**] 頁面上，這是 TCP 連接埠 3306。


> [AZURE.NOTE] 謹慎，因為這會使 MySQL 伺服器服務可在網際網路上的所有電腦，您應該考慮這麼做的安全性含意。 您可以定義允許使用端點與存取控制清單 (ACL) 的來源 IP 位址的設定。 如需詳細資訊，請參閱[如何設定設定結束點到虛擬機器](virtual-machines-windows-classic-setup-endpoints.md)。


若要設定的 MySQL 伺服器服務端點︰

1.  在 Azure 傳統的入口網站中，按一下**虛擬機器**，按一下 MySQL 虛擬機器的名稱，然後按一下 [**結束點**。
2.  命令列中，按一下 [**新增**]。
3.  在 [**新增到虛擬機器結束點**] 頁面上按一下右箭號。
4.  如果您使用的預設 3306 MySQL TCP 連接埠，按一下 [ **MySQL**在 [**名稱**]，然後按一下 [核取記號。
5.  如果您使用的不同的 TCP 連接埠，請在 [**名稱]**輸入唯一的名稱。 選取 [通訊協定中的 [ **TCP** **公用連接埠**與**私人連接埠**中, 輸入的連接埠號碼，再按一下 [核取記號。

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>新增 [Windows 防火牆規則，允許 MySQL 流量

若要新增從網際網路允許 MySQL 流量的 Windows 防火牆規則，請執行下列命令提高權限的 Windows PowerShell 命令提示字元 MySQL 伺服器虛擬機器上。

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>測試您遠端的連線


若要測試 MySQL 伺服器服務 Azure 虛擬機器上執行遠端的連線，您必須先決定對應到包含執行 MySQL 伺服器的虛擬機器雲端服務的 DNS 名稱。

1.  在 Azure 傳統的入口網站中，按一下**虛擬機器**，按一下 MySQL 伺服器虛擬機器的名稱，然後按一下 [**儀表板**。
2.  從虛擬機器儀表板，請注意**快速瀏覽**] 區段下的 [ **DNS 名稱**] 值。 以下是範例︰

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  從本機電腦上執行 MySQL 或 MySQL 用戶端，執行下列命令以 MySQL 使用者身分登入。

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    例如，如果 MySQL 使用者名稱 dbadmin3 及 testmysql.cloudapp.net DNS 名稱虛擬機器，使用下列命令。

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>後續步驟

若要進一步瞭解執行 MySQL 」 的資訊，請參閱[MySQL 文件](http://dev.mysql.com/doc/)。
