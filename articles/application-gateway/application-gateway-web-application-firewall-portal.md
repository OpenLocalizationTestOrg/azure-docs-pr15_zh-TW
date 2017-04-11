<properties
   pageTitle="建立 web 應用程式防火牆使用入口網站應用程式閘道 |Microsoft Azure"
   description="瞭解如何使用 web 應用程式防火牆建立應用程式閘道器，使用入口網站"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>建立 web 應用程式防火牆使用入口網站應用程式閘道器

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-web-application-firewall-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure 應用程式的閘道器的 web 應用程式防火牆 (WAF) 可從網頁的常見攻擊，例如 SQL 插入、 跨網站指令碼攻擊和工作階段倘若保護 web 應用程式。 Web 應用程式會防止許多 OWASP 前 10 個常見網頁的弱點。

Azure 應用程式閘道是有 7 層負載平衡器。 是否位於雲端或內部部署，它會提供容錯移轉時，效能路由 HTTP 要求之間不同的伺服器。
應用程式提供許多應用程式傳送控制器 (ADC) 的功能，包括 HTTP 負載平衡、 cookie 為基礎的工作階段相關性、 安全通訊端層 (SSL) 卸載自訂健康探查、 支援多重網站和許多其他。
若要尋找的受支援功能的完整清單，請造訪[應用程式閘道器概觀](application-gateway-introduction.md)

## <a name="scenarios"></a>案例

本文中有兩種案例︰

