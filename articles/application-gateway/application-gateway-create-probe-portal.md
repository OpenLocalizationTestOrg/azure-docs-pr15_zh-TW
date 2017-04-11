<properties
   pageTitle="使用入口網站建立自訂的應用程式閘道檢查 |Microsoft Azure"
   description="瞭解如何使用入口網站應用程式的閘道器建立自訂的檢查"
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

# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>藉由使用入口網站應用程式的閘道器建立自訂的檢查

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-probe-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-probe-ps.md)
- [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>案例

在 [現有的應用程式閘道器中建立自訂的狀況檢查經歷下列案例。
此案例假設您已經有遵循的步驟來[建立應用程式閘道器](application-gateway-create-gateway-portal.md)。

## <a name="createprobe"></a>建立探查

透過入口網站執行的程序中設定探查。 第一個步驟是，建立探查下, 一步您加入探查應用程式閘道器的後端 http 設定。

### <a name="step-1"></a>步驟 1

瀏覽至 http://portal.azure.com，然後選取現有的應用程式閘道器。

![應用程式閘道器概觀][1]

### <a name="step-2"></a>步驟 2

按一下 [**探查**，然後按一下 [**新增**] 按鈕，將新的探查。

![新增探查刀填妥的資訊][2]

### <a name="step-3"></a>步驟 3

填寫 [探查所需的資訊，並完成時按一下**[確定]**。

- **名稱**-這是可以存取入口網站中探查好記的名稱。
- **Host （主機）** -這是用於探查主機名稱。 適用只有在多個網站上設定應用程式的閘道器，否則使用 '127.0.0.1'。 這是不同的 VM 主機名稱。
- **路徑**的自訂探查的完整 url 的其餘部分。 有效的路徑開頭 '/'
- **間隔時間 （秒）** -探查檢查狀況的執行頻率。 若要設定較不建議超過 30 秒。
- **逾時 （秒）** -探查等待逾時的時間量。 逾時間隔必須高 http 通話，可確保可用後端健康狀況] 頁面。
- **不佳的閥值**-失敗次數被視為不佳。 0 表示，如果狀況檢查失敗後端決定健康立即臨界值。

> [AZURE.IMPORTANT] 主機名稱不的伺服器名稱。 這是應用程式伺服器上執行的虛擬主機名稱。 探查會傳送至 http://(host name):(port from httpsetting)/urlPath

![這個設定的設定][3]

## <a name="add-probe-to-the-gateway"></a>新增閘道器探查

現在探查建立之後，就是將其新增至 [閘道器的時間。 這個設定應用程式閘道器的後端 http 設定。

### <a name="step-1"></a>步驟 1

按一下 [應用程式閘道器， **HTTP 設定**，然後按一下 [目前後端 http 設定] 視窗中的以顯示設定刀。

![https 設定] 視窗][4]

### <a name="step-2"></a>步驟 2

在**appGatewayBackEndHttp**設定刀中，按一下 [**使用自訂探查**，選擇 [[建立探查](#createprobe)] 區段中建立探查。
完成時，按一下**[確定]**並設定會套用。

![appgatewaybackend 設定刀][5]

預設探查檢查 web 應用程式的預設存取。 現在，建立自訂的檢查之後，應用程式閘道器會使用定義監控狀況的後端選取的自訂路徑。 根據所定義的準則，應用程式閘道器會檢查探查中指定的檔案。 如果 host （主機）︰ 連接埠通話 / 路徑不會傳回 Http 200 OK 狀態回應，伺服器超越旋轉] 之後達到不佳的臨界值。 探查會繼續不佳的執行個體，來判斷時健全一次。 後執行個體回健全 server 集區流量的開頭重新傳送至該，並執行個體探查持續使用者指定的間隔。


## <a name="next-steps"></a>後續步驟

若要瞭解如何設定 SSL 卸載 Azure 應用程式的閘道器請參閱[設定 SSL 卸載](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png