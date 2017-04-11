<properties
    pageTitle="Cassandra Linux 執行上執行 Azure |Microsoft Azure"
    description="如何執行 Cassandra 叢集 linux Azure 虛擬機器中的商務連絡人，從 Node.js 應用程式"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Azure 上執行 Linux Cassandra 和從 Node.js 存取

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/)。

## <a name="overview"></a>概觀
Microsoft Azure 是以也為非 Microsoft 軟體，包括作業系統、 應用程式伺服器、 訊息介軟體，以及從兩個商業和開啟來源模型資料庫的 SQL 與 NoSQL 資料庫執行兩個 Microsoft 開啟雲端平台。 建立有彈性的服務，包括 Azure 公用雲朵上需要謹慎規劃與刻意架構的兩個應用程式伺服器為好的儲存空間的圖層。 Cassandra 的分散式的儲存架構自然有助於建立容錯叢集失敗的高度可用系統。 Cassandra 是雲端色階 cassandra.apache.org; 在維護 Apache 軟體 Foundation NoSQL 資料庫Cassandra Java 所撰寫，並因此兩 Windows，以及 Linux 上執行平台。

本文的焦點會顯示為單一與多個資料中心叢集運用 Microsoft Azure 虛擬機器與虛擬網路 Cassandra 部署 Ubuntu。 最佳化生產負載叢集部署超出本文的範圍，視需要多磁碟節點設定適當撥打拓撲設計與資料模型支援的所需的複寫、 資料的一致性，處理能力和可用性需求。

顯示內容的基本方法參與建置 Cassandra 叢集此文章會花費比 Docker、 主廚或傀儡可以讓基礎結構部署更加。  

## <a name="the-deployment-models"></a>部署模型
Microsoft Azure 網路可以讓隔離的私人叢集，可達成資訊粗略的網路安全性限制其存取權的部署。  本文是關於顯示 Cassandra 部署基本的層級，因為重點將會不一致性層級和處理量最佳的儲存空間設計。 以下是網路我們假設叢集需求的清單︰

- 外部系統無法存取 Cassandra 資料庫的內部或外部 Azure
- Cassandra 叢集有後面 thrift 流量的負載平衡器
- 部署中的增強型的叢集可用性每個資料中心的兩個群組中的 Cassandra 節點
- 鎖定叢集，因此，只有應用程式伺服器陣列直接具有資料庫的存取權
- 非 SSH 沒有公用網路端點
- 每個 Cassandra 節點需要固定的內部 IP 位址

單一 Azure 區域或多個區域根據分散工作負載的特性，可以部署 Cassandra。 多區域部署模型，可以利用作為使用者往透過相同 Cassandra 基礎結構的特定地理位置。 多重母片的同步處理的 Cassandra 的內建的節點複寫會負責將來自多個資料中心，並顯示一致的應用程式的資料檢視。 多區域部署也可以協助降低狀況提出更廣義的 Azure 服務中斷的風險。 Cassandra 的可微調一致性和複寫拓撲有助於在會議的應用程式的各種 RPO 需求。

### <a name="single-region-deployment"></a>單一區域部署
我們會開始在單一區域部署，並蒐集建立多區域模型檢討。 Azure 虛擬網路會用於建立隔離子網路，以便可以符合上述的網路安全性需求。  建立單一區域部署所述的程序使用 Ubuntu 14.04 LTS 和 Cassandra 2.08;不過，程序可以輕鬆地採用的其他 Linux 變數。 以下是部分的一個區域部署系統特性。  

