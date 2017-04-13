<properties
   pageTitle="部署 3 節點 Deis 叢集 |Microsoft Azure"
   description="本文將說明如何建立 3 節點 Deis 叢集上 Azure 使用 Azure 資源管理員範本"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>部署 3 節點 Deis 叢集

本文將引導您佈建[Deis](http://deis.io/)叢集 Azure 上的。 矩形建立必要的憑證，部署，並在新能夠叢集縮放範例**移**應用程式的所有步驟。

下圖顯示已部署系統的架構。 系統管理員管理叢集使用 Deis 等**deis** **deisctl**工具。 透過 Azure 負載平衡器，其中轉寄連線到其中的成員叢集上的節點建立連線。 用戶端存取部署透過負載平衡器的應用程式。 在此情況下，負載平衡器轉寄流量 Deis 路由器網狀，進一步 routs 裝載於叢集對應 Docker 容器的流量。

  ![部署 Desis 叢集的架構圖表](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

若要執行下列步驟執行，您需要︰

 * 作用中的 Azure 訂閱。 如果您沒有帳戶，您可以取得免費的線索[azure.com](https://azure.microsoft.com/)上。
 * 若要使用 Azure 資源群組工作或學校識別碼。 如果您的個人帳戶和登入 Microsoft id，您需要[建立工作的識別碼，從您個人的項目](virtual-machines-windows-create-aad-work-id.md)。
 * 其中，用戶端視作業系統而- [PowerShell 的 Azure](../powershell-install-configure.md)或[Mac、 Linux，及 Windows Azure CLI](../xplat-cli-install.md)。
 * [OpenSSL](https://www.openssl.org/)。 OpenSSL 用來產生必要的憑證。
 * 例如[給艦隊](https://git-scm.com/)給客戶。
 * 若要測試的範例應用程式，您也必須在 DNS 伺服器。 您可以使用任何 DNS 伺服器或服務的支援萬用字元的記錄。
 * 若要執行的電腦 Deis 用戶端工具。 您可以使用 [本機電腦] 或 [虛擬機器。 您可以執行這些工具在幾乎任何 Linux 通訊群組，但下列指示使用 Ubuntu。

## <a name="provision-the-cluster"></a>佈建叢集

在此區段中，您會使用從開啟的來源存放庫[azure-快速入門-範本](https://github.com/Azure/azure-quickstart-templates) [Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)範本。 首先，您會將往下複製範本。 然後，您會建立新 SSH 組進行驗證。 然後，您會設定為您叢集新的識別碼。 然後，最後，您會使用的命令介面指令碼或 PowerShell 指令碼佈建叢集。

1. 複製存放庫︰ [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)。

        git clone https://github.com/Azure/azure-quickstart-templates

2. 移至 [範本] 資料夾︰

        cd azure-quickstart-templates\deis-cluster-coreos

3. 建立新 SSH 組使用 ssh-keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. 產生使用上述私密金鑰的憑證︰

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. 移至[https://discovery.etcd.io/new](https://discovery.etcd.io/new)產生新的叢集權杖，看起來會類似︰

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
每個 CoreOS 叢集需要的唯一的權杖，此可用的服務。 請如需詳細資訊，參閱[CoreOS 文件](https://coreos.com/docs/cluster-management/setup/cluster-discovery/)。

6. 修改以新的權杖取代現有的**探索**權杖**雲端 config.yaml**檔案︰

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. 修改**azuredeploy parameters.json**檔案︰ 開啟文字編輯器中的步驟 4 中所建立的憑證。 複製之間的所有文字`----BEGIN CERTIFICATE-----`和`-----END CERTIFICATE-----`到**sshKeyData**參數 （您會需要移除所有換行字元）。

8. 修改**newStorageAccountName**參數。 這是 VM OS 磁碟儲存帳戶。 此帳戶名稱必須是全域唯一。

9. 修改**publicDomainName**參數。 這會成為負載平衡器的公用 IP 相關聯的 DNS 名稱的一部分。 最終的 FQDN 會有_此參數值_的格式。_[區域]_。 cloudapp.azure.com。 比方說，如果您指定名稱為 deishbai32，且資源群組部署至西美國地區，然後至您的負載平衡器最終的 FQDN 會 deishbai32.westus.cloudapp.azure.com。

10. 儲存參數的檔案。 然後您可以在此提供使用 PowerShell 的 Azure 叢集︰

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  或 Azure CLI:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. 一旦資源群組佈建後，您可以看到 [] 群組中的所有資源 Azure 傳統入口網站上。 下圖所示，資源群組包含三個 Vm，連結到相同的可用性設定的虛擬網路。 群組也包含負載平衡器，具有相關聯的公用 IP。

  ![Azure 傳統入口網站上的 [能夠的資源] 群組](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>用戶端安裝

您需要**deisctl**來控制您 Deis 叢集。 雖然 deisctl 會自動安裝中所有叢集節點，但最好使用 deisctl 個別管理電腦上。 此外，因為所有節點都設定成僅私人 IP 位址，您會需要使用 SSH 通道透過負載平衡器，已連線至節點機器的公用 IP。 以下是設定 [在個別的實體 Ubuntu deisctl 或虛擬機器的步驟。

1. 安裝 deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. 新增至您的私密金鑰 ssh 代理程式︰

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. 設定 deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

範本定義輸入對應至執行個體 1、 2224 執行個體 2 和 3 的執行個體 2225年 2223年的 NAT 規則。 提供重複使用 deisctl 工具。 您可以檢查 Azure 傳統入口網站上的下列規則︰

![負載平衡器上 NAT 規則](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] 目前範本只支援 3 節點叢集。 這是因為 Azure 資源管理員範本 NAT 規則定義不支援迴圈語法的限制。

## <a name="install-and-start-the-deis-platform"></a>安裝及啟動 Deis 平台

現在，您可以使用 deisctl 安裝並啟動 Deis 平台︰

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] 開始在平台需要一段時間 （達 10 分鐘）。 特別是，開始建立器服務，可能需要較長的時間。 以及有時花幾次才能成功︰ 如果懸置作業，請輸入`ctrl+c`中斷命令的執行並再試一次。

您可以使用`deisctl list`驗證所有服務是否正在都執行︰

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

恭喜您 ！ 現在您已執行 Deis 上 Azure clsuter ！ 接下來，讓我們來部署行動應用程式，若要查看的實際操作叢集樣本。

## <a name="deploy-and-scale-a-hello-world-application"></a>部署及不按比例縮放的什麼應用程式

下列步驟說明如何部署"Hello World 」 移到叢集的應用程式。 步驟會根據[Deis 文件](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles)。

1. 針對才能正常運作的路由網路，您必須負載平衡器的公用 IP 指向您網域的萬用字元的記錄。 以下螢幕擷取畫面會顯示在 GoDaddy 的範例網域註冊的 A 記錄︰

    ![Godaddy A 記錄](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. 安裝 deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. 建立新的 SSH 機碼，並將公開金鑰加入 GitHub （當然，您也可以重複使用現有索引鍵）。 若要建立新的 SSH 組，請使用︰

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. 新增 GitHub id_rsa.pub 或公用您選擇的索引鍵。 您可以使用 [新增 SSH 金鑰] 按鈕 SSH 鍵設定畫面中︰

  ![Github 鍵](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. 註冊新的使用者︰

        deis register http://deis.[your domain]
<p />
6. 新增 SSH 機碼︰

        deis keys:add [path to your SSH public key]
  <p />      
7. 建立應用程式。

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.給推入會觸發 Docker 圖像以建立並部署，將需要花幾分鐘的時間。 我的經驗，有時候步驟 10 （私人存放庫 Pushing 圖像） 可能會停止回應。 發生這種情況下，您可以停止程序，移除應用程式使用 「 deis 應用程式︰ destroy – <application name> ` to remove the application and try again. You can use `deis apps:list 」，找出您的應用程式的名稱。 如果一切運作，您應該會看到類似下列命令輸出結尾的訊息︰

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. 確認是否使用應用程式︰

        curl -S http://[your application name].[your domain]
  您應該會看到︰

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. 不按比例縮放的 3 個執行個體的應用程式︰

        deis scale cmd=3
<p />
11. 或者，您可以使用 deis 資訊，請檢查您的應用程式的詳細資料。 下列輸出是從 [我的應用程式部署︰

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>後續步驟

本文帶您完成所有步驟，以佈建新 Deis 叢集上 Azure 使用 Azure 資源管理員範本。 範本中工具的連線，以及已部署應用程式的負載平衡支援重複。 範本也可避免上成員節點，就會儲存寶貴的公用 IP 資源，並提供主應用程式更安全的環境中使用公用 IPs。 若要深入瞭解，請參閱下列文章︰

[Azure 資源管理員的概觀] [resource-group-overview]  
[如何使用 Azure CLI] [azure-command-line-tools]  
[使用 Azure PowerShell 的 Azure 資源管理員] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md
