<properties
   pageTitle="使用入口網站建立的應用程式閘道路徑為基礎的規則 |Microsoft Azure"
   description="瞭解如何使用入口網站建立的應用程式閘道路徑為基礎的規則"
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

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>使用入口網站建立的應用程式閘道路徑為基礎的規則

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-url-route-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-url-route-arm-ps.md)

URL 路徑路由，可讓您建立根據 Http 要求的 URL 路徑路由之間的關聯。 它會檢查是否有傳送至 URL 清單，在 [應用程式閘道設定後端資料庫，並網路流量傳送至已定義的後端資料庫。 常用的 URL 為基礎的路由是負載平衡要求的不同的內容類型，以不同的後端伺服器集區。

URL 為基礎的路由至應用程式閘道介紹新規則類型。 應用程式閘道具有兩種規則類型︰ 基本和路徑為基礎的規則。 基本規則類型循環為提供服務後端資料庫，路徑為基礎的規則，除了循環通訊時，也會考慮要求 URL 路徑模式時選擇後端資料庫。

## <a name="scenario"></a>案例

建立路徑為基礎的規則中使用現有的應用程式閘道經歷下列案例。
此案例假設您已經有遵循的步驟來[建立應用程式閘道器](application-gateway-create-gateway-portal.md)。

![url 路徑][scenario]

## <a name="createrule"></a>建立路徑為基礎的規則

路徑為基礎的規則需要自己接聽程式時，建立規則，請務必確認之前必須使用可用接聽程式。

### <a name="step-1"></a>步驟 1

瀏覽至 http://portal.azure.com，然後選取現有的應用程式閘道器。 按一下 [**規則**]

![應用程式閘道器概觀][1]

### <a name="step-2"></a>步驟 2

按一下**路徑為基礎**的按鈕，以加入新路徑為基礎的規則。

### <a name="step-3"></a>步驟 3

**新增路徑為基礎的規則**刀有兩個部分。 第一個區段是您可在此定義接聽、 規則及預設路徑設定的名稱。 預設路徑設定是未落在路徑為基礎的自訂路徑的路線。 **新增路徑為基礎的規則**刀的第二部分是定義的路徑為基礎的規則本身的位置。

**基本設定**

- **名稱**-這是可以存取入口網站中的規則好記的名稱。
- **接聽**-這是用於規則的接聽程式。
- **預設後端資料庫**此設定會定義要用於預設規則後端的設定
- **預設 HTTP 設定**此設定會定義要用於預設規則的 HTTP 設定的設定。

**路徑為基礎的規則**

- **名稱**-這是好記的名稱，路徑為基礎的規則。
- **路徑**這項設定，定義規則時尋找轉接流量的路徑
- **後端資料庫**此設定會定義要用於規則後端的設定
- **HTTP 設定**此設定會定義要用於規則的 HTTP 設定的設定。

>[AZURE.IMPORTANT] 路徑︰ 以符合路徑圖樣的清單。 每個必須開始 / 和唯一 」\*」 允許在結尾。 /Xyz /xyz*或 /xyz/*是有效的範例。  

![新增路徑為基礎的規則刀填妥的資訊][2]

將路徑為基礎的規則新增至現有的應用程式閘道器是透過網站輕鬆程序。 建立路徑為基礎的規則之後, 才能輕鬆地新增其他規則進行編輯。 

![新增其他路徑為基礎的規則][3]

## <a name="next-steps"></a>後續步驟

若要瞭解如何設定 SSL 卸載 Azure 應用程式的閘道器請參閱[設定 SSL 卸載](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png