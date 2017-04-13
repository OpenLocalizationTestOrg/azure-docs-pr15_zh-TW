<properties
    pageTitle="在 Windows VM 上安裝 MongoDB |Microsoft Azure"
    description="瞭解如何在執行 Windows Server 2012 R2 使用資源管理員部署模型建立 Azure VM 安裝 MongoDB。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>在安裝及設定 MongoDB Windows VM Azure 中
[MongoDB](http://www.mongodb.org)是常見的開啟來源、 高效能 NoSQL 資料庫。 本文會引導您上安裝和設定 MongoDB Azure 中的 Windows Server 2012 R2 虛擬機器 (VM)。 您也可以[安裝在 Azure 中 Linux VM MongoDB](virtual-machines-linux-install-mongodb.md)。


## <a name="prerequisites"></a>必要條件

安裝並設定 MongoDB 之前，必須先建立 VM 及理想的情況下，為其新增資料磁碟。 請參閱建立 VM 並將資料磁碟下列文章︰

- [建立 Windows Server VM 使用 Azure 入口網站](virtual-machines-windows-hero-tutorial.md)，或[建立 Windows Server VM 使用 PowerShell 的 Azure](virtual-machines-windows-ps-create.md)
- [附加至 Windows Server VM 資料磁碟使用 Azure 入口網站](virtual-machines-windows-attach-disk-portal.md)，或[附加資料磁碟與 Windows Server VM 使用 PowerShell 的 Azure](https://msdn.microsoft.com/library/mt603673.aspx)
    
若要開始安裝及設定 MongoDB，[登入您的 Windows Server VM](virtual-machines-windows-connect-logon.md)使用遠端桌面。


## <a name="install-mongodb"></a>安裝 MongoDB

> [AZURE.IMPORTANT] 根據預設，不會啟用 MongoDB 安全性功能，例如驗證和 IP 位址繫結。 應該部署 MongoDB 生產環境之前，先啟用安全性功能。 如需詳細資訊，請參閱[MongoDB 安全性和驗證](http://www.mongodb.org/display/DOCS/Security+and+Authentication)。

1. 您已連線至您 VM 使用遠端桌面之後，請開啟 Internet Explorer VM 上的 [**開始**] 功能表。

2. Internet Explorer 第一次開啟時，選取 [**使用建議的安全性、 隱私權和相容性設定**，然後按一下**[確定**]。

3. 依預設會啟用 Internet Explorer 增強的安全性設定。 將 MongoDB 網站新增至 [允許網站的清單︰

    - 選取右上角的 [**工具**] 圖示。
    - 在 [**網際網路選項]**中，選取 [**安全性**] 索引標籤，然後選取 [**信任的網站**] 圖示。
    - 按一下 [**網站**] 按鈕。 新增_https://\*。 mongodb.org_到信任的網站，然後關閉對話方塊的清單。

    ![Internet Explorer 安全性設定](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. 瀏覽至[MongoDB-下載](http://www.mongodb.org/downloads)頁面 (http://www.mongodb.org/downloads)。

5. 根據預設，它應該選取**社群伺服器**年度] 和 [最新目前穩定版本的 Windows Server 2008 R2 64 位元及更新版本。 若要下載安裝程式，請按一下 [**下載 (msi)**]。

    ![下載 MongoDB 安裝程式](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    下載完成後，請執行安裝程式。

6. 閱讀並接受授權合約。 當您收到提示時，請選取 [**完成**安裝。

7. 在最後一個畫面中，按一下 [**安裝**]。


## <a name="configure-the-vm-and-mongodb"></a>設定 VM 與 MongoDB

1. 路徑變數不會更新，MongoDB 安裝程式。 不 MongoDB`bin`在您的 path 變數的位置，您需要指定的完整路徑每次使用 MongoDB 可執行檔。 若要新增至您的 path 變數的位置︰

    - 以滑鼠右鍵按一下**[開始**] 功能表中，然後選取 [**系統**]。
    - 按一下 [**進階系統設定**]，然後按一下 [**環境變數**。
    - 在**系統變數**中，選取**路徑**，然後再按一下**編輯**。

    ![設定路徑變數](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    新增您 MongoDB 路徑`bin`資料夾。 MongoDB 通常是安裝在`C:\Program Files\MongoDB`。 請確認您 VM 上的安裝路徑。 下列範例會 MongoDB 安裝位置的預設`PATH`變數︰

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] 請務必新增前置分號 (`;`)，表示您要新增要的位置，您`PATH`變數。

2. 建立 MongoDB 資料和記錄目錄磁碟上的資料。 從**[開始**] 功能表中，選取 [**命令提示字元**]。 下列範例會建立目錄磁碟機上 f:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. MongoDB 執行個體的開頭以下命令，來調整您的資料的路徑，然後依此記錄目錄︰

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    可能需要幾分鐘 MongoDB 配置的筆記本檔案，並開始接聽連線。 所有記錄訊息會都導向至*F:\MongoLogs\mongolog.log*檔案，為`mongod.exe`伺服器啟動，並配置筆記本檔案。

    > [AZURE.NOTE] 當您 MongoDB 執行個體正在執行時，命令提示字元中保持著重於此工作。 [離開繼續執行 MongoDB 開啟命令提示字元視窗。 或者，安裝 MongoDB 為服務下, 一個步驟所述。

4. 更強大的 MongoDB 體驗，安裝`mongod.exe`為服務。 建立服務，就表示您不需要離開執行您想要使用 MongoDB 每的次命令提示字元。 根據這一點來調整您的資料和記錄目錄的路徑，如下所示，建立服務︰

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    之前的命令會建立名為 「 Mongo DB 」 的描述 MongoDB，服務。 也會指定下列參數︰

    - `--dbpath`選項會指定資料目錄的位置。
    - `--logpath`必須指定記錄檔中，使用選項，因為執行中的服務並沒有顯示輸出的命令] 視窗。
    - `--logappend`選項會指定重新啟動服務會使輸出至附加至現有的記錄檔。

  若要開始 MongoDB 服務，請執行下列命令︰

    ```
    net start MongoDB
    ```

    如需建立 MongoDB 服務的詳細資訊，請參閱[設定 MongoDB 」 的 Windows 服務](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service)。

## <a name="test-the-mongodb-instance"></a>測試 MongoDB 執行個體

MongoDB 執行為單一的執行個體，或安裝的服務，就可以開始建立和使用您的資料庫。 若要開始 MongoDB 管理命令介面，從**[開始**] 功能表中，開啟另一個命令提示字元視窗，然後輸入下列命令︰

```
mongo  
```

您可以列出的資料庫`db`] 命令。 插入一些資料如下所示︰

```
db.foo.insert( { a : 1 } )
```

搜尋資料，如下所示︰

```
db.foo.find()
```

輸出非常類似以下的範例︰

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

結束`mongo`主控台，如下所示︰

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>設定防火牆與網路安全性群組規則
現在 MongoDB 已安裝及執行，請開啟 Windows 防火牆] 中的連接埠，因此您可以從遠端連線到 MongoDB。 若要建立新的連入的規則，以允許 TCP 連接埠 27017，開啟管理的 PowerShell 提示，然後輸入下列命令︰

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

您也可以使用**具有進階安全性的 Windows 防火牆**圖形管理工具中建立規則。 建立新的連入的規則，以允許 TCP 連接埠 27017。

如有需要建立 [允許存取 MongoDB 從現有的 Azure 虛擬網路子網路外部網路安全性群組規則。 您可以使用[Azure 入口網站](virtual-machines-windows-nsg-quickstart-portal.md)或[PowerShell 的 Azure](virtual-machines-windows-nsg-quickstart-powershell.md)建立網路安全性群組規則。 與 Windows 防火牆規則，允許 TCP 連接埠 27017 您 MongoDB VM 的虛擬網路介面。

> [AZURE.NOTE] TCP 連接埠 27017 是 MongoDB 所使用的預設連接埠。 您可以藉由變更此連接埠`--port`參數啟動時`mongod.exe`手動或服務。 如果您變更連接埠，請務必一併更新的 Windows 防火牆和網路安全性群組的規則，在上述步驟。


## <a name="next-steps"></a>後續步驟
在本教學課程中，您學到如何安裝並設定您的 Windows VM MongoDB。 您現在可以在您的 Windows VM，存取 MongoDB 按照[MongoDB 文件](https://docs.mongodb.com/manual/)中的進階的主題。
