<properties
   pageTitle="設定您的開發環境 linux |Microsoft Azure"
   description="安裝執行階段及 SDK，並建立本機開發叢集 Linux 上。 完成此安裝之後，您就可以開始建立應用程式項目。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>在 Linux 開發環境準備作業


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 若要部署 Linux 開發電腦上執行[Azure 服務布料的轉印圖樣的應用程式](service-fabric-application-model.md)，安裝執行階段和一般 SDK。 您也可以安裝選用 Sdk Java 和.NET 核心。

## <a name="prerequisites"></a>必要條件
### <a name="supported-operating-system-versions"></a>支援的作業系統版本
以下作業系統版本支援的開發︰

- Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>更新引起來源

安裝 SDK，並透過引起取得相關聯的執行階段套件，您必須先更新引起來源。

1. 開啟 terminal。
2. 將服務布料的轉印圖樣 repo 新增至您的來源清單中。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. 將您引起 keyring 新 GPG 鍵。

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. 重新整理您最近新增存放庫所根據的套件清單。

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>安裝和設定 SDK

之後會更新您的來源，您可以安裝 SDK。

1. 安裝的服務布料的轉印圖樣 SDK 套件。 系統會要求確認安裝並接受授權合約。

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. 執行 SDK 設定指令碼。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>設定 Azure 跨平台 CLI

[Azure 跨平台 CLI] [azure-xplat-cli-github]包含互動服務布料的轉印圖樣實體，包括叢集和應用程式的命令。 它會根據 Node.js，因此[請確定您有安裝節點][install-node]再繼續進行下列指示進行。

1. 複製到開發電腦 github repo。

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. 切換成複製 repo，並安裝 CLI 的相依性使用節點封裝管理員 (npm)。

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. 建立 symlink 從回收筒/azure 的資料夾複製 repo /usr/bin/azure，好讓它會新增至您的路徑和從任何目錄，您可以使用的命令。

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. 最後，啟用自動完成服務布料的轉印圖樣命令。

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>設定本機叢集

如果所有項目已成功安裝，您應該能開始本機叢集。

1. 執行叢集安裝指令碼。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. 開啟網頁瀏覽器，然後瀏覽至 http://localhost:19080/總管]。 如果已啟動叢集，您應該會看到 [服務布料的轉印圖樣總管儀表板。

    ![在 Linux 服務布料的轉印圖樣總管][sfx-linux]

此時，您就可以部署預先建立的服務布料的轉印圖樣應用程式套件 」 或 「 來賓容器或來賓可執行檔為基礎的新檔案。 若要建立新的服務使用 Java 或.NET 核心 Sdk，請遵循下列選用的設定步驟。

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>安裝 Java SDK 和蝕霓紅外掛程式 （選用）

Java SDK 提供文件庫和建置使用 Java 服務布料的轉印圖樣服務所需的範本。

1. 安裝 Java SDK 套件。

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. 執行 SDK 設定指令碼。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

您可以安裝蝕外掛程式的服務布料的轉印圖樣從蝕霓紅 IDE 中。

1. 在 [蝕，確認您擁有 Buildship 1.0.17 版本或更新版本的安裝。 您可以選擇以檢查已安裝的元件版本**說明 > 安裝的詳細資訊**。 您可以更新 Buildship 使用的指示[以下][buildship-update]。

2. 若要安裝的服務布料的轉印圖樣外掛程式，請選擇 [**說明 > 安裝新軟體**

3. 在 「 使用 」] 文字方塊中，輸入︰ http://dl.windowsazure.com/eclipse/servicefabric

4. 按一下 [新增]。

    ![蝕外掛程式][sf-eclipse-plugin]

5. 選擇服務布料的轉印圖樣外掛程式，然後按一下 [下一步]。

6. 繼續安裝，並接受授權合約。

## <a name="install-the-net-core-sdk-optional"></a>安裝.NET 核心 SDK （選用）

.NET 核心 SDK 提供文件庫和建置使用跨平台.NET 核心服務布料的轉印圖樣服務所需的範本。

1. 安裝.NET 核心 SDK 套件。

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. 執行 SDK 設定指令碼。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>後續步驟

- [Linux 上建立您的第一個 Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)

- [OSX 的開發環境準備作業](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
