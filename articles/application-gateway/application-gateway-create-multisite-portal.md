<properties
   pageTitle="設定現有的應用程式閘道裝載 Azure 入口網站中的多個網站 |Microsoft Azure"
   description="本頁面提供設定現有的 Azure 應用程式閘道裝載於相同閘道 Azure 入口網站的多個 web 應用程式的指示進行。"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>設定現有的應用程式閘道裝載多個 web 應用程式

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-multisite-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

多個網站主機服務可讓您部署上相同的應用程式閘道器的多個 web 應用程式。 必須在傳入 HTTP 邀請中，以決定哪些接聽會收到流量主機標題的目前狀態。 接聽然後引導至適當的後端資料庫的資料傳輸規則定義中的閘道器設定。 啟用 SSL web 應用程式，在應用程式閘道依賴選擇正確的接聽 web 流量的伺服器名稱的指示 (SNI) 副檔名。 常用的多個網站主機服務是負載平衡要求以不同的後端伺服器集區的其他網站網域。 同樣的相同的根網域的多個子網域可能也會裝載於相同的應用程式閘道器。

## <a name="scenario"></a>案例

在下列範例中，應用程式的閘道器服務 contoso.com 和 fabrikam.com 的流量與兩個後端伺服器集區︰ contoso server 集區和 fabrikam server 集區。 類似的安裝程式無法用於主機子網域，例如 app.contoso.com 與 blog.contoso.com。

![多站台的案例][multisite]

## <a name="before-you-begin"></a>開始之前

這種情況下會新增至現有的應用程式閘道器的多網站支援。 才能完成這種情況下使用現有的應用程式閘道的案例必須可供設定。 請造訪[建立使用入口網站應用程式閘道器](./application-gateway-create-gateway-portal.md)以瞭解如何建立基本的應用程式的閘道器在入口網站。

## <a name="requirements"></a>需求

- **後端伺服器集區︰**後端伺服器 IP 位址清單。 列出的 IP 位址應該是屬於虛擬網路的子網路或應該公用 IP/VIP。 也可以用 FQDN。
- **後端伺服器資料庫設定︰**每個資料庫有設定，例如連接埠與通訊協定，cookie 為基礎的相關性。 這些設定會連結到資料庫]，就會套用到所有資料庫中的伺服器。
- **前端連接埠︰**此連接埠是開啟應用程式閘道器公用連接埠。 流量碰到此連接埠]，然後會被重新導向至其中一個後端伺服器。
- **接聽︰**接聽具有前端連接埠通訊協定 （Http 或 Https，這些值會區分大小寫），以及的 SSL 憑證名稱 （如果設定 SSL 卸載）。 多網站啟用應用程式閘道主機名稱和 SNI 指標也會加入。
- **規則︰**此規則繫結後端伺服器集區接聽程式，並定義流量在時叫接聽會導向至的後端伺服器集區。
- **憑證︰**每個接聽程式需要唯一的憑證時，會在此範例中的多網站建立 2 接聽程式。 必須建立兩個.pfx 憑證和他們的密碼。

## <a name="create-an-application-gateway"></a>建立應用程式閘道器

更新閘道器應用程式所需的步驟如下︰

1. 建立要用於每個網站的後端資料庫。
2. 建立新的接聽，每個網站應用程式閘道器會支援。
3. 建立規則以對應適當的後端每個接聽程式。

## <a name="create-back-end-pools-for-each-site"></a>建立每個網站的後端的資料庫

每個網站的後端資料庫的應用程式閘道器會在需要支援，請在此情況下 2 將一個用於 contoso11.com，一個用於 fabrikam11.com。

### <a name="step-1"></a>步驟 1

瀏覽至現有的應用程式閘道在 Azure 入口網站 (https://portal.azure.com)。 選取**後端資料庫**，然後按一下 [**新增**]

![新增後端資料庫][7]

### <a name="step-2"></a>步驟 2

填入後端資料庫**pool1**，新增的 ip 位址或 Fqdn 的後端伺服器的資訊，然後按一下**[確定]**

![後端資料庫 pool1 設定][8]

### <a name="step-3"></a>步驟 3

在後端資料庫刀上按一下 [**新增**]，以新增額外的後端資料庫**pool2**，新增的 ip 位址或 FQDN 的後端伺服器，然後按一下**[確定]**

![後端資料庫 pool2 設定][9]

### <a name="create-listeners-for-each-back-end"></a>建立每個後端的接聽

應用程式閘道依賴 HTTP 1.1 主機標頭裝載相同的公用 IP 位址和連接埠上的多個網站。 在入口網站中建立基本接聽不包含此屬性。

### <a name="step-1"></a>步驟 1

按一下現有的應用程式閘道**接聽程式**，然後按一下 [新增第一個接聽的**多網站**。

![接聽概觀刀][1]

### <a name="step-2"></a>步驟 2

設定填寫接聽程式，在此範例中 SSL 終止的資訊，請建立新的主選單連接埠。 上傳用於 SSL 終止.pfx 憑證。 只在標準的基本接聽刀比較此刀差別主機名稱。

![接聽屬性刀][2]

### <a name="step-3"></a>步驟 3

按一下 [**多個網站**並建立另一個接聽項，第二個網站上一個步驟所述。 請確認第二個接聽程式使用不同的憑證。 只在標準的基本接聽刀比較此刀差別主機名稱。 填寫 [接聽程式的資訊，然後按一下**[確定**]。

![接聽屬性刀][3]

> [AZURE.NOTE] 建立在 Azure 入口網站應用程式的閘道器接聽長時間執行工作時，可能需要一些時間以在這個案例中建立的兩個接聽。 完成時接聽程式顯示在入口網站下圖所示。

![接聽概觀][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>建立規則，將接聽對應到後端資料庫

### <a name="step-1"></a>步驟 1

瀏覽至現有的應用程式閘道在 Azure 入口網站 (https://portal.azure.com)。 選取**規則**，選擇 [現有的預設規則**rule1**並按一下 [**編輯**]。

### <a name="step-2"></a>步驟 2

填寫規則刀，如下圖所示。 選擇第一個接聽和第一個資料庫，然後按一下完成時，[**儲存**]。

![編輯現有的規則][6]

### <a name="step-3"></a>步驟 3

按一下 [若要建立第 2 規則的**基本規則**]。 填寫表單的第二個接聽和第二個後端資料庫，然後按一下**[確定**] 儲存]。

![新增基本規則刀][10]

已完成設定現有的應用程式閘道器的多網站支援透過 Azure 入口網站。

## <a name="next-steps"></a>後續步驟

瞭解如何保護您的網站與[應用程式閘道-Web 應用程式防火牆](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png