在第一個案例中，您瞭解如何[新增至現有的應用程式閘道器的 web 應用程式防火牆](#add-web-application-firewall-to-an-existing-application-gateway)。

在第二個案例中，您瞭解如何[建立 web 應用程式防火牆應用程式閘道](#create-an-application-gateway-with-web-application-firewall)

![案例範例][scenario]

>[AZURE.NOTE] 其他設定的應用程式的閘道器，包括自訂健康探查、 應用程式閘道器設定之後，而不是在初次部署設定後端資料庫地址及其他規則。

## <a name="before-you-begin"></a>開始之前

Azure 應用程式閘道需要它自己的子網路。 在建立虛擬網路時，請確定您離開地址空間不足，無法有多個子網路。 一旦您部署子網路應用程式閘道器時，只其他應用程式的閘道器都能新增到子網路。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>將 web 應用程式防火牆新增至現有的應用程式閘道器

這種情況更新現有的應用程式閘道器至支援 web 應用程式的防火牆，以防止模式。

### <a name="step-1"></a>步驟 1

瀏覽至 Azure 入口網站中，選取 [使用現有的應用程式閘道。

![建立應用程式的閘道器][1]

### <a name="step-2"></a>步驟 2

按一下 [**設定**]，並更新應用程式的閘道器設定。 完成時請按一下 [**儲存**

若要更新現有的應用程式閘道器至支援 web 應用程式防火牆設定為︰

- 必須**WAF**支援 web 應用程式防火牆**層**選取層。
- **SKU 大小**這項設定 web 應用程式防火牆應用程式閘道的大小，可用的選項會 （**中型**和**大型**）。
- **防火牆狀態**此設定會停用或啟用 web 應用程式防火牆。
- **防火牆模式**這項設定是 web 應用程式防火牆惡意流量的處理方式。 **偵測**模式只會記錄的事件，**防止**模式的事件記錄及惡意流量停駐點的位置。

![刀顯示基本設定][2]

>[AZURE.NOTE] 若要檢視 web 應用程式防火牆記錄，您必須啟用診斷和 ApplicationGatewayFirewallLog 選取。 供測試之用，可以選擇的執行個體計數為 1。 請務必知道下兩個執行個體的任何執行個體計數不涵蓋 SLA，因此不建議。 使用 web 應用程式防火牆時，小型的閘道器無法使用。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>建立 web 應用程式防火牆應用程式閘道器

將這種情況︰

- 建立兩個執行個體中型 web 應用程式防火牆應用程式閘道器。
- 建立名為 AdatumAppGatewayVNET 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
- 建立名為其 CIDR 區塊使用 10.0.0.0/28 的 Appgatewaysubnet 子網路。
- 設定 SSL 卸載的憑證。

### <a name="step-1"></a>步驟 1

瀏覽至 Azure 入口網站，按一下 [**新增** > **網路** > **應用程式的閘道器**

![建立應用程式的閘道器][1-1]

### <a name="step-2"></a>步驟 2

接著填寫基本應用程式閘道器的相關資訊。 請務必選擇**WAF**與層。 完成時按一下**[確定]**

基本設定所需的資訊是︰

- **名稱**-應用程式閘道器的名稱。
- **層**的層的應用程式閘道器，可用的選項會 （**標準**和**WAF**）。 Web 應用程式防火牆只適用於 WAF 層。
- **SKU 大小**這項設定為應用程式閘道器的大小，可用的選項 （**中型**和**大型**）。
- **執行個體計數**-執行個體數目，此值應該是**2**和**10**之間的數字。
- **資源群組**儲存應用程式閘道器的 [資源] 群組可現有的資源群組或新 id。
- **位置**應用程式閘道器的地區是在 [資源] 群組的同一個位置。 *位置重要作為虛擬網路，且公用 IP 必須在閘道器的相同位置*。

![刀顯示基本設定][2-2]

>[AZURE.NOTE] 供測試之用，可以選擇的執行個體計數為 1。 請務必知道下兩個執行個體的任何執行個體計數不涵蓋 SLA，因此不建議。 小型的閘道不支援的 web 應用程式防火牆案例。

### <a name="step-3"></a>步驟 3

基本設定的定義後下, 一步是定義要使用的虛擬網路。 虛擬網路儲存了應用程式閘道器會負載平衡的應用程式。

按一下 [**選擇虛擬網路**設定虛擬網路。

![顯示應用程式的閘道器設定刀][3]

### <a name="step-4"></a>步驟 4

在 [**選擇虛擬網路**刀中，按一下 [**建立新**

時不說明在這個案例中，無法在此時選取現有的虛擬網路。  如果使用現有的虛擬網路時，很重要事項虛擬網路需要的空白子網路] 或 [僅限應用程式閘道器資源使用子網路。

![選擇 [虛擬網路刀][4]

### <a name="step-5"></a>步驟 5

填寫網路中的資訊**建立虛擬網路**刀至上一個[案例](#scenario)說明中所述。

![建立虛擬網路刀輸入的資訊][5]

### <a name="step-6"></a>步驟 6

虛擬網路建立後下, 一步是定義應用程式閘道器的前端 IP。 此時，選擇是公用或私用的 IP 位址的前端之間。 選擇取決於應用程式為網際網路具或內部只。 此案例假設使用的公用 IP 位址。 若要選擇 [私用的 IP 位址，可以按一下 [**私人**] 按鈕。 選擇 [自動指派的 IP 位址或您可以按一下 [**選擇特定的私人 IP 位址**核取方塊，以手動輸入。

按一下 [**選擇的公用 IP 位址**。 如果使用現有的公用 IP 位址此時選擇，您可以在這個案例中建立新的公用 IP 位址。 按一下 [**建立新**

![選擇 [公用 IP 位址刀][6]

### <a name="step-7"></a>步驟 7

接下來提供的公用 IP 位址的易記的名稱，然後按一下**[確定]**

![建立公用 IP 位址刀][7]

### <a name="step-8"></a>步驟 8

接下來，您可以設定接聽程式設定。  如果使用**http** ，則不會從左到設定，可以按一下**[確定]** 。 若要使用**https**進一步不需要設定。

若要使用**https**，是必要的憑證。 不需要私密金鑰的憑證，因此.pfx 匯出憑證需求提供和檔案的密碼。

按一下 [ **HTTPS**，按一下**上傳 PFX 憑證**文字方塊旁的**資料夾**圖示。
瀏覽至您的檔案系統.pfx 憑證檔案。 選取後，為憑證好記的名稱，然後輸入.pfx 檔密碼]。

完成後按一下**[確定**]，檢閱應用程式閘道器設定。

![設定刀上接聽組態] 區段][8]

### <a name="step-9"></a>步驟 9

設定**WAF**特定設定。

- **防火牆狀態**此設定會 WAF 開啟或關閉。
- **防火牆模式**此設定會決定動作 WAF 會採用惡意流量。 如果選擇**偵測**，只會記錄流量。  如果選擇**防止**，流量登入，而停止與 403 未經授權。

![web 應用程式防火牆設定][9]

### <a name="step-10"></a>步驟 10

檢閱摘要頁面，然後按一下**[確定**]。  現在是佇列應用程式閘道器，然後建立。

### <a name="step-12"></a>步驟 12

一旦建立應用程式閘道器，瀏覽至其在入口網站，以繼續應用程式閘道器設定。

![應用程式閘道器資源檢視][10]

這些步驟建立基本的應用程式的閘道器接聽、 後端資料庫、 後端 http 設定與規則的預設設定。 您可以修改以符合您的部署成功佈建後這些設定

## <a name="next-steps"></a>後續步驟

瞭解如何設定診斷記錄]，瀏覽[應用程式閘道器診斷](application-gateway-diagnostics.md)登 Web 應用程式防火牆會偵測到或無法的事件

瞭解如何建立自訂的狀況檢查瀏覽[建立自訂的狀況檢查](application-gateway-create-probe-portal.md)

瞭解如何設定 SSL 卸載及瀏覽[設定 SSL 卸載](application-gateway-ssl-portal.md)採取成本 SSL 解密關閉您的網頁伺服器

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png