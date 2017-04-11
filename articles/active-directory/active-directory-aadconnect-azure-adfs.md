<properties
    pageTitle="在 [Azure active Directory Federation Services |Microsoft Azure"
    description="在這份文件中，您將學習如何為高可用性部署中 Azure AD FS。"
    keywords="部署中 azure AD FS 部署 azure adfs、 azure adfs azure ad fs、 部署 adfs、 部署 ad fs adfs 中 azure 部署 adfs azure 中的、 部署 AD FS azure、 adfs azure AD FS、 Azure AD FS 中 Azure iaas，ADFS，簡介中的移動至 azure adfs"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>Azure AD FS 部署 

AD FS 提供簡化、 受到保護的識別同盟及 Web 單一登入 (SSO) 功能。 Azure AD 同盟或 O365 可讓使用者使用內部部署認證項目會驗證並存取雲端中的所有資源。 如此一來，它會變成有高度可用的 AD FS 基礎結構，以確保資源的存取權的兩個內部部署和雲端中。 部署中 Azure AD FS，可協助達成可用性需要最小的效用。
有數種優點部署中 Azure AD FS，其中一些如下所示︰

* **可用性**-的 Azure 可用性集]，以確定高度可用的基礎結構。
* **縮放容易**– 需要更多的效能？ 輕鬆地將移轉到功能更強大的機器 Azure 中只需要幾個按下滑鼠按鈕
* **跨地理重複**– 使用 Azure 地理重複您可以確定全球的基礎結構的高度可用性
* **容易管理**– 強烈簡化的管理選項，在 Azure 入口網站管理您的基礎結構就很容易及困擾 

## <a name="design-principles"></a>設計原則

![部署設計](./media/active-directory-aadconnect-azure-adfs/deployment.png)

上述圖表會顯示建議的基本拓撲啟動部署中 Azure AD FS 基礎結構。 以下列出背後拓撲的各種元件的原則︰

* **DC / ADFS 伺服器**︰ 如果您有少於 1000 個使用者便可直接在您的網域控制站安裝 AD FS 角色。 如果您不想在網域控制站的任何效能影響，或者如果您有 1000 個以上的使用者，然後部署 AD FS 不同的伺服器上。
* **WAP 伺服器**– 讓使用者可以連絡的 AD FS 時，不是在公司內部網路有必要部署 Web 應用程式 Proxy 伺服器。
* **DMZ**: Web 應用程式 Proxy 伺服器會放在 DMZ，而且只 TCP/443 准許 DMZ 和內部子網路之間。
* **負載平衡器**︰ 若要確保可用性 AD FS 和 Web 應用程式 Proxy 伺服器，建議您使用內部負載平衡器 AD FS 伺服器和 Azure 負載平衡器的 Web 應用程式 Proxy 伺服器。
* **可用性集**︰ 提供重複性給 AD FS 部署，建議您群組的兩個或多個虛擬機器中類似負載可用性設定。 此設定可確保期間請計畫之內進行的維修作業事件，至少有一個虛擬機器會提供
* **儲存帳戶**︰ 建議您是在有兩個儲存的帳戶。 沒有單一儲存帳戶建立單一的失敗，可能會導致，可能會導致無法在可能的案例中儲存帳戶當機部署。 兩個儲存帳戶可協助您建立一個儲存帳戶，為每個錯誤行之間的關聯。
* **網路責任分隔**︰ Web 應用程式 Proxy 伺服器應該部署在個別的 DMZ 網路。 您可以將一個虛擬網路分割成兩個子網路，然後再部署隔離的子網路中的 Web 應用程式 Proxy 伺服器。 您只可以網路安全性設定] 群組中的每一個子網路，並允許兩個子網路所需的溝通。 每個部署案例下提供更多詳細資料

##<a name="steps-to-deploy-ad-fs-in-azure"></a>若要部署 AD FS Azure 中的步驟

本節所述的步驟建立大綱部署指南所述的 Azure AD FS 基礎結構] 下方。

