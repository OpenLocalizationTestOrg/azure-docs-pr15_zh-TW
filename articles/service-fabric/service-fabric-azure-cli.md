<properties
   pageTitle="與使用 CLI 服務布料的轉印圖樣叢集互動 |Microsoft Azure"
   description="如何使用 Azure CLI 與服務布料的轉印圖樣叢集互動"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>使用 Azure CLI 與服務布料的轉印圖樣叢集互動

您可以從 Linux 電腦上 Linux 使用 Azure CLI 服務布料的轉印圖樣叢集與進行互動。

第一步取得從給代表 CLI 的最新版本，並設定在您路徑中使用下列命令︰

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

針對每個命令，支援，您可以輸入名稱的命令，以取得該命令的說明。 自動完成功能受支援的命令。 例如，下列命令可讓您說明的所有應用程式命令。 

```sh
 azure servicefabric application 
```

您可以進一步篩選特定的命令，說明]，如下列範例所示︰

```sh
 azure servicefabric application  create
```

若要啟用 CLI 的自動完成，請執行下列命令︰

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

下列命令連線到叢集，並顯示在叢集的節點︰

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

若要使用具名的參數，並尋找什麼是，您可以輸入，協助之後命令。 例如︰

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

從電腦**也就不是屬於叢集**，以連線至多機器叢集時, 使用下列命令︰

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

取代 PublicIPorFQDN 標記的實際 IP 或 FQDN 視。 從電腦**也就是叢集的組件**連線至多機器叢集時, 使用下列命令︰

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

您可以使用 PowerShell 或 Linux 服務布料的轉印圖樣叢集與互動 CLI 建立透過 Azure 入口網站。 

**注意︰**這些叢集不安全，因此，您可能會開啟一個方塊新增叢集資訊清單中的公用 IP 位址。



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>連線至服務布料的轉印圖樣叢集使用 Azure CLI

下列 Azure CLI 命令會說明如何連線至安全叢集。 憑證的詳細資訊，必須符合叢集節點的憑證。

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
如果您的憑證有憑證授權單位 (Ca)，您需要新增-ca 憑證路徑參數，如下列範例所示︰ 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
如果您有多個 Ca，使用逗號作為分隔符號。
 
如果您一般的憑證名稱不相符的連接端點，您可以使用參數`--strict-ssl`略過驗證，如下所示下列命令︰ 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
如果您想要略過 CA 驗證，您可以新增--拒絕未經授權的參數下列命令中所示︰ 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
在連線後，您應該可以執行其他 CLI 的命令與叢集互動。 

## <a name="deploying-your-service-fabric-application"></a>部署服務布料的轉印圖樣應用程式

執行下列命令，請 register，然後啟動服務布料的轉印圖樣應用程式︰

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>升級您的應用程式

程序很類似[Windows 中的程序](service-fabric-application-upgrade-tutorial-powershell.md)）。

建立、 複製、 register，並從專案根目錄建立您的應用程式。 如果您的應用程式的執行個體名稱為布料的轉印圖樣: / MySFApp，然後輸入 MySFApp，命令便如下︰

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

應用程式中進行變更，並重建已修改的服務。  更新與更新的版本修改的服務的資訊清單檔案 (ServiceManifest.xml) 服務 （與程式碼或設定或適當的資料）。 使用應用程式，並修改的服務的更新的版本號碼，也修改應用程式的資訊清單 (ApplicationManifest.xml)。  現在，複製並註冊您更新的應用程式，使用下列命令︰

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

現在，您可以使用下列命令來啟動應用程式升級︰

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

您現在可以監視使用 SFX 應用程式升級。 在幾分鐘，應用程式會有更新。  您也可以嘗試更新應用程式發生錯誤，並檢查服務布料的轉印圖樣自動復原功能。

## <a name="troubleshooting"></a>疑難排解

### <a name="copying-of-the-application-package-does-not-succeed"></a>複製的應用程式套件會失敗

如果核取`openssh`已安裝。 根據預設，Ubuntu 桌面沒有安裝它。 安裝並使用下列命令︰

```
 sudo apt-get install openssh-server openssh-client**
```

如果問題持續發生，請停用的 PAM ssh 藉由變更**sshd_config**檔案使用下列命令︰

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

如果問題仍持續發生，請嘗試增加數 ssh 工作階段藉由執行下列命令︰

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
使用索引鍵的 ssh 驗證 （而不是密碼） 不支援 （因為平台使用 ssh 複製封包），因此請改為使用密碼驗證。


## <a name="next-steps"></a>後續步驟

設定的開發環境和部署 Linux 叢集服務布料的轉印圖樣應用程式。
