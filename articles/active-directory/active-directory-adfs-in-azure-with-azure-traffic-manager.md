<properties
    pageTitle="Azure 與 Azure 流量管理員中的可用性跨地理 AD FS 部署 |Microsoft Azure"
    description="在這份文件中，您將學習如何為高可用性部署中 Azure AD FS。"
    keywords="Azure 流量管理員、 adfs Azure 流量管理員，地理、 多重資料中心、 地理資料中心、 多重地理資料中心時，使用 ad fs 部署中 azure AD FS 部署 azure adfs、 azure adfs azure ad fs、 部署 adfs、 部署 ad fs adfs 中 azure 部署 adfs azure 中的、 部署 AD FS 中 azure、 adfs azure AD FS、 Azure AD FS 中 Azure iaas 簡介ADFS，將 adfs 移至 azure"
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
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Azure 與 Azure 流量 Manager 中的可用性跨地理 AD FS 部署

[Azure AD FS 部署](active-directory-aadconnect-azure-adfs.md)提供逐步指導您如何部署組織 Azure 中的簡單的 AD FS 基礎結構。 本文提供建立 AD FS 跨地理部署 Azure 中的下一個步驟，使用[Azure 流量的管理員](../traffic-manager/traffic-manager-overview.md)。 Azure 流量 Manager 分開的地理位置模式高可用性和高效能 AD FS 基礎結構，為您的組織藉由使用路由可用的方法不同需求的基礎結構的範圍。

跨地理 AD FS 高度可用的基礎結構可讓︰

* **的單一的失敗︰**容錯移轉功能的 Azure 流量管理員，可以獲得高度可用的 AD FS 基礎結構即使地球的組件中的資料中心的其中一個當機
* **改良的效能︰**您可以使用本文中的建議的部署提供高效能 AD FS 基礎結構，可協助您更快速驗證的使用者。 

##<a name="design-principles"></a>設計原則