### <a name="1-deploying-the-network"></a>1.部署網路

如以上所述，您可以為單一的虛擬網路中建立兩個子網路，或是建立兩個完全不同虛擬網路 (VNet)。 本文將著重於部署單一虛擬網路，並分成兩個子網路。 這是目前簡便的方法，因為兩個不同的 VNets 需要 VNet 閘道 VNet 通訊。

**1.1 （英文） 建立虛擬網路**

![建立虛擬網路](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
在 Azure 入口網站中，選取虛擬網路，您可以部署虛擬網路和立即使用只要按一下子網路。 INT 子網路也會定義且已準備好現在要新增 Vm。
下一步是新增另一個子網路網路時，也就是 DMZ 子網路。 若要建立 DMZ 子網路，只要

* 選取新建立的網路
* 屬性中選取 [子網路
* 在子網路面板] 按一下 [新增] 按鈕
* 提供建立子網路的子網路名稱與地址空間資訊

![子網路](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![子網路 DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2。 建立網路安全性群組**

網路安全性群組 (NSG) 包含允許或拒絕網路流量至您 VM 中執行個體虛擬網路存取控制清單 (ACL) 規則清單。 NSGs 可與子網路或子網路中的個別 VM 執行個體相關聯。 NSG 與子網路相關聯時，ACL 規則套用至子網路中的所有 VM 執行個體。
為了本指南中，我們將會建立兩個 NSGs︰ 一個每個內部網路和 DMZ。 他們會標示 NSG_INT 和 NSG_DMZ 分別。

![建立 NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

建立 NSG 後，會有 0 輸入和 0 輸出規則。 一旦在個別的伺服器上的角色安裝且正常運作，然後輸入與輸出規則可根據所要的安全性層級。

![初始化 NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

建立 NSGs 之後，將子網路與關聯 NSG_INT INT 和使用子網路 DMZ NSG_DMZ。 範例螢幕擷取畫面如下所示︰

![NSG 設定](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* 按一下 [開啟的面板子網路的子網路上
* 選取關聯 NSG 子網路 

設定之後，子網路的面板看起來應該像下面︰

![NSG 後的子網路](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3。 建立連線至內部部署**

我們需要連線至內部部署才能部署 azure 中的網域控制站 (DC)。 Azure 提供各種不同的連線選項連線 Azure 基礎結構的內部部署基礎結構。

* 點-網站
* 虛擬網路-網站
* ExpressRoute

建議使用 ExpressRoute。 ExpressRoute 可讓您私人之間建立關聯 Azure 資料中心，在您的內部部署或代管環境中的基礎結構。 ExpressRoute 連線，請勿繼續進行公用網際網路上。 他們在網際網路上提供更多的可靠性、 更快的速度，較低的延遲和較高的安全性比一般的連線。
建議使用 ExpressRoute，而您可以選擇最適合貴組織的任何連線方式。 若要進一步瞭解關於 ExpressRoute 及各種不同的連線選項使用 ExpressRoute，閱讀[ExpressRoute 技術概觀](https://aka.ms/Azure/ExpressRoute)。

### <a name="2-create-storage-accounts"></a>2.建立儲存帳戶

為了維護可用性並避免在單一儲存的帳戶的相依性，您可以建立兩個儲存帳戶。 分成兩個群組中的每個可用性集合中的電腦，然後將每個群組指派不同的儲存帳戶。

![建立儲存帳戶](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3.建立可用性集

每一個角色 （亦即/AD FS 和 WAP），建立包含 2 機器每個最小的可用性集。 這有助於獲得更高的可用性每一個角色。 建立可用性組時很重要決定使用哪種動作︰
* **故障網域**︰ 虛擬機器中相同的錯誤網域共用相同的電源和實體網路切換。 建議的 2 故障網域最小值。 預設值為 3，讓它為只是為了將此部署
* **更新網域**︰ 屬於相同的更新網域的電腦都必須重新啟動一起更新時。 您想要有 2 更新網域的最小值。 預設值為 5，讓它為只是為了將此部署

![可用性集](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

建立下列可用性

| 顯示狀態設定 | 角色 | 故障網域 | 更新的網域 |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | 在 ADFS DC / | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4.部署虛擬機器
下一步是部署虛擬機器裝載您的基礎結構的不同角色。 在每個可用性設定建議的兩個電腦的最小值。 建立基本的部署的六個虛擬機器。

| 電腦 | 角色 | 子網路 | 顯示狀態設定 | 儲存帳戶 | IP 位址 |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|在 ADFS DC /|INT|contosodcset|contososac1|靜態|
|contosodc2|在 ADFS DC /|INT|contosodcset|contososac2|靜態|
|contosowap1|WAP|DMZ|contosowapset|contososac1|靜態|
|contosowap2|WAP|DMZ|contosowapset|contososac2|靜態|

您可能已經注意到，指定沒有 NSG。 這是因為 azure 可讓您使用 NSG 子網路層級。 然後，您也可以使用 [子網路，否則 NIC 物件相關聯的個別 NSG 控制電腦網路流量。 閱讀更多上的[什麼是網路安全性群組 (NSG)](https://aka.ms/Azure/NSG)。
如果您管理 DNS，建議使用靜態 IP 位址。 您可以使用 Azure DNS 並改為在您網域的 DNS 記錄，請參閱依其 Azure Fqdn 的新電腦。
您的虛擬機器窗格看起來應該像下面部署完畢之後︰

![部署的虛擬機器](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5.設定網域控制站 / AD FS 伺服器
 驗證任何內送的要求，才能 AD FS 就必須連絡網域控制站。 若要驗證的內部部署 DC azure 儲存成本出差，建議部署 Azure 中的網域控制站的複本。 為了得到可用性，建議建立最小的 2 的網域控制站的可用性組合。

|網域控制站|角色|儲存帳戶|
|:-----:|:-----:|:-----:|
|contosodc1|複本|contososac1|
|contosodc2|複本|contososac2|

* 將兩個伺服器升階為複本網域控制站 dns
* 安裝 AD FS 角色使用伺服器管理員來設定 AD FS 伺服器。

###<a name="6---deploying-internal-load-balancer-ilb"></a>6。 部署內部負載平衡器 (ILB)

**6.1。 建立 ILB**

若要部署 ILB，選取負載平衡器中 Azure 入口網站和中的，按一下 [新增 （+）。
>[AZURE.NOTE] 如果您沒有看到**負載平衡器**您] 功能表中，，按一下 [**瀏覽**入口網站與捲軸的左下角，直到您看到**負載平衡器**。  然後按一下 [黃色星形以將其新增至您的功能表。 現在，選取新的負載平衡器圖示以開啟 [開始設定負載平衡器] 面板。

![瀏覽負載平衡器](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **名稱**︰ 給負載平衡器中的任何適當的名稱
* **配置**︰ 由於此負載平衡器會放在前面 AD FS 伺服器，而是內部網路的連線，選取 [內部 」
* **虛擬網路**︰ 選擇您要在其中部署您 AD FS 虛擬網路
* **子網路**︰ 選擇內部子網路
* **IP 位址工作分派**︰ 動態

![內部負載平衡器](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
按一下之後建立並部署 ILB 時，您應該會看到它的負載平衡器清單中︰

![ILB 後的負載平衡器](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
下一步是設定後端資料庫與後端探查。

**6.2。 設定 ILB 後端資料庫**

選取新建立的 ILB 面板中的負載平衡器。 隨即會開啟 [設定] 面板。 
1.  選取後端資料庫，從 [設定] 面板
2.  在 [新增後端資料庫] 面板中，按一下 [上新增虛擬機器
3.  您會顯示您可以在其中選擇可用性設定的面板
4.  選擇 [AD FS 可用性設定

![設定 ILB 後端資料庫](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3。 設定探查**

在 ILB [設定] 面板中，選取 [檢查]。
1.  在上的按一下 [新增]
2.  提供探查的詳細資料。 **名稱**︰ 探查名稱 b。 **通訊協定**︰ TCP c。 **連接埠**︰ 443 (HTTPS) d。 **間隔**︰ 5 （預設值）︰ 這是用以 ILB 將探查後端資料庫 e 中的機器的時間間隔。 **不佳的臨界值限制**︰ 2 （預設值 val 舒慧）-這是臨界值的連續探查失敗次數之後 ILB 會宣告後端資料庫中的電腦沒有回應，停止傳送流量。

![設定 ILB 探查](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4。 建立負載平衡規則**

以有效餘額流量，ILB 應該設定負載平衡規則。 若要建立負載平衡規則， 
1.  選取負載平衡 ILB 的 [設定] 面板中的規則
2.  按一下 [加入負載平衡規則] 面板中
3.  在 [新增負載平衡規則] 面板中。 **名稱**︰ 提供 b 規則的名稱。 **通訊協定**︰ 選取 [TCP c。 **連接埠**︰ 443 d。 **後端連接埠**︰ 443 e。 **後端資料庫**︰ 選取您建立的 AD FS 叢集舊版 f 集區。 **探查**︰ 選取先前建立的 AD FS 伺服器探查

![設定 ILB 平衡規則](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5。 更新 ILB DNS**

移至您的 DNS 伺服器，並建立 ILB CNAME。 CNAME 應該同盟服務指向 ILB 的 IP 位址的 IP 位址。 例如，如果 ILB DIP 地址是 10.3.0.8，與同盟服務安裝方法，是 fs.contoso.com，然後建立指向 10.3.0.8 fs.contoso.com 的 CNAME。
這樣就能確定 fs.contoso.com 結束有關的所有通訊設定在 ILB 和傳閱適當。

###<a name="7---configuring-the-web-application-proxy-server"></a>7。 設定 Web 應用程式 Proxy 伺服器

**7.1。 設定 Web 應用程式 Proxy 伺服器達到 AD FS 伺服器**

若要確保能夠使用 AD FS 之後伺服器 ILB Web 應用程式 Proxy 伺服器，記錄版中建立 %systemroot%\system32\drivers\etc\hosts ILB。 請注意辨別的名稱應該同盟服務名稱，例如 fs.contoso.com。 而且 IP 項目應該的 ILB 的 IP 位址 (10.3.0.8 如範例所示)。

**7.2。 安裝 Web 應用程式 Proxy 角色**

您確定 Web 應用程式 Proxy 伺服器是能夠使用 AD FS 之後伺服器 ILB 之後，您接下來可以安裝 Web 應用程式 Proxy 伺服器。 Web 應用程式 Proxy 伺服器未加入網域。 安裝 Web 應用程式 Proxy 角色在兩個 Web 應用程式 Proxy 伺服器上，選取 [遠端存取角色。 伺服器管理員會引導您完成 WAP 安裝。
如何部署 WAP 的詳細資訊，請參閱[安裝及設定 Web 應用程式 Proxy 伺服器](https://technet.microsoft.com/library/dn383662.aspx)。

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8。 部署對 （公開） 負載平衡器網際網路

**8.1。 建立網際網路圖示 （公開） 負載平衡器**
 
在 Azure 入口網站中，選取負載平衡器，然後按一下 [新增]。 在 [建立負載平衡器] 面板中，輸入下列資訊
1. **名稱**︰ 負載平衡器名稱
2. **配置**︰ 公用 – 此選項會告訴 Azure 此負載平衡器需要公用的地址。
3. **IP 位址**︰ 建立新的 IP 位址 （動態）

![網際網路具負載平衡器](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

部署之後，負載平衡器會出現在負載平衡器清單。

![負載平衡器清單](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2。 指定的公用 IP DNS 標籤**

按一下以顯示 [設定] 面板的 [負載平衡器] 面板中的新建立的負載平衡器項目。 請遵循下列步驟來設定 DNS 標籤的公用 IP:
1.  按一下 [上的公用 IP 位址。 這會開啟 [控制台的公用 IP 和它的設定
2.  按一下 [設定
3.  提供 DNS 標籤。 這會成為您可以從任何地方，例如 contosofs.westus.cloudapp.azure.com 存取的公用 DNS 標籤。 您可以在外部 DNS 解析為外部負載平衡器 (contosofs.westus.cloudapp.azure.com) 的 [DNS] 標籤同盟服務 （例如 fs.contoso.com) 中新增項目。

![設定網際網路對負載平衡器](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![設定網際網路對負載平衡器 (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3。 針對網際網路對 （公開） 負載平衡器設定後端資料庫** 

請依照建立內部負載平衡器，以針對網際網路對 （公開）] 的負載平衡器設定後端資料庫，可用性 WAP 伺服器設定為相同的步驟。 例如，contosowapset。

![設定網際網路面對負載平衡器的後端資料庫](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8.4。 設定探查**

請遵循設定來設定 WAP 伺服器的後端資料庫的探查內部負載平衡器相同的步驟。

![設定探查網際網路對負載平衡器](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8.5。 建立負載平衡規則**

請遵循設定負載平衡規則的 TCP 443 ILB 相同的步驟。

![設定網際網路對負載平衡器平衡規則](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9。 保護網路

**9.1。 保護內部子網路**

整體來說，您需要下列規則以有效率地保護您的內部子網路 （依如下所示）

|規則|描述|流程|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| 允許 DMZ HTTPS 通訊 | 連入 |
|DenyInternetOutbound| 不能存取網際網路 | 輸出 |

![INT 存取規則 （輸入）](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[註解]: <> (![INT 存取規則 （輸入）](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [註解]: <> (![INT 存取規則 （輸出）](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2。 保護 DMZ 子網路**

|規則|描述|流程|
|:----|:----|:------:|
|AllowHTTPSFromInternet| 從網際網路] 允許 DMZ HTTPS | 連入|
|DenyInternetOutbound|  網際網路 HTTPS 以外的項目遭到封鎖 | 輸出 |

![分機存取規則 （輸入）](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[註解]: <> (![分機存取規則 （輸入）](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [註解]: <> (![分機存取規則 （輸出）](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] 如果使用者用戶端憑證驗證 (使用 X509 clientTLS 驗證使用者憑證) 是必要的然後 AD FS 需要 TCP 連接埠 49443 啟用連入存取。

###<a name="10--test-the-ad-fs-sign-in"></a>10.測試 AD FS 登入

最簡單的方法是以測試 AD FS 使用 IdpInitiatedSignon.aspx 頁面。 若要能夠執行，則需要啟用 AD FS 屬性 IdpInitiatedSignOn。 請遵循下列步驟來確認您 AD FS 設定
1.  執行下方的 AD FS 伺服器上，將它設定為啟用使用 PowerShell 指令程式。
    設定 AdfsProperties EnableIdPInitiatedSignonPage $true 
2.  從任何外部電腦存取 https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3.  您應該會看到 [AD FS 頁面下方等︰

![測試登入頁面](./media/active-directory-aadconnect-azure-adfs/test1.png)

在成功登入時，它會提供您成功的訊息如下所示︰

![測試成功](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>部署 AD FS Azure 中的範本

範本部署 6 電腦設定，2 每個網域控制站、 AD FS 和 WAP。

[Azure 部署範本中的 AD FS](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

您可以使用現有的虛擬網路或時部署此範本建立新的 VNET。 可用來自訂部署的各種參數如下所示的部署程序中的參數的使用狀況的描述。 

| 參數 | 描述 |
|:--------|:-----|
|位置| 要部署資源至，例如東亞我們的區域。 |
|StorageAccountType| 建立儲存帳戶類型|
|VirtualNetworkUsage| 指出是否將會建立新的虛擬網路，或使用現有|
|VirtualNetworkName| 若要建立強制現有或新增虛擬網路流量虛擬網路的名稱|
|VirtualNetworkResourceGroupName| 指定現有的虛擬網路的所在位置的 [資源] 群組的名稱。 當使用現有的虛擬網路，這會變成強制的參數，讓部署可以找到現有的虛擬網路的識別碼|
|VirtualNetworkAddressRange| 新的 VNET，則為必要建立新的虛擬網路位址範圍|
|InternalSubnetName| 的內部子網路名稱，為兩個虛擬網路使用選項 （新的或現有的）|
|InternalSubnetAddressRange| 如果建立新的虛擬網路包含網域控制站和 ADFS 伺服器，強制的內部子網路位址範圍。|
|DMZSubnetAddressRange| 地址 dmz 子網路，其中包含的 Windows 應用程式 proxy 伺服器，強制如果建立新的虛擬網路的範圍。|
|DMZSubnetName| 內部子網路，強制兩個虛擬網路使用選項 （新的或現有的） 的名稱。 |
|ADDC01NICIPAddress| 第一個網域控制站的內部 IP 位址，這個 IP 位址會固定指派給，亦即，必須是內部子網路內有效的 ip 位址|
|ADDC02NICIPAddress| 第二個網域控制站的內部 IP 位址，這個 IP 位址會固定指派給，亦即，必須是內部子網路內有效的 ip 位址|
|ADFS01NICIPAddress| 第一個在 ADFS 伺服器的內部 IP 位址，這個 IP 位址會固定指派給在 ADFS 伺服器，必須是內部子網路內有效的 ip 位址|
|ADFS02NICIPAddress| 第二個 ADFS 伺服器的內部的 IP 位址，這個 IP 位址會固定指派給在 ADFS 伺服器，必須是內部子網路內有效的 ip 位址|
|WAP01NICIPAddress| 第一個 WAP 伺服器的內部的 IP 位址，這個 IP 位址會固定指派給 WAP 伺服器，必須是有效的 ip 位址 DMZ 子網路中|
|WAP02NICIPAddress| 第二個 WAP 伺服器的內部的 IP 位址，這個 IP 位址會固定指派給 WAP 伺服器，必須 DMZ 子網路中的有效的 ip 位址|
|ADFSLoadBalancerPrivateIPAddress| ADFS 負載平衡器內部 IP 位址，這個 IP 位址會固定指派給負載平衡器，以及必須是內部子網路內有效的 ip 位址|
|ADDCVMNamePrefix| 網域控制站的虛擬機器名稱前置詞|
|ADFSVMNamePrefix| 在 ADFS 伺服器的虛擬機器名稱前置字元|
|WAPVMNamePrefix| 虛擬機器 WAP 伺服器的名稱前置字元|
|ADDCVMSize| 網域控制站的虛擬記憶體大小|
|ADFSVMSize| 在 ADFS 伺服器的虛擬記憶體大小|
|WAPVMSize| WAP 伺服器的虛擬記憶體大小|
|AdminUserName| 虛擬機器本機系統管理員的名稱|
|AdminPassword| 虛擬機器本機系統管理員帳戶的密碼|

## <a name="additional-resources"></a>其他資源
* [可用性集](https://aka.ms/Azure/Availability ) 
* [Azure 負載平衡器](https://aka.ms/Azure/ILB)
* [內部負載平衡器](https://aka.ms/Azure/ILB/Internal)
* [網際網路面對負載平衡器](https://aka.ms/Azure/ILB/Internet)
* [儲存帳戶](https://aka.ms/Azure/Storage )
* [Azure 虛擬網路](https://aka.ms/Azure/VNet)
* [AD FS 和應用程式 Proxy 連結](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>後續步驟

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
* [設定和管理您的 AD FS 使用 Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Azure 與 Azure 流量管理員中的可用性跨地理 AD FS 部署](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




