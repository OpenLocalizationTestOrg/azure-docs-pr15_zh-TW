<properties
   pageTitle="連線到 Azure 容器服務叢集 |Microsoft Azure"
   description="使用 SSH 通道連線到 Azure 容器服務叢集。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker，容器，微服務，亦即/OS、 Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# <a name="connect-to-an-azure-container-service-cluster"></a>連線到 Azure 容器服務叢集

DC/OS 和 Azure 容器服務所部署 Docker 廣域叢集公開其餘結束點。 不過，不會開啟外面的世界這些端點。 如要管理這些端點，您必須建立安全命令介面 (SSH) 通道。 SSH 通道具有已建立後，您可以對叢集端點執行命令，並在您的系統檢視叢集使用者介面透過瀏覽器。 這份文件會引導您從 Linux、 OS X 和 Windows 建立 SSH 通道。

>[AZURE.NOTE] 您可以建立叢集管理系統的 SSH 工作階段。 不過，我們不建議這。 直接在管理系統的工作將公開無意間設定變更風險。   

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>建立一個 SSH 通道 Linux 或 OS X 上

當您建立一個 SSH 通道 Linux 或 OS X 上時，您的第一個項目是找出負載平衡母片的公用 DNS 名稱。 若要這麼做，展開 [資源] 群組，而顯示每個資源。 找出並選取 [母片的公用 IP 位址。 這會開啟包含相關包含 DNS 名稱的公用 IP 位址資訊刀。 儲存以供日後使用此名稱。 <br />


![公用 DNS 名稱](media/pubdns.png)

現在開啟命令介面，然後執行下列命令位置︰

**連接埠**是端點的您想要的方式公開的連接埠。 廣域，這是 2375年。 如需 DC/OS，使用連接埠 80。  
**使用者名稱**與部署叢集時所提供的使用者名稱。  
**DNSPREFIX**是部署叢集時所提供的 DNS 前置詞。  
**地區**是資源群組所在的區域。  
**PATH_TO_PRIVATE_KEY**[選擇性] 是對應到公用時所建立的容器服務叢集所提供的索引鍵的私密金鑰的路徑。 使用此選項與-i 加上標幟。

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> SSH 連線連接埠是 2200-不標準連接埠 22。

## <a name="dcos-tunnel"></a>DC/OS 通道

若要開啟的 DC/OS 相關的端點通道，執行類似下列命令︰

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

您現在可以存取 DC/OS 相關的結束點，在︰

- DC/作業系統︰`http://localhost/`
- Marathon:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

同樣地，您也可以連絡 rest Api 透過此通道的每一個應用程式。

## <a name="swarm-tunnel"></a>廣域通道

若要開啟通道廣域端點，請執行看起來就像以下命令︰

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

現在您可以設定 DOCKER_HOST 環境變數如下。 您可以繼續使用，您 Docker 命令列介面 (CLI)。

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>在 Windows 上建立 SSH 通道

有數個選項來建立 SSH 通道 Windows 上。 這份文件會說明如何使用 PuTTY 執行此動作。

下載到您的 Windows 系統的 PuTTY 並執行應用程式。

輸入包含叢集管理員的使用者名稱與公用 DNS 名稱的第一個主叢集主機名稱。 **主機名稱**看起來像這樣︰ `adminuser@PublicDNS`。 輸入 2200年**連接埠**。

![PuTTY 設定 1](media/putty1.png)

選取 [ **SSH** ] 和 [**驗證**]。 新增驗證您私密金鑰檔案。

![PuTTY 設定 2](media/putty2.png)

選取**通道**，並設定下列轉寄連接埠︰
- **來源連接埠︰**您的喜好設定，使用 80 DC/OS 或 2375 的廣域。
- **目的地︰**使用 localhost:80 DC/OS 或 localhost:2375 廣域。

下列範例針對 DC/OS，設定，但將類似的 Docker 廣域。

>[AZURE.NOTE] 連接埠 80 不可使用中，當您建立此通道。

![PuTTY 設定 3](media/putty3.png)

完成後，儲存連線的設定，然後連線 PuTTY 工作階段。 當您連線時，您可以看到 PuTTY 的事件記錄檔中的連接埠設定。

![PuTTY 的事件記錄檔](media/putty4.png)

當您已設定通道的 DC/OS 時，您可以存取在相關的結束點︰

- DC/作業系統︰`http://localhost/`
- Marathon:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

當您設定通道 Docker 廣域時，您可以透過 Docker CLI 來存取廣域叢集。 您將必須先設定一個名為 Windows 環境變數`DOCKER_HOST`值為` :2375`。

## <a name="next-steps"></a>後續步驟

部署及管理 DC/OS 或廣域容器︰

- [使用容器 Azure 服務和 DC/作業系統](container-service-mesos-marathon-rest.md)
- [使用容器 Azure 服務和 Docker 廣域](container-service-docker-swarm.md)