![整體設計](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

基本設計原則會列在設計原則，請參閱 Azure AD FS 部署與相同。 上述圖表會顯示基本部署至另一個地理區域的副檔名為簡單。 以下是 [延長您的部署新的地理區域時要考慮的一些事項

* **虛擬網路︰**您應該在您想要部署其他 AD FS 基礎結構的地理區域中建立新的虛擬網路。 在上圖中您查看 Geo1 VNET 和 Geo2 VNET 兩個虛擬網路中每個地理區域。

* **網域控制站與 AD FS 伺服器中新的地理 VNET:**建議您部署的網域控制站中新的地理區域以便在新的地區 AD FS 伺服器不需要連絡網域控制站遠另一個網路來完成驗證，藉此改善效能。

* **儲存帳戶︰**儲存帳戶的相關聯的區域。 由於您部署新的地理區域的電腦，您必須建立新的儲存空間帳戶使用區域中。  

* **網路安全性群組︰**儲存帳戶，請在區域中建立的網路安全性群組無法用於其他地理區域。 因此，您需要新的網路安全性群組與 INT 及 DMZ 子網路的第一個地理區域中建立新的地理區域。

* **DNS 的公用 IP 位址的標籤︰**結束點，可參閱 azure 流量管理員僅透過 DNS 標籤。 因此，您需要建立 DNS 外部負載平衡器的公用 IP 位址的標籤。

* **Azure 流量管理員︰**Microsoft Azure 流量管理員可讓您控制使用者流量至您在不同的資料中心世界各地的中執行的服務端點的通訊群組。 Azure 流量管理員運作 DNS 層級。 它會使用 DNS 回應，將使用者流量導向全域分散式結束點。 用戶端連線到這些端點直接。 使用不同的效能、 加權和優先順序的路由選項，您可以輕鬆地選擇最適合貴組織的需求路由選項。 

* **兩個區域之間的 V-淨至 V-網路連線︰**您不需要本身有虛擬網路之間的連線。 每個虛擬網路有權存取網域控制站，並在本身有 AD FS 和 WAP 伺服器，他們可以用於不同區域的虛擬網路之間的任何連線。 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Azure 流量 Manager 整合的步驟

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>部署新的地理區域中的 AD FS
請遵循的步驟及部署新的地理區域中相同的拓撲[中 Azure AD FS 部署](active-directory-aadconnect-azure-adfs.md)中的準則。

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>DNS 標籤的公用 IP 位址的網際網路對 （公開） 負載平衡器
如上所述，Azure 流量管理員只可以參考 DNS 標籤為端點，因此重要 DNS 為建立標籤外部負載平衡器的公用 IP 位址。 以下螢幕擷取畫面顯示如何設定 DNS 標籤的公用 IP 位址。 

![DNS 標籤](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>部署 Azure 流量管理員

請遵循下列步驟來建立流量管理員設定檔。 如需詳細資訊，您也可以參閱[管理 Azure 流量管理員設定檔](../traffic-manager/traffic-manager-manage-profiles.md)。

1. **建立流量的管理員設定檔︰**授與您流量的管理員設定檔的唯一名稱。 此設定檔的名稱 DNS 名稱的一部分，而作為前置字元的流量管理員網域名稱標籤。 名稱 / 前置詞新增至。 若要建立您的流量的 DNS 標籤管理員 trafficmanager.net。 以下螢幕擷取畫面顯示流量管理員被設定為 mysts DNS 字首，並產生 DNS 標籤會 mysts.trafficmanager.net。 

    ![流量管理員設定檔建立](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **傳輸路由的方法︰**有流量管理員中可用的三個路由選項︰

    * 優先順序 
    * 效能
    * 加權
    
    **效能**是為了要快速回應 AD FS 基礎結構的建議的選項。 不過，您可以選擇最適合您的部署需要任何路由方法。 AD FS 功能不會受到選取 [路由] 選項。 如需詳細資訊，請參閱[流量管理員流量路由方法](../traffic-manager/traffic-manager-routing-methods.md)。 在範例螢幕擷取畫面上方您可以看到選取的**效能**方法。
   
3.  **端點的設定︰**在 [流量管理員] 頁面中，按一下端點上，選取 [新增]。 這會開啟新增結束點] 頁面類似下面的螢幕擷取畫面
 
    ![設定結束點](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    不同的輸入值，請遵循以下指導方針︰

    **類型︰**我們會指向 Azure 的公用 IP 位址時，請選取 [Azure 結束點]。

    **名稱︰**建立您想要與端點關聯的名稱。 這不是 DNS 名稱，並沒有任何關係的 DNS 記錄。

    **目標資源類型︰**選取做為此屬性的值的公用 IP 位址。 

    **目標資源︰**這可讓您選擇不同的 DNS 標籤您在您的訂閱有可用的選項。 選擇 [以 DNS 標籤]。

    新增您想要將流量路由到 Azure 流量管理員每個地理區域的結束點。
    如需詳細資訊以及如何新增 / 設定流量管理員中的端點的詳細的步驟，請參閱[新增，請停用、 啟用或刪除端點](../traffic-manager/traffic-manager-endpoints.md)
    
4. **設定探查︰**在 [流量管理員] 頁面中，按一下設定。 在 [設定] 頁面中，您需要變更探查 HTTP 連接埠 80 和相對路徑 /adfs/probe 的監視器設定

    ![設定探查](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **確保的端點的狀態是 [ONLINE 一旦完成設定**。 如果所有的結束點 」 降級 」 的狀態，請 Azure 流量管理員會執行路由傳送流量，假設診斷不正確，而且所有端點都是連線到的最佳嘗試。

5. **修改 DNS 記錄的 Azure 流量 Manager:**您的同盟服務應該 CNAME Azure 流量管理員 DNS 名稱。 建立 CNAME 公用的 DNS 記錄中，只要嘗試連線到同盟服務實際到達 Azure 流量管理員。

    例如，若要指向同盟服務 fs.fabidentity.com 流量 Manager，您必須更新您的 DNS 資源記錄可能如下︰

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>測試路由與 AD FS 登入   

###<a name="routing-test"></a>路由測試

路由的基本測試是，請嘗試從電腦中每個地理區域的同盟服務 DNS 名稱偵測 （ping）。 根據選擇的路由方法，它實際上會偵測的端點將會反映在偵測 （ping） 顯示。 例如，如果您選取效能路由，然後最接近的用戶端地區端點會連接。 以下是從兩個不同區域的用戶端電腦的兩個 ping 快照、 EastAsia 區域中，一個西美國。 

![路由測試](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>登入測試 AD FS

若要測試 AD FS 最簡單的方法是使用 IdpInitiatedSignon.aspx 頁面。 若要能夠執行，則需要啟用 AD FS 屬性 IdpInitiatedSignOn。 請遵循下列步驟來確認您 AD FS 設定
 
1. 執行下方的 AD FS 伺服器上，將它設定為啟用使用 PowerShell 指令程式。 設定 AdfsProperties EnableIdPInitiatedSignonPage $true
2. 從任何外部電腦存取 https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. 您應該會看到 [AD FS 頁面下方等︰

    ![在 ADFS 測試-驗證挑戰](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    然後，在成功登入時，它會提供您成功的訊息如下所示︰

    ![在 ADFS 測試-驗證成功](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>相關的連結
* [Azure 中的基本 AD FS 部署](active-directory-aadconnect-azure-adfs.md)
* [Microsoft Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)
* [流量管理員流量路由方法](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>後續步驟
* [管理 Azure 流量管理員設定檔](../traffic-manager/traffic-manager-manage-profiles.md)
* [新增、 停用、 啟用或刪除端點](../traffic-manager/traffic-manager-endpoints.md) 

