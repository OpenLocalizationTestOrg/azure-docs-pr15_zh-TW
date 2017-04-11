<properties
   pageTitle="建立使用入口網站應用程式閘道器 |Microsoft Azure"
   description="瞭解如何建立應用程式閘道使用入口網站"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
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

# <a name="create-an-application-gateway-by-using-the-portal"></a>使用入口網站建立的應用程式閘道器

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-gateway-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-gateway-arm.md)
- [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
- [Azure 資源管理員範本](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是有 7 層負載平衡器。 是否位於雲端或內部部署，它會提供容錯移轉時，效能路由 HTTP 要求之間不同的伺服器。 應用程式的閘道器提供許多的應用程式傳送控制器 (ADC) 的功能，包括 HTTP 負載平衡、 cookie 為基礎的工作階段相關性、 安全通訊端層 (SSL) 卸載自訂健康探查、 支援多重網站和許多其他。 若要尋找的受支援功能的完整清單，請造訪[應用程式閘道器概觀](application-gateway-introduction.md)

## <a name="scenario"></a>案例

在此案例中，您可以瞭解如何建立使用 Azure 入口網站應用程式閘道器。

將這種情況︰

- 建立兩個執行個體中的應用程式的閘道器。
- 建立名為 AdatumAppGatewayVNET 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
- 建立名為其 CIDR 區塊使用 10.0.0.0/28 的 Appgatewaysubnet 子網路。
- 設定 SSL 卸載的憑證。

![案例範例][scenario]

>[AZURE.IMPORTANT] 其他設定的應用程式的閘道器，包括自訂健康探查、 應用程式閘道器設定之後，而不是在初次部署設定後端資料庫地址及其他規則。

## <a name="before-you-begin"></a>開始之前

Azure 應用程式閘道需要它自己的子網路。 在建立虛擬網路時，請確定您離開地址空間不足，無法有多個子網路。 一旦您部署子網路應用程式閘道器時，只其他應用程式的閘道器都能新增到子網路。

## <a name="create-the-application-gateway"></a>建立應用程式閘道器

### <a name="step-1"></a>步驟 1

瀏覽至 Azure 入口網站，按一下 [**新增** > **網路** > **應用程式的閘道器**

![建立應用程式的閘道器][1]

### <a name="step-2"></a>步驟 2

接著填寫基本應用程式閘道器的相關資訊。 完成時按一下**[確定]**

基本設定所需的資訊是︰

- **名稱**-應用程式閘道器的名稱。
- **層**-這是應用程式閘道器層。 兩個層可**WAF**和**標準**。 WAF 可讓 web 應用程式防火牆功能。
- **SKU 大小**這項設定為應用程式閘道器的大小，可用的選項 （**小型**、**中型**企業版和**大**）。 *選擇 [WAF 層時，無法使用小型企業*
- **執行個體計數**-執行個體數目，此值應該是 2 和 10 之間的數字。
- **資源群組**儲存應用程式閘道器的 [資源] 群組可現有的資源群組或新 id。
- **位置**應用程式閘道器的地區是在 [資源] 群組的同一個位置。 *位置重要作為虛擬網路，且公用 IP 必須在閘道器的相同位置*。

![刀顯示基本設定][2]

>[AZURE.NOTE] 供測試之用，可以選擇的執行個體計數為 1。 請務必知道下兩個執行個體的任何執行個體計數不涵蓋 SLA，因此不建議。 小型的閘道器會針對開發測試而非生產用途使用。

### <a name="step-3"></a>步驟 3

基本設定的定義後下, 一步是定義要使用的虛擬網路。 虛擬網路儲存了應用程式閘道器會負載平衡的應用程式。

按一下 [**選擇虛擬網路**設定虛擬網路。

![顯示應用程式的閘道器設定刀][3]

### <a name="step-4"></a>步驟 4

在 [*選擇虛擬網路*刀中，按一下 [**建立新**

時不說明在這個案例中，無法在此時選取現有的虛擬網路。  如果使用現有的虛擬網路時，很重要事項虛擬網路需要的空白子網路] 或 [僅限應用程式閘道器資源使用子網路。

![選擇 [虛擬網路刀][4]

### <a name="step-5"></a>步驟 5

填寫網路中的資訊**建立虛擬網路**刀至上一個[案例](#scenario)說明中所述。

![建立虛擬網路刀輸入的資訊][5]

### <a name="step-6"></a>步驟 6

虛擬網路建立後下, 一步是定義應用程式閘道器的前端 IP。 此時，選擇是公用或私用的 IP 位址的前端之間。 選擇取決於應用程式為網際網路具或內部只。 此案例假設使用的公用 IP 位址。 若要選擇 [私用的 IP 位址，可以按一下 [**私人**] 按鈕。 選擇 [自動指派的 IP 位址或您可以按一下 [**選擇特定私人的 IP 位址**核取方塊，以手動輸入。

### <a name="step-7"></a>步驟 7

按一下 [**選擇的公用 IP 位址**。 如果使用現有的公用 IP 位址此時選擇，您可以在這個案例中建立新的公用 IP 位址。 按一下 [**建立新**

![選擇 [公用 IP 位址刀][6]

### <a name="step-8"></a>步驟 8

接下來提供的公用 IP 位址的易記的名稱，然後按一下**[確定]**

![建立公用 IP 位址刀][7]

### <a name="step-9"></a>步驟 9

若要建立的應用程式閘道器時設定的最後一個設定，則接聽程式設定。  如果使用**http** ，則不會從左到設定，可以按一下**[確定]** 。 若要使用**https**進一步不需要設定。

若要使用**https**，是必要的憑證。 需要私密金鑰的憑證，因此需要提供.pfx 匯出的憑證和密碼。

### <a name="step-10"></a>步驟 10

按一下 [ **HTTPS**，按一下**上傳 PFX 憑證**文字方塊旁的**資料夾**圖示。
瀏覽至您的檔案系統.pfx 憑證檔案。 選取後，為憑證好記的名稱，然後輸入.pfx 檔密碼]。

完成後按一下**[確定**]，檢閱應用程式閘道器設定。

![在設定刀接聽組態] 區段][9]

### <a name="step-11"></a>步驟 11

檢閱摘要頁面，然後按一下**[確定**]。  現在是佇列應用程式閘道器，然後建立。

### <a name="step-12"></a>步驟 12

一旦建立應用程式閘道器，瀏覽至其在入口網站，以繼續應用程式閘道器設定。

![應用程式閘道器資源檢視][10]

這些步驟建立基本的應用程式的閘道器接聽、 後端資料庫、 後端 http 設定與規則的預設設定。 您可以修改以符合您的部署成功佈建後這些設定。

## <a name="next-steps"></a>後續步驟

這種情況下會建立預設應用程式的閘道器。 接下來的步驟是將集區成員新增、 修改設定，並調整中閘道器，才能正常運作的規則設定應用程式閘道器。

瞭解如何建立自訂的狀況檢查瀏覽[建立自訂的狀況檢查](application-gateway-create-probe-portal.md)

瞭解如何設定 SSL 卸載及瀏覽[設定 SSL 卸載](application-gateway-ssl-portal.md)採取成本 SSL 解密關閉您的網頁伺服器

瞭解如何保護您[Web 應用程式防火牆](application-gateway-webapplicationfirewall-overview.md)功能的應用程式的閘道器的應用程式。

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
