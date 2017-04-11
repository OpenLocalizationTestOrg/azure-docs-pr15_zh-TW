<properties
   pageTitle="執行自動的 Elasticsearch 恢復測試 |Microsoft Azure"
   description="您可以執行方式恢復測試您自己的環境中的描述。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="running-the-automated-elasticsearch-resiliency-tests"></a>執行自動的 Elasticsearch 恢復測試

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文是[一系列的一部分](guidance-elasticsearch.md)。

[設定彈性]且復原上 Azure Elasticsearch[elasticsearch-resilience-recovery]，我們所述的一系列的範例 Elasticsearch 叢集若要判斷系統如何回應失敗的一些常見的表單，以及如何將復原對所執行的測試。 測試已建立指令碼，以便讓使用者執行自動化方式。 這份文件將說明如何您可以在自己的環境中重複測試。 

測試以下情況︰

- **節點失敗，並重新啟動與遺失任何資料**。 停止資料節點，並在 5 分鐘後重新啟動。
重新配置遺失擊碎在這段，因此沒有額外的 I/O 中移動擊碎所產生，並且不設定 Elasticsearch。 節點重新啟動時，修復程序會顯示擊碎該節點回最新狀態。

- **與嚴重資料遺失的節點失敗**。 停止資料節點，它會保留的資料會清除模擬嚴重磁碟失敗。 節點然後重新啟動 （後 5 分鐘），有效做為取代原始節點。 修復程序需要重建這個節點遺失的資料，而且可能會重新放置擊碎保留其他節點。

- **節點失敗，並重新啟動遺失任何資料，但晶怪重新配置**。 停止資料節點，它會保留擊碎會重新配置到其他節點。 更多重新配置發生若要重新平衡叢集與節點然後重新啟動。

- **循環更新**。 停止叢集中的每個節點並重新啟動模擬的軟體更新後重新啟動電腦在短時間間隔。 只有一個節點並停止一次。
向下節點時，不會重新配置擊碎。

## <a name="prerequisites"></a>必要條件

自動的測試需要下列項目︰

- Elasticsearch 叢集。

- JMeter 環境設定[效能測試指南]所述。 

- 下列新增的項目上 JMeter 主 VM 只安裝。

    - Java 執行階段 7。

    - Nodejs 為 4.x.x 或更新版本。

    - 給命令列工具。

## <a name="how-the-scripts-work"></a>指令碼的運作方式

測試指令碼的目的是 JMeter 母片 VM 上執行。 當您選取要執行的測試時，指令碼會執行下列作業的順序︰

1.  開始將您指定的參數傳遞 JMeter 測試方案。

2.  複製會執行運算叢集指定 vm 測試所需的指令碼。 如果您已經建立使用[Azure Elasticsearch 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch)叢集，這可能是有公用的 IP 位址，任何 VM 或*Jumpbox* VM。

3.  執行指令碼 VM （或 Jumpbox）。

下圖顯示測試環境和 Elasticsearch 叢集的結構。 請注意，測試指令碼會使用安全殼層 (SSH)，在連線至叢集執行各種 Elasticsearch 作業，例如停止或重新啟動節點中每個節點。

![](./media/guidance-elasticsearch/resilience-testing1.png)

## <a name="setting-up-the-jmeter-tests"></a>設定 JMeter 測試

執行可靠度測試之前，您應該編譯及部署恢復/jmeter/測試資料夾中的 JUnit 測試。 JMeter 測試方案參考這些測試。 如需詳細資訊，請參閱 「 匯入現有 JUnit 測試專案蝕 」 的程序中[部署 JMeter JUnit 樣本中的，以進行測試 Elasticsearch 效能][]。

有兩個版本的下列資料夾中的 JUnit 測試︰

- **Elasticsearch17。** 在這個資料夾中的專案會產生 Elasticsearch17.jar 的檔案。 使用此 JAR 測試 Elasticsearch 版本 1.7.x

- **Elasticsearch20**。 在這個資料夾中的專案會產生 Elasticsearch20.jar 的檔案。 使用此 JAR 測試 Elasticsearch 版本 2.0.0 及更新版本

