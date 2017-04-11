<properties
   pageTitle="部署 Node.js 應用程式至 Azure 中 Linux 虛擬機器"
   description="瞭解如何部署 Linux 虛擬機器中 Azure Node.js 應用程式。"
   services=""
   documentationCenter="nodejs"
   authors="stepro"
   manager="dmitryr"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="stephpr"/>

# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>部署 Node.js 應用程式至 Azure 中 Linux 虛擬機器

本教學課程介紹如何 Node.js 應用程式並將其部署到 Linux 虛擬機器 Azure 中執行。 可以在能夠執行 Node.js 任何作業系統上遵循此教學課程中的指示進行。

您的學習方式︰

- 分叉和複製 GitHub 存放庫包含簡單 TODO 應用程式。
- 建立及設定兩個 Linux 虛擬機器中 Azure 執行應用程式。
- 持續按更新 web frontend 虛擬機器應用程式。

> [AZURE.NOTE]
> 若要完成此教學課程中，您需要 GitHub 帳戶和 Microsoft Azure 帳戶，能夠使用給從開發的電腦。

> 如果您沒有安裝 GitHub 帳戶，您可以[在這裡](https://github.com/join)設定登入。

> 如果您沒有[Microsoft Azure](https://azure.microsoft.com/)帳戶，您可以註冊免費試用版[以下](https://azure.microsoft.com/pricing/free-trial/)。 這會也逐步引導您註冊程序[的 Microsoft 帳戶](http://account.microsoft.com)如果您還沒有一個。 或者，如果您是 Visual Studio 訂閱者，您可以[啟動您的 MSDN 優點](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> 如果您沒有給您的部署電腦上，然後如果您使用的 Macintosh 或 Windows 電腦安裝給從[這裡](http://www.git-scm.com)。 如果您使用的 Linux，安裝給使用機制最適合您，如`sudo apt-get install git`。

## <a name="forking-and-cloning-the-todo-application"></a>分支和複製 TODO 應用程式

本教學課程中所使用的 TODO 應用程式實作簡單 web frontend 移轉會持續追蹤的待辦清單 MongoDB 執行個體。 登入後要 GitHub，請[在這裡](https://github.com/stepro/node-todo)尋找應用程式並分叉使用右上方的連結。 此應該建立存放庫中名為*accountname*/node-todo 您的帳戶。

現在您開發在電腦上，複製此存放庫︰

    git clone https://github.com/accountname/node-todo.git

我們將使用此本機複本存放庫稍微稍後原始程式碼進行變更時。

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>建立及設定 Linux 虛擬機器

Azure 有絕佳支援使用 Linux 虛擬機器未經處理的計算。 教學課程會顯示如何輕鬆地兩個 Linux 虛擬機器向上微調及部署 TODO 應用程式，以及其他的 MongoDB 執行個體上執行 web frontend 這個部分。

### <a name="creating-virtual-machines"></a>建立虛擬機器

若要建立新的虛擬機器中 Azure 最簡單的方法是使用 Azure 入口網站。 按一下[這裡](https://portal.azure.com)登入並啟動您的網頁瀏覽器中 Azure 入口網站。 一旦具有載入 Azure 入口網站，請完成下列步驟︰

- 按一下 [+ 新] 連結。
- 選擇 [計算] 類別，然後選擇 [」 Ubuntu 伺服器 14.04 LTS 」;
- 選取 「 資源管理員 」 部署模型，然後按一下 [建立]。
- 填入基本概念，請遵循下列方針︰
  - 指定的名稱，您可輕鬆識別稍後;
  - 在此教學課程中，選擇 [密碼驗證。
  - 建立新的資源群組可辨識的名稱。
- 虛擬機器的大小，「 A1 標準 」 是一個合理選擇此教學課程。
- 進行其他設定，請確定磁碟類型是 [標準] 並接受所有剩餘的預設值。
- 開始建立摘要頁面上。

執行上述程序，按兩次來建立兩個 Linux 虛擬機器、 另一個用於網頁 frontend MongoDB 執行個體。 建立虛擬機器帶約 5 到 10 分鐘的時間。

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>虛擬機器指派 DNS 項目

建立 Azure 虛擬機器是預設僅可透過公用的 IP 位址，例如 1.2.3.4 存取。 現在就讓我們，讓電腦更容易辨識指派這些 DNS 項目。

後入口網站指出虛擬機器所建立，請按一下左方瀏覽列中的 「 虛擬機器 」 連結，然後找出您的電腦。 每一部電腦︰

- 找出 [基本資訊] 索引標籤，然後按一下公用 IP 位址。
- 中的公用 IP 位址設定指派 DNS 名稱標籤，然後儲存。

入口網站，可確保您指定的名稱可使用。 儲存之後設定，您的虛擬機器必須主機名稱類似`machinename.region.cloudapp.azure.com`。

### <a name="connecting-to-the-virtual-machines"></a>連線至虛擬機器

當您的虛擬機器已佈建後時，他們是預先設定為允許透過 SSH 的遠端的連線。 這是我們將使用來設定虛擬機器機制。 如果您使用您的開發 Windows，您必須取得 SSH 用戶端，如果您還沒有一個。 以下是常見選擇是 PuTTY，您可以從[這裡](http://www.chiark.greenend.org.uk/~sgtatham/putty/)下載。 Macintosh 和 Linux Os 了 SSH 已預先安裝的版本。

### <a name="configuring-the-web-frontend-virtual-machine"></a>設定網頁 Frontend 虛擬機器

以您建立使用 PuTTY，ssh 命令或您其他最愛 SSH 工具的 web frontend 機器 SSH。 您應該會看到歡迎訊息，後面接著命令提示字元。

首先，讓我們來確認給和節點會同時安裝︰

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs
    
應用程式的 web frontend 依賴某些原生 Node.js 模組，因為我們也需要安裝必要的建立工具︰

    sudo apt-get install -y build-essential

最後，讓我們來安裝 Node.js 應用程式稱為 「*永無止境*，可協助執行 Node.js 伺服器應用程式︰

    sudo npm install -g forever
    
以下是需要在此虛擬機器上，才能執行應用程式的 web frontend，所以請將該執行的相依性。 若要這麼做，我們將第一次建立不包裝先前分叉使您可以輕鬆地將更新發佈至虛擬機器 （會為您介紹這個案例中更新更新版本），然後複製 [提供實際執行存放庫的版本的不包裝複製 GitHub 存放庫的複本。

從首頁 （~） 目錄開始，請執行下列命令 （將*accountname*取代您 GitHub 的使用者帳戶名稱）︰

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

現在輸入節點 todo 目錄，然後執行下列命令︰

    npm install
    forever start server.js
    
應用程式的 web frontend 目前正在執行，但有一個步驟之前，您可以從網頁瀏覽器存取應用程式。 您所建立的虛擬機器受到稱為*網路安全性群組*，其中提供虛擬機器時，為您所建立的 Azure 資源。 目前，這項資源只允許路由至虛擬機器，這可讓電腦，但還沒有 SSH 通訊的連接埠 22 外部要求。 因此，才能檢視 TODO 應用程式，會設定為執行連接埠 8080，此連接埠也必須開啟。

返回 Azure 入口網站，請完成下列步驟︰

- 按一下 「 資源群組 」 在左方瀏覽列。
- 選取包含您的虛擬機器; 的資源群組
- 在 [資源的 [結果] 清單中，選取 [網路安全性群組 （項目盾牌圖示）。
- 在屬性中，選擇 [」 輸入的安全性規則 」;
- 在工具列上，按一下 「 新增 」;
- 提供的名稱，例如 「 預設-允許-todo 」;
- 設定 「 TCP 」; 的通訊協定
- 設定為 「 8080 」; 目的地連接埠範圍
- 按一下 [確定]，並等待 [要建立的安全性規則。

建立這個安全性規則之後, TODO 應用程式會在網際網路上公開顯示，您可以瀏覽至，例如使用 URL，例如︰

    http://machinename.region.cloudapp.azure.com:8080

您會發現即使我們有尚未設定 MongoDB 虛擬機器，相當功能出現 TODO 應用程式。 這是因為來源存放庫硬式編碼，若要使用預先部署的 MongoDB 執行個體。 我們已設定 MongoDB 虛擬機器，我們會回到後變更來源程式碼，請改為使用我們私人 MongoDB 執行個體。

### <a name="configuring-the-mongodb-virtual-machine"></a>設定 MongoDB 虛擬機器

以另一部電腦所建立的 PuTTY，ssh 命令列您其他最愛的 SSH 工具 SSH。 後看到的歡迎訊息及命令提示字元中，安裝的 MongoDB （這些指示從[這裡](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)取得）︰

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

根據預設，讓它可以只在本機存取設定 MongoDB。 在此教學課程中，我們會設定 MongoDB，讓它可以從應用程式的虛擬機器中存取。 在 sudo 內容中，開啟 /etc/mongod.conf 檔案，並找出`# network interfaces`一節。 變更`net.bindIp`設定值`0.0.0.0`。

> [AZURE.NOTE]
> 此設定為僅在此教學課程的目的。 它**不建議的安全性的作法**，並不應該用於生產環境中。

現在，請確定已啟動服務 MongoDB:

    sudo service mongod restart

預設的連接埠 27017 上的運作方式 MongoDB。 因此，我們需要開啟連接埠 8080 web frontend 虛擬機器上相同的方式，我們需要開啟連接埠 27017 MongoDB 虛擬機器上。

返回 Azure 入口網站，請完成下列步驟︰

* 按一下 「 資源群組 」 在左方瀏覽列。
* 選取 [資源群組包含 MongoDB 虛擬機器中。
* 在 [資源的 [結果] 清單中，選取 [網路安全性群組 （項目與盾圖示） 具有相同名稱的配 MongoDB 虛擬機器中。
* 在屬性中，選擇 [」 輸入的安全性規則 」;
* 在工具列上，按一下 「 新增 」;
* 提供的名稱，例如 「 預設-允許-mongo 」;
* 設定 「 TCP 」; 的通訊協定
* 設定 「 27017 」; 的目的地連接埠範圍
* 按一下 [確定]，並等待 [要建立的安全性規則。

## <a name="iterating-on-the-todo-application"></a>逐一查看 TODO 應用程式
到目前為止，我們有佈建兩個 Linux 虛擬機器︰ 的執行中的應用程式的 web frontend，一個執行 MongoDB 執行個體。 但有問題-web frontend 不使用能夠的 MongoDB 的執行個體尚未。 現在就讓我們來更新 web frontend 程式碼，使用環境變數，而不是硬式編碼的執行個體的修正。

### <a name="changing-the-todo-application"></a>變更 TODO 應用程式

在第一次複製節點 todo 存放庫您開發電腦上開啟`node-todo/config/database.js`您最愛編輯器中的檔案，然後從硬式編碼值要像變更 url 值`mongodb://...`至`process.env.MONGODB`。

確認您的變更，並推入 GitHub 母片︰

    git commit -am "Get MongoDB instance from env"
    git push origin master

很抱歉，此不會將變更發佈至 web frontend 虛擬機器中。 讓我們來變更幾個更多的虛擬機器啟用發佈功能的更新，以便您可以快速地觀察即時環境中變更的效果的簡單而有效機制。

### <a name="configuring-the-web-frontend-virtual-machine"></a>設定網頁 Frontend 虛擬機器
回收我們先前建立的節點 todo 存放庫不包裝複製 web frontend 虛擬機器上。 開啟這個動作會建立新給遠端變更可以推入。 不過，只要推入到此遠端相當並未提供開發人員使用其程式碼時要尋找的快速反覆運算模型。

我們想要能夠做為確保推入到遠端存放庫虛擬機器上的時，執行 TODO 應用程式會自動更新。 所幸，這是可以輕易達到給使用。

給公開攔截程序，稱為 「 在特定時間來回應存放庫所採取動作的數字。 以下使用指定的命令介面指令碼將存放庫內`hooks`資料夾。 最適合用於自動更新案例鉤是`post-update`事件。

Web frontend 虛擬機器 SSH 工作階段，變更為`~/node-todo.git/hooks`目錄及新增下列內容至命名的檔案`post-update`(取代`machinename`和`region`您 MongoDB 虛擬機器資訊):

    #!/bin/bash
    
    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info
    
請確定此檔案可執行藉由執行下列命令︰

    chmod 755 post-update

這個指令碼確保停止目前的伺服器應用程式、 複製存放庫中的程式碼會更新為最新、 符合任何更新的相依性，並重新啟動伺服器。 也可確保環境的已接收我們的第一個應用程式更新從環境變數取得 MongoDB 執行個體的準備。

### <a name="configuring-your-development-machine"></a>設定您的部署電腦
現在就讓我們開始開發機器連結至網頁 frontend 虛擬機器。 這是非常簡單，將不包裝存放庫新增為遠端虛擬機器上。 （取代與您的 web frontend 虛擬機器登入名稱和*machinename*視*地區*的*使用者*），執行下列命令，請執行下列動作︰

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

這是所需以啟用 [推入，或 web frontend 虛擬機器作用中發佈，變更。

### <a name="publishing-updates"></a>發佈的更新

讓我們來發佈一個所做的變更具有已到目前為止，讓其他應用程式會使用我們的 MongoDB 執行個體︰

    git push azure master

您應該會看到類似︰

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

這個命令完成之後，請嘗試重新整理網頁瀏覽器中的應用程式。 您應該能夠看到介紹待辦清單是空白，不再採用共用部署的 MongoDB 執行個體。

若要完成教學課程，現在就讓我們進行其他、 更清楚的變更。 在您的部署電腦上開啟您最愛編輯器 node-todo/public/index.html 檔案。 找出 jumbotron 標題，然後變更 「 我可以是 Todo aholic 」 至 「 我可以是 Azure Todo aholic ！ 」 的標題。

現在讓我們來確認︰

    git commit -am "Azurify the title"

這次，再將它返回 GitHub repo 推現在就讓我們發佈至 Azure 的變更︰

    git push azure master

這個命令完成後，重新整理網頁，您會看到所做的變更。 因為其外觀不錯，請推變更至原點遠端︰ 

    git push origin master

## <a name="next-steps"></a>後續步驟
本文說明如何讓 Node.js 應用程式，並將其部署到 Linux 虛擬機器 Azure 中執行。 若要進一步瞭解 Linux 虛擬機器中 Azure，請參閱[Azure 上 Linux 簡介](/documentation/articles/virtual-machines-linux-introduction/)。
    
如需有關如何開發 Node.js Azure 上的應用程式的詳細資訊，請參閱[Node.js 開發人員中心](/develop/nodejs/)。