**可用性︰**圖 1 Cassandra 節點部署到兩個可用性集以便節點展現之間高可用性的多個錯誤網域。 Vm 每個可用性設定將附註] 已對應至 2 故障網域。  Microsoft Azure 會使用錯誤網域管理升級的網域 （例如主機] 或 [訪客 OS 修補/升級，應用程式升級） 的概念意外關機時間 （例如硬體或軟體失敗） 的概念用來管理停機時間的排程。 請參閱[損毀修復和 Azure 應用程式的可用性](http://msdn.microsoft.com/library/dn251004.aspx)錯誤的角色與升級中取得可用性網域。

![單一區域部署](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

圖 1︰ 單一區域部署

請注意，在撰寫時，Azure 不允許特定的錯誤網域; 群組的 Vm 明確的對應因此，與圖 1 部署模型，甚至是統計可能的所有虛擬機器可能會都對應到兩個故障網域，而不是四。

**負載平衡 Thrift 流量︰**Thrift 內網頁伺服器的用戶端文件庫連線到內部負載平衡器透過叢集。 這需要新增到 「 資料 」 的子網路的內部負載平衡器的程序 （請參閱圖 1） 主機服務 Cassandra 叢集雲端服務的內容中。 一旦定義內部負載平衡器後，每個節點需要新增負載平衡集先前定義的負載平衡器名稱的註釋與負載平衡端點。 如需詳細資訊，請參閱[Azure 內部的負載平衡](../load-balancer/load-balancer-internal-overview.md)。

**叢集種子︰**請務必選取新的節點將通訊與探索叢集的拓撲種子節點種子的最高可用性節點。 每個可用性設定從一個節點避免失敗點指定為種子節點。

**複寫因素和一致性層級︰**Cassandra 的內建高可用性和資料持續性在於複寫因子變異數 (RF-叢集上儲存的每個資料列的份數) 和一致性層級 （為已讀取/撰寫結果傳回給呼叫者之前的複本數目）。 期間 （類似關聯式資料庫） 再建立指定複寫因子變異數，而時發出 CRUD 查詢指定的一致性層級。 請參閱 Cassandra 文件[的一致性設定](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html)的一致性詳細資料，仲裁計算的公式。

Cassandra 支援兩種類型的資料完整性模型 – 一致性和一致性。複寫因素和一致性層級同時會決定，一旦完成寫入作業是否會最後一致是否資料將會保持一致。 例如一致性層級一律會指定仲裁可確保資料一致性任何一致性層級時，才能寫入視需要完成所能獲得的複本的數字下方的仲裁 （例如一個） 結果中的最後一致的資料。

如上所示，使用 3 和仲裁複寫 factor 8 節點叢集 （2 節點讀取或寫入的一致性） 讀/寫一致性層級，不會受理論遺失的每個應用程式開始注意失敗之前複寫群組最 1 節點。 這是假設的所有按鍵空格也有平衡讀/寫要求。  以下是我們將使用部署叢集參數︰

單一區域 Cassandra 叢集設定︰

| 叢集參數 | 值 | 註解 |
| ----------------- | ----- | ------- |
| 數字的節點 (N) | 8   | 在叢集節點總數 |
| 複寫因子變異數 (RF) | 3 | 指定資料列的複本數目 |
| 一致性層級 （寫入） | QUORUM[(RF/2) +1) = 無條件捨位 2] 公式的結果 | 將回應傳送給呼叫者; 之前的最 2 的複本第 3 的複本會寫入最後一致的方式。 |
| 一致性層級 （唯讀） | 仲裁 [(RF/2) + 1 = 2] 無條件捨位公式的結果 | 傳送回覆給呼叫者之前讀取 2 的複本。 |
| 複寫策略 | NetworkTopologyStrategy 看到[資料複寫](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html)Cassandra 文件，如需詳細資訊 | 瞭解部署拓撲，並在 [節點] 上放置複本，好讓所有複本時，不都會出現上，在相同的機架 |
| 告密者 | GossipingPropertyFileSnitch Cassandra 如需詳細資訊的文件中看到 [ [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) | NetworkTopologyStrategy 瞭解拓撲使用告密者的概念。 GossipingPropertyFileSnitch 可讓您進一步控制中將每個節點對應至資料中心和機架。 叢集然後使用八卦傳播這項資訊。 這是更容易在相對於 PropertyFileSnitch 動態 IP 設定 |


**Cassandra 叢集 azure 考量︰**Microsoft Azure 虛擬機器功能磁碟保存; 使用 Azure Blob 儲存體Azure 儲存儲存高持續性的每個磁碟的 3 的複本。 這表示每一列資料插入 Cassandra 資料表已經儲存在 3 的複本，因此資料的一致性是已處理即使複寫因子變異數 (RF) 為 1。 複寫因子變異數的 1 的主要問題是應用程式會遇到停機時間，即使單一 Cassandra 節點失敗。 不過，如果節點已關閉辨識 Azure 布料的轉印圖樣控制器問題 （例如硬體、 系統軟體失敗），它會佈建位置使用相同的儲存空間磁碟機中新的節點。 佈建取代舊的新節點，可能需要幾分鐘的時間。  同樣地，例如來賓 OS 變更預定進行的維護活動，Cassandra 升級，應用程式的變更 Azure 布料的轉印圖樣控制器執行叢集運轉的節點的升級。  輪流升級也可能需要幾個節點下一次，因此叢集可能會遇到幾個磁碟分割區的簡短停機時間。 不過，資料不會遺失的內建的 Azure 儲存體重複狀況。  

部署至不需要高的顯示狀態 （例如周圍的相當於 8.76 的小時年; 如需詳細資訊，請參閱[高的可用性](http://en.wikipedia.org/wiki/High_availability)99.9) 的 Azure 系統您或許可以使用 RF 執行 = 1 和一致性層級 = 一項。  應用程式可用性需求，RF = 3 和一致性層級 = 仲裁會容許的其中一個節點其中一個複本的停機時間。 RF = 1 的傳統部署 （例如內部部署） 無法使用因為所產生的問題，例如磁碟失敗資料可能會遺失。   

## <a name="multi-region-deployment"></a>多區域部署
Cassandra 的資料中心-識別複寫和一致性模型上述有助於多區域部署，而不需要任何外部工具方塊登出。 這是很不同傳統的關聯式資料庫可能相當複雜的資料庫鏡像多重母片寫入的設定。 Cassandra 在多個區域設定可以協助包括以下狀況︰

**鄰近範圍基礎部署︰**多租用戶的應用程式中，清除對應的租用戶的使用者-至-地區，可以發展依多區域叢集低延遲。 例如學習管理系統的教育機構可以部署分散式的叢集東亞和西美國地區做為分別校園交易以及分析。 資料可以在時間讀取和寫入本機一致，並且可最後一致跨兩個區域。 有媒體通訊、 電子商務及任何項目等其他範例，做基底地理集中使用者的所有項目是很好的使用案例此部署模型。

**可用性︰**重複是取得的軟體和硬體; 可用性的主要因素如需詳細資訊，請參閱在 Microsoft Azure 建置可靠的雲端系統。 在 Microsoft Azure 達成，則為 true 的重複性的唯一可靠的方法是部署多區域叢集。 應用程式可以部署作用作用中] 或 [主動被動模式中，而且如果其中一個區域] 下，Azure 流量管理員可以流量重新導向至使用中的區域。  使用單一區域部署時，如果可用性 99.9，兩個區域部署可以達到 99.9999 公式計算出的可用性: (1-(1-0.999) *(1-0.999))*100);請參閱上述紙張如需詳細資訊。

**損毀修復︰**使用多區域 Cassandra 叢集，如果正確設計，能承擔嚴重的資料中心中斷。 向下一個區域時，應用程式部署到其他區域就可以開始給使用者。 類似的任何業務連續性實作，必須是容錯非同步管道的郵件中的資料所產生的部分資料遺失的應用程式。 不過，Cassandra 可以復原多 swifter 比傳統資料庫修復程序所花的時間。 圖 2 顯示具有八個節點的一般多區域部署模型中每個區域。 兩個區域是鏡映影像彼此對稱; 的相同真實設計工作負載類型 （例如交易或分析）、 RPO、 RTO、 資料的一致性及可用性需求而定。

![多重地區部署](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

圖 2︰ 多區域 Cassandra 部署

### <a name="network-integration"></a>網路的整合
與使用 VPN 通道彼此通訊的虛擬機器部署至私人網路上兩個區域。 VPN 通道連線網路部署程序期間佈建後的兩個軟體閘道器。 兩個區域有類似的網路架構，就 「 web 」 和 「 資料 」 的子網路;Azure 網路允許任何數量的子網路，視需要建立和套用 Acl，如有需要網路安全性。 在設計叢集拓撲時間資料中心通訊延遲和經濟網路流量需要考慮的影響。

### <a name="data-consistency-for-multi-data-center-deployment"></a>多個資料中心部署資料的一致性
發佈部署需要，您必須知道的輸出量和可用性叢集拓撲影響。 RF 和一致性層級需要選取仲裁不定的所有資料中心可用性這種方式。
為需要高的一致性的系統，LOCAL_QUORUM （適用於讀取和寫入） 的一致性層級會確定的本機讀取和寫入符合本機節點從遠端資料中心非同步複製資料時。  資料表 2 的摘要列出多區域叢集設定寫入稍後所述的設定詳細資料。

**兩個區域 Cassandra 叢集設定**


| 叢集參數 | 值 | 註解 |
| ----------------- | ----- | ------- |
| 數字的節點 (N) | 8 + 8 | 在叢集節點總數 |
| 複寫因子變異數 (RF) | 3 | 指定資料列的複本數目 |
| 一致性層級 （寫入） | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] 無條件捨位公式的結果 | 2 節點會寫入到第一個資料中心同步。將第 2 的資料中心非同步寫入仲裁所需的額外 2 節點。 |
| 一致性層級 （唯讀） | LOCAL_QUORUM ((RF/2) + 1) = 的 2 無條件捨位公式的結果 | 閱讀要求符合只有一個區域。回覆傳送至用戶端之前，會讀取 2 節點。 |
| 複寫策略 | NetworkTopologyStrategy 看到[資料複寫](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html)Cassandra 文件，如需詳細資訊 | 瞭解部署拓撲，並在 [節點] 上放置複本，好讓所有複本時，不都會出現上，在相同的機架 |
| 告密者 | GossipingPropertyFileSnitch Cassandra 如需詳細資訊的文件中看到 [ [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) | NetworkTopologyStrategy 瞭解拓撲使用告密者的概念。 GossipingPropertyFileSnitch 可讓您進一步控制中將每個節點對應至資料中心和機架。 叢集然後使用八卦傳播這項資訊。 這是更容易在相對於 PropertyFileSnitch 動態 IP 設定 |


##<a name="the-software-configuration"></a>軟體設定
部署時，會使用下列軟體版本︰

<table>
<tr><th>軟體</th><th>來源</th><th>版本</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

由於下載 JRE 需要手動接受 Oracle 授權，以簡化部署，下載所需的軟體更新版本，我們將建立為叢集部署前兆 Ubuntu 範本圖像上傳的桌面。

下載上述軟體到本機電腦上的已知下載目錄 （例如 Windows 上的 %TEMP%/downloads 或 ~/Downloads 大部分 Linux 散佈或 Mac 上的）。

### <a name="create-ubuntu-vm"></a>建立 UBUNTU VM
在此步驟的程序我們會以預先必要的軟體建立 Ubuntu 圖像，以便可重複使用的佈建數個 Cassandra 節點的圖像。  
####<a name="step-1-generate-ssh-key-pair"></a>步驟 1︰ 產生 SSH 組
Azure 必須公開金鑰 PEM 或 DER 編碼佈建次 X509。 產生公用/私人組使用位於如何使用 SSH 與 Linux Azure 上的指示進行。 如果您打算使用 putty.exe 為 [Windows 或 Linux 的 SSH 用戶端，您需要將轉換編碼 PEM RSA 私密金鑰使用 puttygen.exe; PPK 格式上述的網頁中找的指示。

####<a name="step-2-create-ubuntu-template-vm"></a>步驟 2︰ 建立 Ubuntu 範本 VM
若要建立範本 VM，請登入 Azure 傳統入口網站及使用下列程序︰ 按一下新增計算、 虛擬機器、 從圖庫、 UBUNTU、 Ubuntu 伺服器 14.04 LTS，然後再按一下右箭號。 說明如何建立 Linux VM 的教學課程，請參閱建立虛擬機器執行 Linux。

#1 」 虛擬機器設定] 畫面上輸入下列資訊︰

<table>
<tr><th>欄位名稱              </td><td>       欄位值               </td><td>         註解                </td><tr>
<tr><td>版本發行日期    </td><td> 向下選取 drow 日期</td><td></td><tr>
<tr><td>虛擬機器名稱    </td><td> cass 範本                </td><td> 這是 VM 的主機名稱 </td><tr>
<tr><td>層                     </td><td> 標準                        </td><td> 保留預設值              </td><tr>
<tr><td>大小                     </td><td> A1                              </td><td>選取 [根據 IO 需求; VM這項目的保留預設值 </td><tr>
<tr><td> 新的使用者名稱           </td><td> localadmin                      </td><td> 「 系統 」 會保留的使用者名稱和 [後 Ubuntu 12.xx 中</td><tr>
<tr><td> 驗證      </td><td> 按一下 [核取方塊                 </td><td>檢查您是否要使用 SSH 金鑰安全 </td><tr>
<tr><td> 憑證             </td><td> 公開金鑰憑證的檔案名稱 </td><td> 使用公開金鑰先前產生</td><tr>
<tr><td> 新的密碼   </td><td> 強式密碼 </td><td> </td><tr>
<tr><td> 確認密碼   </td><td> 強式密碼 </td><td></td><tr>
</table>

#2 」 虛擬機器設定] 畫面上輸入下列資訊︰

<table>
<tr><th>欄位名稱             </th><th> 欄位值                       </th><th> 註解                                 </th></tr>
<tr><td> 雲端服務  </td><td> 建立新的雲端服務    </td><td>雲端服務是容器計算之類的資源虛擬機器</td></tr>
<tr><td> 雲端服務的 DNS 名稱 </td><td>ubuntu template.cloudapp.net   </td><td>為電腦無從驗證負載平衡器命名</td></tr>
<tr><td> 地區/相關性群組/虛擬網路 </td><td>    西美制]。 </td><td> 選取 [從 web 應用程式存取 Cassandra 叢集的區域</td></tr>
<tr><td>儲存帳戶 </td><td>   使用預設值 </td><td>使用預設儲存帳戶或預先建立的儲存空間帳戶中的特定區域</td></tr>
<tr><td>顯示狀態設定 </td><td>  無 </td><td>  保留為空白</td></tr>
<tr><td>結束點   </td><td>使用預設值 </td><td>  使用預設的 SSH 設定 </td></tr>
</table>

按一下右箭號保留預設值的 [3] 畫面上，按一下 [檢查] 按鈕以完成 VM 佈建程序。 請稍候幾分鐘名稱 「 ubuntu-範本 」 VM 應該是 「 執行中 」 的狀態。

###<a name="install-the-necessary-software"></a>安裝必要的軟體
####<a name="step-1-upload-tarballs"></a>步驟 1︰ 上傳 tarballs
使用 scp 或 pscp，請使用下列命令格式 ~/downloads 目錄複製先前已下載的軟體︰

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp 伺服器-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

重複上述命令，以及 JRE 先 Cassandra 位。

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>步驟 2︰ 準備目錄結構，並擷取封存
登入 VM 和建立目錄結構，以使用下列艦隊指令碼的進階使用者擷取軟體︰

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


如果您將這個指令碼貼到 vim 視窗時，請確定移除歸位字元 ('\r 」) 使用下列命令︰

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>步驟 3︰ 編輯等/設定檔
附加結尾下列動作︰

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>步驟 4︰ 安裝 JNA 生產系統
使用下列命令順序︰ 下列命令會安裝 jna 3.2.7.jar 和 jna-平台-3.2.7.jar /usr/share.java 目錄取得 sudo 引起-安裝 libjna java

$CASS_HOME/文件庫目錄中建立符號連結，以便 Cassandra 啟動指令碼可以找到這些 （每瓶）︰

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>步驟 5︰ 設定 cassandra.yaml
編輯 cassandra.yaml 在每個 VM 以反映所需的所有虛擬機器中 [我們會調整此期間實際的佈建] 的設定︰

<table>
<tr><th>欄位名稱   </th><th> 值  </th><th> 註解 </th></tr>
<tr><td>cluster_name </td><td>  「 CustomerService 」   </td><td> 使用名稱，以反映您的部署</td></tr>
<tr><td>listen_address  </td><td>[空白]   </td><td> 刪除 「 本機 」 </td></tr>
<tr><td>rpc_addres   </td><td>[空白]  </td><td> 刪除 「 本機 」 </td></tr>
<tr><td>種子   </td><td>「 10.1.2.4、 10.1.2.6，10.1.2.8 」 </td><td>所有已指定作為種子 IP 位址的清單。</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> 這是由 NetworkTopologyStrateg 推斷資料中心和 VM 的機架</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>步驟 6︰ 擷取 VM 圖像
登入使用 [主機名稱 (香港特別行政區-ca-template.cloudapp.net) 和 SSH 私密金鑰先前建立的虛擬機器。 請參閱如何使用 SSH Linux 的詳細說明如何登入使用命令 ssh 或 putty.exe Azure 上使用。

執行下列動作來擷取圖像的順序︰
#####<a name="1-deprovision"></a>1.deprovision
使用命令] sudo waagent – deprovision + 使用者 「 若要移除的虛擬機器執行個體中的特定資訊。 請參閱[如何擷取 Linux 虛擬機器](virtual-machines-linux-classic-capture-image.md)使用另存為範本更多詳細資料的圖像擷取程序。

#####<a name="2-shutdown-the-vm"></a>2︰ 關閉 VM
確認虛擬機器會醒目提示，然後按一下 [下] 命令列中的 [關閉] 連結。

#####<a name="3-capture-the-image"></a>3︰ 擷取圖像
確認虛擬機器會醒目提示，然後按一下 [下] 命令列中的 [擷取] 連結。 在下一個畫面中，讓影像名稱 (例如 hk-cas-2-08-ub-14-04-2014071) 適當的圖像描述，然後按一下 「 核取 」 標記，以完成擷取程序。

這會需要幾秒且圖像應提供的圖像庫的 [我的圖像] 區段中。 來源 VM 就會自動 delated 之後成功擷取的圖像。

##<a name="single-region-deployment-process"></a>單一區域部署程序
**步驟 1︰ 建立虛擬網路**登入 Azure 傳統入口網站，並使用表格中的屬性顯示建立虛擬網路。 如需詳細步驟的程序，請參閱[設定 Cloud-Only 虛擬網路 Azure 傳統入口網站中](../virtual-network/virtual-networks-create-vnet-classic-portal.md)。      

<table>
<tr><th>VM 屬性名稱</th><th>值</th><th>註解</th></tr>
<tr><td>名稱</td><td>vnet-cass-西部-我們</td><td></td></tr>
<tr><td>區域</td><td>西美制]。</td><td></td></tr>
<tr><td>DNS 伺服器 </td><td>無</td><td>忽略這我們不使用 DNS 伺服器</td></tr>
<tr><td>設定點為網站 VPN</td><td>無</td><td> 略過此</td></tr>
<tr><td>設定網站-VPN</td><td>Nnone</td><td> 略過此</td></tr>
<tr><td>位址空間</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>啟動 IP</td><td>10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/ 16 (65531)</td><td></td></tr>
</table>

新增下列子網路︰

<table>
<tr><th>名稱</th><th>啟動 IP</th><th>CIDR</th><th>註解</th></tr>
<tr><td>網頁</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>網頁伺服器陣列的子網路</td></tr>
<tr><td>資料</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>資料庫節點的子網路</td></tr>
</table>

可以保護資料和 Web 子網路，透過網路安全性群組，其中涵蓋超出本文中的範圍。  

**步驟 2︰ 佈建虛擬機器**使用先前所建立的圖像，我們會建立下列虛擬機器中雲端伺服器 「 香港特別行政區-c-svc-西部 」，並繫結至個別的子網路，如下所示︰

<table>
<tr><th>電腦名稱    </th><th>子網路 </th><th>IP 位址 </th><th>顯示狀態設定</th><th>DC/機架</th><th>種子嗎？</th></tr>
<tr><td>香港特別行政區-c1-西部-我們   </td><td>資料   </td><td>10.1.2.4   </td><td>香港特別行政區-c-aset-1    </td><td>dc = WESTUS 機架 = rack1 </td><td>[是]</td></tr>
<tr><td>香港特別行政區-c2-西部-我們   </td><td>資料   </td><td>10.1.2.5   </td><td>香港特別行政區-c-aset-1    </td><td>dc = WESTUS 機架 = rack1 </td><td>無 </td></tr>
<tr><td>香港特別行政區-c3-西部-我們   </td><td>資料   </td><td>10.1.2.6   </td><td>香港特別行政區-c-aset-1    </td><td>dc = WESTUS 機架 = rack2 </td><td>[是]</td></tr>
<tr><td>香港特別行政區-c4-西部-我們   </td><td>資料   </td><td>10.1.2.7   </td><td>香港特別行政區-c-aset-1    </td><td>dc = WESTUS 機架 = rack2 </td><td>無 </td></tr>
<tr><td>香港特別行政區-c5-西部-我們   </td><td>資料   </td><td>10.1.2.8   </td><td>香港特別行政區-c-aset-2    </td><td>dc = WESTUS 機架 = rack3 </td><td>[是]</td></tr>
<tr><td>香港特別行政區-c6-西部-我們   </td><td>資料   </td><td>10.1.2.9   </td><td>香港特別行政區-c-aset-2    </td><td>dc = WESTUS 機架 = rack3 </td><td>無 </td></tr>
<tr><td>香港特別行政區-c7-西部-我們   </td><td>資料   </td><td>10.1.2.10  </td><td>香港特別行政區-c-aset-2    </td><td>dc = WESTUS 機架 = rack4 </td><td>[是]</td></tr>
<tr><td>香港特別行政區-c8-西部-我們   </td><td>資料   </td><td>10.1.2.11  </td><td>香港特別行政區-c-aset-2    </td><td>dc = WESTUS 機架 = rack4 </td><td>無 </td></tr>
<tr><td>香港特別行政區-w1-西部-我們   </td><td>網頁    </td><td>10.1.1.4   </td><td>香港特別行政區-w-aset-1    </td><td>                       </td><td>N/A</td></tr>
<tr><td>香港特別行政區-w2-西部-我們   </td><td>網頁    </td><td>10.1.1.5   </td><td>香港特別行政區-w-aset-1    </td><td>                       </td><td>N/A</td></tr>
</table>

上述清單的 Vm 建立需要以下程序︰

1.  建立空白的雲端服務中的特定區域
2.  建立 VM 從先前所擷取的圖像，並將其附加至先前; 建立虛擬網路針對所有 Vm 重複此步驟
3.  將內部負載平衡器新增至雲端服務，並將它附加到 「 資料 」 的子網路
4.  如先前所建立的每個 VM，請參閱新增連線至先前建立的內部負載平衡器負載平衡集 thrift 流量與負載平衡端點

可以使用 Azure 傳統入口網站; 執行上述程序使用 Windows 電腦 （VM Azure，如果您沒有存取權的 Windows 電腦上的使用），使用下列 PowerShell 指令碼自動佈建所有 8 Vm。

**適用於佈建虛擬機器清單 1: PowerShell 指令碼**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**步驟 3︰ 設定每個 VM Cassandra**

登入 VM，然後執行下列步驟︰

* 編輯 $CASS_HOME/conf/cassandra-rackdc.properties 以指定的資料中心及機架內容︰

       dc =EASTUS, rack =rack1

* 編輯 cassandra.yaml 設定種子節點如下︰

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**步驟 4︰ 啟動 Vm 和測試叢集**

登入其中一個節點 （例如香港特別行政區-c1-西部-我們），然後執行下列命令，請參閱叢集的狀態︰

       nodetool –h 10.1.2.4 –p 7199 status

您應該會看到類似以下 8 節點叢集的顯示︰

<table>
<tr><th>狀態</th><th>地址  </th><th>載入   </th><th>權杖 </th><th>擁有 </th><th>主機識別碼  </th><th>機架</th></tr>
<tr><th>取消  </td><td>10.1.2.4   </td><td>87.81 KB   </td><td>256    </td><td>38.0%  </td><td>Guid （移除）</td><td>rack1</td></tr>
<tr><th>取消  </td><td>10.1.2.5   </td><td>41.08 KB   </td><td>256    </td><td>68.9%  </td><td>Guid （移除）</td><td>rack1</td></tr>
<tr><th>取消  </td><td>10.1.2.6   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>Guid （移除）</td><td>rack2</td></tr>
<tr><th>取消  </td><td>10.1.2.7   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>Guid （移除）</td><td>rack2</td></tr>
<tr><th>取消  </td><td>10.1.2.8   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>Guid （移除）</td><td>rack3</td></tr>
<tr><th>取消  </td><td>10.1.2.9   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>Guid （移除）</td><td>rack3</td></tr>
<tr><th>取消  </td><td>10.1.2.10  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>Guid （移除）</td><td>rack4</td></tr>
<tr><th>取消  </td><td>10.1.2.11  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>Guid （移除）</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>測試單一區域圖組
若要測試叢集使用下列步驟︰

1.    使用 Powershell 命令取得 AzureInternalLoadbalancer commandlet，取得的 IP 位址的內部負載平衡器 （例如︰ 10.1.2.101)。 命令的語法如下所示︰ 取得 AzureLoadbalancer – ServiceName 」 香港特別行政區-c-svc-西部-我們 」 [顯示內部負載平衡器，以及其 IP 位址的詳細資料]
2.  登入網頁伺服器陣列 VM （例如香港特別行政區-w1-西部-我們） 使用 Putty 或 ssh
3.  執行 $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4.  您可以使用下列 CQL 命令驗證叢集是否正常運作︰

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

您應該會看到類似以下的顯示︰

<table>
  <tr><th> customer_id </th><th> 名字 </th><th> [姓氏] </th></tr>
  <tr><td> 1 </td><td> 名字 </td><td> 為何 </td></tr>
  <tr><td> 2 </td><td> 珍 </td><td> 為何 </td></tr>
</table>

請注意，在步驟 4 中建立再使用 SimpleStrategy replication_factor 的 3。 SimpleStrategy 建議的單一資料中心部署而 NetworkTopologyStrategy 多資料中心部署。 3 的 replication_factor 可讓容錯節點失敗。

##<a id="tworegion"></a>多區域部署程序
將運用已完成的一個區域部署，並重複相同的程序安裝第二個區域。 單一與多個區域部署之間的主要差異為間的地區通訊; VPN 通道的設定我們會開始網路安裝與佈建 Vm 設定 Cassandra。

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>步驟 1︰ 建立虛擬網路第 2 地區
登入 Azure 傳統入口網站，並使用表格中的屬性顯示建立虛擬網路。 如需詳細步驟的程序，請參閱[設定 Cloud-Only 虛擬網路 Azure 傳統入口網站中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。      

<table>
<tr><th>屬性名稱    </th><th>值    </th><th>註解</th></tr>
<tr><td>名稱    </td><td>vnet-cass-東亞-我們</td><td></td></tr>
<tr><td>區域  </td><td>設定適用於美國</td><td></td></tr>
<tr><td>DNS 伺服器     </td><td></td><td>忽略這我們不使用 DNS 伺服器</td></tr>
<tr><td>設定點為網站 VPN</td><td></td><td>     略過此</td></tr>
<tr><td>設定網站-VPN</td><td></td><td>      略過此</td></tr>
<tr><td>位址空間   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>啟動 IP </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>CIDR    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

新增下列子網路︰
<table>
<tr><th>名稱    </th><th>啟動 IP    </th><th>CIDR   </th><th>註解</th></tr>
<tr><td>網頁 </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>網頁伺服器陣列的子網路</td></tr>
<tr><td>資料    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>資料庫節點的子網路</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>步驟 2︰ 建立本機的網路
Azure 虛擬網路中的區域網路是對應到遠端網站，包括私人的雲端或另一個 Azure 地區 proxy 位址空間。 此 proxy 位址空間繫結到正確的網路目的地遠端路由網路的閘道器。 如需建立 VNET-VNET 連線指示，請參閱[設定 VNet 到 VNet 連線](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

建立兩個區域的網路，每個下列詳細資料︰

| 網路名稱 | VPN 閘道器位址 | 位址空間 | 註解 |
| ------------ | ------------------- | ------------- | ------- |
| hk-lnet-map-to-east-us | 23.1.1.1  | 10.2.0.0/16   | 建立區域網路時為 [版面配置區閘道器的地址]。 一旦建立閘道器填滿的實際的閘道器地址。 請確定位址空間完全符合個別的遠端 VNET;在此情況下 VNET 建立東亞美國地區。 |
| hk-lnet-map-to-west-us | 23.2.2.2  | 10.1.0.0/16   | 建立區域網路時為 [版面配置區閘道器的地址]。 一旦建立閘道器填滿的實際的閘道器地址。 請確定位址空間完全符合個別的遠端 VNET;在此情況下 VNET 建立西美國地區。 |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>步驟 3︰ 地圖 」 本機 」 網路分別 VNETs
從 Azure 傳統的入口網站中，選取每個 vnet 「 設定 」、 「 連線到本機網路 」] 核取 [，然後按一下選取每個下列詳細資料的本機網路︰


| 虛擬網路 | 區域網路 |
| --------------- | ------------- |
| 香港特別行政區-vnet-西部-我們 | hk-lnet-map-to-east-us |
| 香港特別行政區-vnet-東亞-我們 | hk-lnet-map-to-west-us |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>步驟 4︰ 建立閘道器 VNET1 和 VNET2
從儀表板的虛擬網路中，按一下 [會觸發 VPN 閘道器佈建程序建立閘道器]。 請稍候幾分鐘的每個虛擬網路的儀表板應該顯示實際的閘道器位址。

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>步驟 5︰ 更新 「 本機 」 網路個別的 「 閘道器 」 地址###
編輯這兩個區域網路，若要取代的真實的 IP 位址的只提供的閘道器的版面配置區的閘道器 IP 位址。 使用下列對應︰

<table>
<tr><th>區域網路    </th><th>虛擬網路閘道器</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>香港特別行政區-vnet-西部-我們的閘道器</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>香港特別行政區-vnet-東亞-我們的閘道器</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>步驟 6︰ 更新共用索引鍵
若要更新的每個 VPN 閘道器 [使用兩個閘道器的起見索引鍵] 的 IPSec 金鑰使用下列 Powershell 指令碼︰ 設定 AzureVNetGatewayKey VNetName 香港特別行政區-vnet-東亞-我們-LocalNetworkSiteName hk-lnet-map-to-west-us-SharedKey D9E76BKK 設定 AzureVNetGatewayKey VNetName 香港特別行政區-vnet-西部-我們-LocalNetworkSiteName hk-lnet-map-to-east-us-SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>步驟 7︰ 建立 VNET-VNET 連線
從 Azure 傳統的入口網站中，使用 「 儀表板] 功能表的虛擬網路建立閘道-連線。 使用 [底部] 工具列中的 [連線] 功能表項目。 請稍候幾分鐘的儀表板應以圖形方式顯示連線詳細資料。

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>步驟 8︰ 建立虛擬機器區域 #2
建立 Ubuntu 圖像中所述地區 #1 部署按照相同的步驟，或是 Azure 儲存體帳戶圖像 VHD 檔案位於地區 2 的複本，並建立圖像。 使用此圖像，並建立下列清單的虛擬機器到新的雲端服務香港特別行政區-c-svc-東亞-我們︰


| 電腦名稱 | 子網路 | IP 位址 | 顯示狀態設定 | DC/機架 | 種子嗎？ |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| 香港特別行政區-c1-東亞-我們 | 資料  | 10.2.2.4   | 香港特別行政區-c-aset-1      | dc = EASTUS 機架 = rack1 | [是] |
| 香港特別行政區-c2-東亞-我們 | 資料  | 10.2.2.5   | 香港特別行政區-c-aset-1      | dc = EASTUS 機架 = rack1 | 無  |
| 香港特別行政區-c3-東亞-我們 | 資料  | 10.2.2.6   | 香港特別行政區-c-aset-1      | dc = EASTUS 機架 = rack2 | [是] |
| 香港特別行政區-c5-東亞-我們 | 資料  | 10.2.2.8   | 香港特別行政區-c-aset-2      | dc = EASTUS 機架 = rack3 | [是] |
| 香港特別行政區-c6-東亞-我們 | 資料  | 10.2.2.9   | 香港特別行政區-c-aset-2      | dc = EASTUS 機架 = rack3 | 無  |
| 香港特別行政區-c7-東亞-我們 | 資料  | 10.2.2.10  | 香港特別行政區-c-aset-2      | dc = EASTUS 機架 = rack4 | [是] |
| 香港特別行政區-c8-東亞-我們 | 資料  | 10.2.2.11  | 香港特別行政區-c-aset-2      | dc = EASTUS 機架 = rack4 | 無  |
| 香港特別行政區-w1-東亞-我們 | 網頁   | 10.2.1.4   | 香港特別行政區-w-aset-1      | N/A                    | N/A |
| 香港特別行政區-w2-東亞-我們 | 網頁   | 10.2.1.5   | 香港特別行政區-w-aset-1      | N/A                    | N/A |


依照地區 #1 為相同的指示進行，但使用 10.2.xxx.xxx 位址空間。

###<a name="step-9-configure-cassandra-on-each-vm"></a>步驟 9︰ 設定每個 VM Cassandra
登入 VM，然後執行下列步驟︰

1. 編輯指定的資料中心及機架屬性格式 $CASS_HOME/conf/cassandra-rackdc.properties: dc = EASTUS 機架 = rack1
2. 編輯設定種子節點 cassandra.yaml︰ 種子: 「 10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10 」

###<a name="step-10-start-cassandra"></a>步驟 10︰ 開始 Cassandra
登入每個 VM，然後執行下列命令，以啟動背景 Cassandra: cassandra/CASS_HOME/回收筒 $

## <a name="test-the-multi-region-cluster"></a>測試多區域圖組
現在已部署 Cassandra 至 16 節點 8 節點中每個 Azure 的區域。 這些節點位於相同的叢集一定一般叢集名稱] 和 [種子節點設定。 若要測試叢集使用以下程序︰

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>步驟 1︰ 取得使用 PowerShell 的兩個區域的內部負載平衡器 IP
- 取得 AzureInternalLoadbalancer ServiceName 」 香港特別行政區-c-svc-西部-我們 」
- 取得 AzureInternalLoadbalancer ServiceName 」 香港特別行政區-c-svc-東亞-我們 」  

    請注意 IP 位址 (例如西部-10.1.2.101，東部-10.2.2.101) 顯示。

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>步驟 2︰ 執行下列命令中西部地區, 後登入香港特別行政區-w1-西部-我們
1.    執行 $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2.  執行下列 CQL 命令︰

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

您應該會看到類似以下的顯示︰

| customer_id | 名字 | [姓氏] |
| ----------- | --------- | -------- |
| 1           | 名字      | 為何      |
| 2           | 珍      | 為何      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>步驟 3︰ 執行下列命令，在東部區域中登入香港特別行政區-w1-東亞-我們之後︰
1.    執行 $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2.  執行下列 CQL 命令︰

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

如同西部區域，您應該會看到相同的顯示︰


| customer_id | 名字 | [姓氏]   |
|------------ | --------- | ---------- |
| 1           | 名字      | 為何        |
| 2           | 珍      | 為何        |


執行幾個更多插入並查看這些取得複寫到西部-我們叢集的部分。

## <a name="test-cassandra-cluster-from-nodejs"></a>從 Node.js 測試 Cassandra 叢集
使用其中一個 Linux Vm 建立在 [網站] 中的層先前，我們將會執行簡單的指令碼 Node.js 閱讀先前插入的資料

**步驟 1︰ 安裝 Node.js 和 Cassandra 用戶端**

1. 安裝 Node.js 和 npm
2. 安裝節點套件 cassandra-用戶端] 使用 npm
3. 執行下列指令碼，顯示擷取資料的 json 字串的命令介面提示︰

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>結論
Microsoft Azure 是彈性的平台可讓 Microsoft 以及開啟來源軟體執行依此練習中所示。 在單一資料中心透過叢集節點分配跨多個錯誤網域，可以部署高度可用 Cassandra 叢集。 也可以部署 Cassandra 叢集跨多個遠距 Azure 區域損毀證明系統。 Azure 和 Cassandra 一起可讓建構的彈性，高度可用，損毀可復原的雲端服務所需的今天網際網路不按比例縮放服務。  

##<a name="references"></a>參照##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