您 JMeter 機器複製適當的 JAR 檔案，以及相依性的其餘部分。 部署[JMeter JUnit 樣本中的 [測試 Elasticsearch 效能]的 「 部署 JMeter JUnit 測試 」 的程序來說明程序。

## <a name="configuring-vm-security-for-each-node"></a>設定為每個節點 VM 安全性

測試指令碼需要驗證憑證安裝在叢集中每個 Elasticsearch 節點。 這會啟用自動執行，而不會在連線至各種不同的 Vm 時提示輸入使用者名稱或密碼的指令碼。

首先請登入其中 Elasticsearch 叢集 （或 Jumpbox VM） 中的節點，並執行下列命令以產生驗證索引鍵︰

```Shell
ssh-keygen -t rsa
```

時連接到 Elasticsearch 節點 （或 Jumpbox），請執行下列命令，每個節點他 Elasticsearch 叢集。 取代`<username>`的每個 VM 及取代有效的使用者名稱與`<nodename>`DNS 名稱或裝載 Elasticsearch 節點 VM IP 位址。
請注意，系統會提示使用者的密碼時執行這些命令。
如需詳細資訊，請參閱[SSH 登入密碼](http://www.linuxproblem.org/art_9.html)︰

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## <a name="downloading-and-configuring-the-test-scripts"></a>下載並設定測試指令碼

測試指令碼提供給存放庫。 若要下載並設定指令碼中使用下列程序。

JMeter 母片在電腦上執行測試的位置，請開啟給桌面視窗 （給艦隊），然後複製存放庫包含的指令碼，如下所示︰

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

移至 [恢復測試] 資料夾，然後執行下列命令以安裝相依性所需執行的測試︰

```Shell
npm install
```

如果 JMeter 母片在 Windows 上執行，請下載[Plink](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)，這是命令列介面 PuTTY Telnet 用戶端。 將 Plink 可執行檔複製到恢復/測試文件庫資料夾中。

Linux 執行 JMeter 母片時，如果您不需要下載 Plink，但您必須以設定密碼的情況 SSH JMeter 母片和 Elasticsearch 節點或您使用下列程序中所述的步驟 Jumpbox 之間 」 設定 VM 安全性的每一個節點。 」 

編輯下列設定參數中的`config.js`以符合您的環境和 Elasticsearch 叢集的檔案。 這些參數是常見的所有測試︰

| 名稱 | 描述 | 預設值 |
| ---- | ----------- | ------------- |
| `jmeterPath` | 本機 JMeter 的所在位置的路徑。 | `C:/apache-jmeter-2.13` |
| `resultsPath` | 相對目錄] 指令碼要傾印結果。 | `results` |
| `verbose` | 指出是否指令碼輸出的詳細資訊模式。 | `true` |
| `remote` | 指出是否要在本機或遠端伺服器上執行的 JMeter 測試。 | `true` |
| `cluster.clusterName` | Elasticsearch 叢集的名稱。 | `elasticsearch` |
| `cluster.jumpboxIp`         | Jumpbox 電腦的 IP 位址。                 |-|
| `cluster.username`          | 您建立時部署叢集管理員使用者。 |-|
| `cluster.password`          | 管理員使用者的密碼。                        |-|
| `cluster.loadBalancer.ip`   | Elasticsearch 負載平衡器 IP 位址。    |-|
| `cluster.loadBalancer.url`  | 基底負載平衡器的 URL。                          |-|

## <a name="running-the-tests"></a>執行測試

移至 [恢復測試] 資料夾，然後執行下列命令︰

```Shell
node app.js
```

應該顯示以下功能表︰

![](./media/guidance-elasticsearch/resilience-testing2.png)

輸入您想要執行的分析藍本號碼︰ `11`， `12`，`13`或`21`。 

一旦您選取的情況，就會自動執行測試。 結果儲存為一組結果目錄下建立資料夾中的逗點分隔值 (CSV) 檔案。 每次執行都有它自己的結果的資料夾。
您可以使用 Excel 來分析及圖形此資料。

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[效能測試指引]: guidance-elasticsearch-creating-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[Considerations for JMeter]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[elasticsearch-resilience-recovery]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Resilience and Recovery Testing]: guidance-elasticsearch-running-automated-resilience-tests.md
[部署 JMeter JUnit 樣本中，以進行測試 Elasticsearch 效能]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
