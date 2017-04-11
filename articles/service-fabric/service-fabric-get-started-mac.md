<properties
   pageTitle="設定您的開發環境 Mac OS X 上 |Microsoft Azure"
   description="安裝執行階段、 sdk，您可以及工具，並建立本機開發叢集。 完成此安裝之後，您可以建立在 Mac OS X 上的應用程式。"
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>設定 Mac OS X 上您的開發環境

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

您可以建立在使用 Mac OS X Linux 叢集上執行的服務布料的轉印圖樣應用程式。本文說明如何設定您的 Mac 版開發。

## <a name="prerequisites"></a>必要條件

原生 OS X 上無法執行服務布料的轉印圖樣。若要執行本機的服務布料的轉印圖樣叢集，我們會提供使用 Vagrant 和 VirtualBox 預先設定的 Ubuntu 虛擬機器。 在您開始之前，您需要︰

- [Vagrant (v1.8.4 或更新版本)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>建立本機 VM

若要建立本機 VM 包含 5 節點服務布料的轉印圖樣叢集，請執行下列動作︰

1. 複製 Vagrantfile repo

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. 瀏覽至 repo 的本機複本

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. （選用）修改預設 VM 設定

    根據預設，本機 VM 設定，如下所示︰

    - 3 GB 的記憶體配置
    - 在 IP 192.168.50.50 設定的私人主機網路啟用傳遞的 Mac 主機流量

    您可以變更這些設定之一，或在 Vagrantfile vm 新增其他設定。 請參閱[Vagrant 文件](http://www.vagrantup.com/docs)的設定選項的完整清單。

4. 建立 VM

    ```bash
    vagrant up
    ```

    此步驟下載預先設定的 VM 圖像開機，且本機服務布料的轉印圖樣然後設定叢集中。 您應該會需要幾分鐘的時間。 如果安裝程式順利完成，您會看到訊息指出叢集啟動輸出中。

    ![叢集設定開始追蹤 VM 佈建][cluster-setup-script]

5. 叢集已設定正確瀏覽至服務布料的轉印圖樣檔案總管，在 http://192.168.50.50:19080/Explorer （假設您保留預設私人網路 IP） 的測試。

    ![檢視從 Mac 的主機服務布料的轉印圖樣總管][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>安裝該服務布料的轉印圖樣外掛程式的蝕霓紅 （選用）

服務布料的轉印圖樣蝕霓紅 IDE，以簡化建立並部署 Java 服務的程序提供外掛程式。

1. 在 [蝕，確認您擁有 Buildship 1.0.17 版本或更新版本的安裝。 您可以選擇以檢查已安裝的元件版本**說明 > 安裝的詳細資訊**。 您可以更新 Buildship 使用的指示[以下][buildship-update]。

2. 若要安裝的服務布料的轉印圖樣外掛程式，請選擇 [**說明 > 安裝新軟體**

3. 在 「 使用 」] 文字方塊中，輸入︰ http://dl.windowsazure.com/eclipse/servicefabric。

4. 按一下 [新增]。

    ![蝕霓紅外掛程式，服務布料的轉印圖樣。][sf-eclipse-plugin-install]

5. 選擇服務布料的轉印圖樣外掛程式，然後按一下 [下一步]。

6. 繼續安裝，並接受授權合約。

## <a name="next-steps"></a>後續步驟

- [建立 Linux 的第一個服務布料的轉印圖樣應用程式](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Azure 入口網站中建立服務布料的轉印圖樣叢集](service-fabric-cluster-creation-via-portal.md)
- [建立使用 Azure 資源管理員服務布料的轉印圖樣叢集](service-fabric-cluster-creation-via-arm.md)
- [瞭解服務布料的轉印圖樣應用程式模型](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
