<properties
    pageTitle="主控 Linux VM 滑軌網站上的 [注音標示 |Microsoft Azure"
    description="設定及裝載在滑軌型網站使用 Linux 虛擬機器 Azure [注音標示。"
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Azure VM 滑軌 Web 應用程式上的 [注音標示

本教學課程中會顯示如何主控 Azure 滑軌網站上的 [注音標示使用 Linux 虛擬機器。  

本教學課程中已使用 Ubuntu 伺服器 14.04 LTS 來確認。 如果您使用不同的 Linux 通訊群組，您可能需要修改步驟來安裝滑軌。

> [AZURE.IMPORTANT] Azure 有兩種不同的部署模型建立及使用的資源︰[資源管理員] 及 [傳統](../../../resource-manager-deployment-model.md)。  本文涵蓋使用傳統的部署模型。 Microsoft 建議最新的部署使用資源管理員模型。

## <a name="create-an-azure-vm"></a>建立 Azure VM

開始建立 Azure VM Linux 圖像。

若要建立 VM，您可以使用 [Azure 傳統入口網站或 Azure 命令列介面 (CLI)。

### <a name="azure-management-portal"></a>Azure 管理入口網站

1. 登入[Azure 傳統入口網站](http://manage.windowsazure.com)
2. 按一下 [**新** > **計算** > **虛擬機器** > **快速建立**。 選取 Linux 圖像。
3. 輸入密碼。

VM 已佈建後，請按一下 VM 名稱上，然後按一下 [**儀表板**。 尋找 SSH 端點，請在 [ **SSH 詳細資料**列。

### <a name="azure-cli"></a>Azure CLI

請依照[建立虛擬機器執行 Linux][vm-instructions]。

VM 已佈建後，您可以取得 SSH 端點藉由執行下列命令︰

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>在機殼安裝 [注音標示

1. 使用 SSH vm 連線。

2. 從 SSH 工作階段，使用下列命令來安裝 [注音標示 VM 上︰

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    安裝可能需要幾分鐘的時間。 完成之後，使用下列命令確認已安裝 [注音標示︰

        ruby -v

    這會傳回 [注音標示已安裝的版本。

3. 若要安裝滑軌使用下列命令︰

        sudo gem install rails --no-rdoc --no-ri -V

    使用略過安裝的文件，這是更快速地--沒有 rdoc 和--沒有 ri 旗標。
    這個命令很可能會很長的時間執行，因此新增和 V，將會顯示安裝進度的相關資訊。

## <a name="create-and-run-an-app"></a>建立和執行應用程式

仍可透過 SSH 登入，請執行下列命令︰

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[[新增](http://guides.rubyonrails.org/command_line.html#rails-new)] 命令會建立新的滑軌應用程式。 [[伺服器](http://guides.rubyonrails.org/command_line.html#rails-server)] 命令會啟動隨附滑軌 WEBrick 網頁伺服器。 （用於生產環境時，可能要使用不同的伺服器，例如獨角獸或旅客。）

您應該會看到類似以下的輸出。

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>新增端點

1. 移至 [ [Azure 傳統入口網站][ management-portal] ，然後選取您 VM。

    ![虛擬機器清單][vmlist]

2. 選取頂端的頁面] 的**結束點**，然後按一下 [ **+ 新增結束點**] 頁面的底部。

    ![結束點] 頁面][endpoints]

3. 在 [**新增結束點**] 對話方塊中選取 [新增獨立結束點]，然後按一下**下一步**的箭號。

    ![新的結束點] 對話方塊][new-endpoint1]

3. 在下一頁的對話方塊上，輸入下列資訊︰

    * **名稱**︰ HTTP

    * **通訊協定**︰ TCP

    * **公用連接埠**︰ 80

    * **私人連接埠**︰ 3000

    這會建立公用連接埠 80，會將流量路由傳送至私人的連接埠 3000，滑軌伺服器接聽的位置。

    ![新的結束點] 對話方塊][new-endpoint]

4. 按一下 [儲存端點核取記號。

5. 郵件應該會出現，指出**正在進行中的更新**。 此訊息會消失，一旦端點為作用中。 現在，您可能會測試您的應用程式瀏覽至您的虛擬機器 DNS 名稱。 網站看起來應該類似下列動作︰

    ![預設滑軌頁面][default-rails-cloud]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您所做的大部分步驟以手動方式。 在生產環境中，您想要開發電腦上撰寫您的應用程式，並將其部署到 Azure VM。 此外，大部分的生產環境主控滑軌應用程式，例如 Apache 或 NginX，哪些控點要求路由至多個執行個體的滑軌應用程式與服務靜態資源的其他伺服器處理程序與搭配使用。 如需詳細資訊，請參閱 http://rubyonrails.org/deploy/。

若要進一步瞭解在機殼並列文字，請造訪[[注音標示滑軌輔助線][rails-guides]。

若要使用 Azure 服務從並列文字應用程式，請參閱︰

* [使用二進位大型物件的非結構化的資料儲存][blobs]

* [使用表格的儲存區金鑰/值組][tables]

* [提供高頻寬內容與內容傳遞網路][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
