#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>如何使用 Azure 命令列工具 for Mac 和 Linux

本指南將說明如何使用 for Mac 和 Linux Azure 命令列工具來建立和管理 Azure 服務。 涵蓋的案例包含**安裝的工具**、**匯入您發佈的設定**、**建立及管理 Azure 網站**，以及**建立及管理 Azure 虛擬機器**。 完整參考文件，請參閱[Azure for Mac 和 Linux 文件的命令列工具][reference-docs]。 

##<a name="table-of-contents"></a>目錄
* [什麼是 for Mac 和 Linux Azure 命令列工具](#Overview)
* [如何安裝 Azure 命令列工具 for Mac 和 Linux](#Download)
* [如何建立 Azure 帳戶](#CreateAccount)
* [如何下載並匯入發佈設定](#Account)
* [如何建立和管理 Azure 網站](#WebSites)
* [如何建立及管理 Azure 虛擬機器](#VMs)


<h2><a id="Overview"></a>什麼是 for Mac 和 Linux Azure 命令列工具</h2>

Mac 和 Linux Azure 命令列工具是一組的命令列工具部署及管理 Azure 服務。
 
支援的工作包括下列各項︰

* 匯入發佈的設定。
* 建立及管理 Azure 網站。
* 建立及管理 Azure 虛擬機器。

若是支援命令的完整清單，請輸入`azure -help`之後安裝工具] 的命令列，請參閱[參考文件]或[reference-docs]。

<h2><a id="Download">如何安裝 Azure 命令列工具 for Mac 和 Linux</a></h2>

下列清單包含安裝的命令列的工具，視您的作業系統而定的資訊︰

* **Mac**︰ 下載[Azure SDK 安裝程式][mac-installer]。 開啟下載的.pkg 檔案，然後依照提示完成安裝步驟。

* **Linux**︰ 安裝最新版的[Node.js] [ nodejs-org] (請參閱[安裝 Node.js 透過封裝管理員][install-node-linux])，然後執行下列命令︰

        npm install azure-cli -g

    **注意**︰ 您可能需要執行此命令，以提高權限︰

        sudo npm install azure-cli -g

* **Windows**︰ 執行 Winows 安裝程式 （.msi 檔案），這是可以使用以下︰ [Azure 命令列工具][windows-installer]。


若要測試的安裝，請輸入`azure`在命令提示字元。 如果安裝成功，您會看到所有可用的清單`azure`命令。

<h2><a id="CreateAccount"></a>如何建立 Azure 帳戶</h2>

若要使用 Mac 和 Linux Azure 命令列工具，您必須 Azure 帳戶。

開啟網頁瀏覽器，瀏覽至[http://www.windowsazure.com] [windowsazuredotcom]按一下右上角的**免費試用版**。

![Azure 網站][Azure Web Site]

請依照下列指示，以建立帳戶。

<h2><a id="Account"></a>如何下載並匯入發佈設定</h2>

若要開始，您需要先下載並匯入您發佈設定。 這可讓您使用的工具來建立及管理 Azure 服務。 若要下載您發佈設定，請使用`account download`命令︰

    azure account download

這會開啟您的預設瀏覽器，並提示您登入管理入口網站。 登入後, 您`.publishsettings`會下載檔案。 請記下儲存此檔案的位置。

接下來，匯入`.publishsettings`檔案執行以下命令，來取代`{path to .publishsettings file}`使用路徑您`.publishsettings`檔案︰

    azure account import {path to .publishsettings file}

您可以移除所有儲存的資訊<code>import</code>使用 command<code>account clear</code>命令︰

    azure account clear

若要查看的選項清單`account`命令，使用`-help`選項︰

    azure account -help

匯入之後您發佈設定，您應該刪除`.publishsettings`檔案基於安全性理由。

> [AZURE.NOTE] 當您匯入發佈設定時，內儲存的認證以存取您的 Azure 訂閱您`user`資料夾。 您`user`資料夾受到您的作業系統。 不過，建議您採取其他步驟來加密您`user`資料夾。 您可以透過下列方式執行︰    
> 
> - 在 Windows 上，修改資料夾內容，或使用 BitLocker。
> - 在 Mac 上，開啟 FileVault 資料夾。
> - 在 Ubuntu，使用加密常用目錄功能。 其他 Linux 散佈提供對等功能。

您已準備好要建立及管理 Azure 網站和 Azure 虛擬機器。  

<h2><a id="WebSites"></a>如何建立和管理 Azure 網站</h2>

###<a name="create-a-website"></a>建立網站

若要建立的 Azure 網站，首先建立空白的目錄要求`MySite`瀏覽至該目錄。

然後，執行下列命令︰

    azure site create MySite --git

這個命令的輸出將包含新建立的網站的預設 URL。 `--git`選項可讓您使用給您本機應用程式目錄和中您網站的資料中心建立給存放庫發佈至您的網站。 請注意，是否您的本機資料夾已給存放庫，] 命令會將新的遠端加入現有的存放庫，指向您的網站資料中心裡儲存機制。

請注意，您可以執行`azure site create`command 使用下列其中一個選項︰

* `--location [location name]`. 這個選項可讓您指定的資料中心網站建立 （例如 「 西部美國 」） 的位置。 如果您省略這個選項，您必須選擇位置 promted。
* `--hostname [custom host name]`. 這個選項可讓您指定為您的網站是自訂主機名稱。

然後您可以將內容加入您的網站目錄。 使用一般給流程 (`git add`， `git commit`) 以確認您的內容。 您可以使用下列給命令推入 Azure 的 [網站內容︰ 

    git push azure master

###<a name="set-up-publishing-from-github"></a>設定 GitHub 發佈

若要設定連續發佈從 GitHub 存放庫，請使用`--GitHub`建立網站時的選項︰

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

如果您有 GitHub 存放庫的本機複本，或如果您有使用 GitHub 存放庫的單一遠端參考存放庫時，此命令會自動發佈 GitHub 存放庫至您的網站中的 [程式碼]。 之後，請於推入 GitHub 存放庫的任何變更會自動將發佈至您的網站。

當您從 GitHub 設定發佈所用的預設分支是主分支。 若要指定不同的分支，請從您的本機存放庫中執行下列命令︰

    azure site repository <branch name>

###<a name="configure-app-settings"></a>設定應用程式設定

應用程式設定是在執行階段應用程式可用的關鍵值組。 設定為 Azure 網站時，應用程式設定值會覆寫設定以定義您的網站 Web.config 檔案中的相同的金鑰。 Node.js 和 PHP 應用程式的應用程式設定，可為環境變數。 下列範例會顯示如何設定關鍵值組︰

    azure site config add <key>=<value> 

若要查看所有清單金鑰/值組，請使用下列動作︰

    azure site config list 

或者，如果您知道索引鍵，並想要查看的值，您可以使用︰

    azure site config get <key> 

如果您想要變更現有的索引鍵的值您必須先清除 [現有的索引鍵，然後再重新加入。 [清除] 命令時︰

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>清單和顯示網站

若要列出您的網站，請使用下列命令︰

    azure site list

若要取得網站的詳細的資訊，請使用`site show`] 命令。 下列範例顯示詳細資料`MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>停止、 啟動或重新啟動網站

您可以停止、 啟動或重新啟動與網站`site stop`， `site start`，或`site restart`命令︰

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>刪除網站

最後，您可以刪除的網站`site delete`命令︰

    azure site delete MySite

請注意，如果您執行的任何上方的命令執行位置的資料夾內`site create`，您不需要指定網站名稱`MySite`的最後一個參數。

若要查看的完整清單`site`命令，使用`-help`選項︰

    azure site -help 

<h2><a id="VMs"></a>如何建立及管理 Azure 虛擬機器</h2>

Azure 虛擬機器是從的虛擬機器圖像 （.vhd 檔案） 所提供的或可以使用圖像庫中建立。 若要查看所提供的圖像，請使用`vm image list`命令︰

    azure vm image list

您可以佈建並啟動其中一個可用的影像的虛擬機器`vm create`] 命令。 下列範例會示範如何建立 Linux 虛擬機器 (稱為`myVM`) (CentOS 6.2) 上的 [圖像] 庫中的圖像。 根使用者名稱和密碼的虛擬機器是`myusername`和`Mypassw0rd`分別。 (請注意，`--location`參數會指定在其中建立虛擬機器資料中心。 如果您省略`--location`參數，系統會提示您選擇的位置。)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

您可以考慮傳遞`--ssh`標幟 (Linux) 或`--rdp`標幟為] (Windows)`vm create`啟用遠端連線到新建立的虛擬機器。

如果您想要而佈建虛擬機器中的自訂圖像，您可以從.vhd 檔案來建立圖像`vm image create`命令，然後使用`vm create`佈建虛擬機器] 命令。 下列範例會示範如何建立 Linux 圖像 (稱為`myImage`) 從本機.vhd 檔案。 (`--location`參數會指定中儲存圖像的資料。)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

而非從本機.vhd 建立圖像，您可以從儲存在 Azure Blob 儲存體.vhd 建立圖像。 您可以使用`blob-url`參數︰

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

建立圖像之後, 您可以使用提供圖像虛擬機器`vm create`。 [下] 命令會建立虛擬機器稱為`myVM`從先前所建立的圖像 (`myImage`)。

    azure vm create myVM myImage myusername --location "West US"

您有佈建虛擬機器之後，您可能會想要建立允許遠端存取您的虛擬機器 （例如） 的結束點。 下列範例會使用`vm create endpoint`命令，以開啟外部連接埠 22 與本機連接埠 22 `myVM`:

    azure vm endpoint create myVM 22 22

您可以取得詳細的資訊 （包括 IP 位址、 DNS 名稱，以及結束點資訊） 虛擬機器與`vm show`命令︰

    azure vm show myVM

關閉，開始，或重新啟動虛擬機器，請使用下列命令其中一項︰

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

最後，若要刪除 VM，請使用`vm delete`命令︰

    azure vm delete myVM

建立及管理虛擬機器命令的完整清單，使用`-h`選項︰

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

