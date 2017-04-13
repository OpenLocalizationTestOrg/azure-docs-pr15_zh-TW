<properties
    pageTitle="安裝 Azure 的命令列介面 |Microsoft Azure"
    description="安裝 Azure 命令列介面 (CLI) for Mac、 Linux 和 Windows 即可開始使用 Azure 服務"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>安裝 Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

快速安裝 Azure 命令列介面 (Azure CLI) 建立及管理 Microsoft Azure 中的資源使用一組開啟來源命令介面為基礎的命令。 您有幾個選項，您的電腦上安裝這些跨平台工具︰ 

* **npm 套件**-Linux 通訊群組或 OS 上安裝最新的 Azure CLI 套件執行 npm （JavaScript 封裝管理員）。 需要 node.js 與您電腦上的 npm。
* **安裝程式**-下載並安裝容易在 Mac 或 Windows 安裝程式。
* **Docker 容器**-開始使用準備-執行 Docker 容器中的最新 CLI。 需要在電腦上的 Docker 主機。
    
如需更多選項與背景，請參閱[GitHub](https://github.com/azure/azure-xplat-cli)專案存放庫。 

後 Azure CLI 安裝，請[將其與您 Azure 訂閱連線](xplat-cli-connect.md)，並從命令列介面 （艦隊、 終端機、 命令提示字元等等） 執行**azure**命令，以使用您 Azure 的資源。



## <a name="option-1-install-an-npm-package"></a>選項 1︰ 安裝 npm 套件

若要安裝 CLI 從 npm 套件，請確定您已下載並安裝[最新的 Node.js 和 npm](https://nodejs.org/en/download/package-manager/)。 然後，執行**npm 安裝**來安裝 azure cli 套件︰ 

    npm install -g azure-cli

在 Linux 散佈，您可能需要用**sudo**成功__npm__ ] 命令，如下所示︰

    sudo npm install -g azure-cli

> [AZURE.NOTE]如果您需要安裝或更新 Node.js 和 Linux 通訊群組或 OS 上的 npm，我們建議您安裝最新的 Node.js LTS 版本 (4.x)。 如果您使用較舊的版本，您可能會收到安裝錯誤。 

如果您想要的話，請下載最新的 Linux [tar 檔案][linux-installer]本機 npm 套件。 然後，如下所示 （在 Linux 分配，您可能需要使用**sudo**） 安裝下載的 npm 套件︰

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>選項 2︰ 使用安裝程式

如果您使用 Mac 或 Windows 電腦時，下列 CLI 安裝程式可供下載的︰

* [Mac OS X 安裝程式][mac-installer]

* [Windows MSI][windows-installer] 

>[AZURE.TIP]在 Windows 上，您也可以下載安裝 CLI [Web 平台安裝程式](https://go.microsoft.com/?linkid=9828653)。 此安裝程式可讓您安裝其他 Azure SDK 和命令列工具安裝 CLI 後的選項。 


## <a name="option-3-use-a-docker-container"></a>選項 [3︰ 使用 Docker 容器

如果您已設定為[Docker](https://docs.docker.com/engine/understanding-docker/)主機電腦，您可以執行最新的 Azure CLI Docker 容器中。 （在 Linux 分配，您可能需要使用**sudo**） 中，執行下列命令︰

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>執行 Azure CLI 命令
Azure CLI 安裝之後，請從命令列的使用者介面 （艦隊、 終端機、 命令提示字元等等） 執行**azure**的命令。 例如，若要執行 [說明] 命令，輸入以下資料︰

```
azure help
```
> [AZURE.NOTE]在某些 Linux 散佈，您可能會收到錯誤類似`/usr/bin/env: ‘node’: No such file or directory`。 此錯誤是來自 Node.js 安裝在 /usr/bin/nodejs 最近安裝。 若要修正此問題，請執行此命令建立 /usr/bin/node 符號連結︰

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

若要查看您安裝的 Azure cli 版本，輸入以下資料︰

```
azure --version
```

現在您已準備好了 ！ 若要存取所有 CLI 命令，以使用您自己的資源，[連線到您的 Azure Azure CLI 訂閱](xplat-cli-connect.md)。

>[AZURE.NOTE] 當您第一次使用 Azure CLI 時，您會看到訊息，詢問您是否要允許 Microsoft 收集使用狀況資訊。 參與是自動。 如果您選擇要加入，您可以隨時停止執行`azure telemetry --disable`。 若要啟用隨時參與，請執行`azure telemetry --enable`。


## <a name="update-the-cli"></a>更新 CLI

Microsoft 經常釋放 Azure CLI 的更新的版本。 重新安裝您的作業系統，請使用安裝程式 CLI 或執行最新的 Docker 容器。 或者，如果您有最新的 Node.js 和 npm 安裝，請更新，輸入下列 （在 Linux 分配，您可能需要使用**sudo**）。

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>啟用索引標籤完成

For Mac 和 Linux 支援 CLI 命令] 索引標籤完成。

若要啟用它 zsh 中，執行︰

```
echo '. <(azure --completion)' >> .zshrc
```

若要啟用它艦隊中，執行︰

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>後續步驟 

* [從 Azure 訂閱 CLI 連線](xplat-cli-connect.md)來建立和管理 Azure 資源。

* 若要進一步瞭解 Azure CLI，下載程式碼中，報告問題或參與專案，請造訪[Azure CLI GitHub 存放庫](https://github.com/azure/azure-xplat-cli)。

* 如果您有使用 Azure CLI 或 Azure 的相關問題，請造訪[Azure 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)。

* 如果您想，您也可以嘗試 Python 型[Azure CLI 2.0 預覽](https://github.com/azure/azure-cli)。

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
