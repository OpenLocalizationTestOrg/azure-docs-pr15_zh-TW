<properties
   pageTitle="使用入口網站中設定 SSL 卸載應用程式閘道 |Microsoft Azure"
   description="本頁面提供使用 SSL 建立應用程式閘道指示卸載使用入口網站"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>使用入口網站中設定 SSL 卸載應用程式閘道

> [AZURE.SELECTOR]
-[Azure 入口網站](application-gateway-ssl-portal.md)
-[資源管理員 PowerShell 的 Azure](application-gateway-ssl-arm.md)
-[傳統 PowerShell 的 Azure](application-gateway-ssl.md)

Azure 應用程式閘道器可以終止安全通訊端層 (SSL) 工作階段，若要避免在網頁伺服器陣列的成本 SSL 解密工作的閘道器設定。 SSL 卸載也可簡化的前端伺服器設定及管理 web 應用程式。

## <a name="scenario"></a>案例

下列情況經歷設定 SSL 卸載上現有的應用程式閘道器。 此案例假設您已經有遵循的步驟來[建立應用程式閘道器](application-gateway-create-gateway-portal.md)。

## <a name="before-you-begin"></a>開始之前

若要設定 SSL 卸載應用程式閘道器，是必要的憑證。 這個憑證會載入的應用程式閘道器，並用於加密並解密透過 SSL 傳送的流量。 憑證必須位於 [個人資訊交換 (pfx) 格式。 這種檔案格式可以匯出私密金鑰所需執行的加密和解密流量的應用程式閘道器。

## <a name="add-an-https-listener"></a>新增 HTTPS 接聽

HTTPS 接聽流量根據其設定，並有助於將流量路由傳送至後端資料庫。

### <a name="step-1"></a>步驟 1

瀏覽至 Azure 入口網站，然後選取 [使用現有的應用程式閘道

### <a name="step-2"></a>步驟 2

按一下接聽程式，然後按一下 [新增] 按鈕新增接聽程式。

![應用程式閘道器概觀刀][1]

### <a name="step-3"></a>步驟 3

填寫所需的資訊接聽和上傳.pfx 憑證，完成時按一下 [確定]。

**名稱**此值是接聽項記的名稱。

**Frontend IP 設定**此值是用於接聽 frontend IP 設定。

**Frontend 連接埠 （名稱/連接埠）** -在應用程式閘道和實際使用的連接埠的前端上使用的連接埠易記的名稱。

**通訊協定**-若要判斷 https 或 http 對前端使用切換參數。

使用**憑證 （名稱/密碼）**如果 SSL 卸載、.pfx 憑證需要此設定，以好記的名稱和密碼所需。

![新增接聽刀][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>建立規則，並將其關聯到接聽程式

現在已建立接聽程式。 就可以建立規則以處理接聽程式流量。

### <a name="step-1"></a>步驟 1

按一下 [應用程式閘道器的**規則**，然後按一下 [新增]。

![應用程式閘道器規則刀][3]

### <a name="step-2"></a>步驟 2

在**新增基本規則**刀中，輸入規則的易記的名稱，然後選擇 [上一個步驟中建立接聽]。 選擇適當的後端資料庫及 http 設定，然後按一下**[確定]**

![https 設定] 視窗][4]

設定會立即儲存應用程式閘道器。 如需這些設定可能需要一些時間才能檢視透過入口網站，或透過 PowerShell 之前的 [處理程序儲存。 一旦儲存應用程式閘道器處理加密及解密的流量。 透過 http 處理應用程式閘道器與後端網頁伺服器之間的所有流量。 如果透過 https 發起的租用戶用戶端的任何通訊會回到加密的用戶端。

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 Azure 應用程式的閘道器設定自訂的狀況檢查，請參閱[建立自訂的狀況檢查](application-gateway-create-gateway-portal.md)。

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png