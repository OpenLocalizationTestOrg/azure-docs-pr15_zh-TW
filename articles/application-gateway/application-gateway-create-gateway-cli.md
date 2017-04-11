<properties
   pageTitle="建立使用 Azure CLI 資源管理員] 中的應用程式閘道 |Microsoft Azure"
   description="瞭解如何使用 Azure CLI 資源管理員] 中建立的應用程式閘道器"
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

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>使用 Azure CLI 建立應用程式閘道器

> [AZURE.SELECTOR]
- [Azure 入口網站](application-gateway-create-gateway-portal.md)
- [Azure 資源管理員 PowerShell](application-gateway-create-gateway-arm.md)
- [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
- [Azure 資源管理員範本](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是有 7 層負載平衡器。 是否位於雲端或內部部署，它會提供容錯移轉時，效能路由 HTTP 要求之間不同的伺服器。 應用程式閘道具有下列的應用程式傳送功能︰ HTTP 負載平衡、 cookie 為基礎的工作階段相關性和安全通訊端層 (SSL) 卸載自訂健康探查及支援多網站。

## <a name="prerequisite-install-the-azure-cli"></a>必要︰ 安裝 Azure CLI

若要執行本文中的步驟，您必須[安裝 Azure 命令列介面 for Mac、 Linux 和 Windows (Azure CLI)](../xplat-cli-install.md) ，您需要[登入 Azure](../xplat-cli-connect.md)。 

> [AZURE.NOTE] 如果您沒有安裝 Azure 帳戶，您需要一個。 移登，[這裡免費試用](../active-directory/sign-up-organization.md)版。

## <a name="scenario"></a>案例

在此案例中，您可以瞭解如何建立使用 Azure 入口網站應用程式閘道器。

將這種情況︰

- 建立兩個執行個體中的應用程式的閘道器。
- 建立名為 AdatumAppGatewayVNET 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
- 建立名為其 CIDR 區塊使用 10.0.0.0/28 的 Appgatewaysubnet 子網路。
- 設定 SSL 卸載的憑證。

![案例範例][scenario]

>[AZURE.NOTE] 其他設定的應用程式的閘道器，包括自訂健康探查、 應用程式閘道器設定之後，而不是在初次部署設定後端資料庫地址及其他規則。

## <a name="before-you-begin"></a>開始之前

Azure 應用程式閘道需要它自己的子網路。 在建立虛擬網路時，請確定您離開地址空間不足，無法有多個子網路。 一旦您部署子網路應用程式閘道器時，只其他應用程式的閘道器都能新增到子網路。

## <a name="log-in-to-azure"></a>登入 Azure

開啟**Microsoft Azure 命令提示字元**中，並登入。 

    azure login

一旦您輸入上述範例中，提供程式碼。 瀏覽至要繼續登入程序在瀏覽器中 https://aka.ms/devicelogin。

![cmd 顯示裝置登入][1]

在瀏覽器中，輸入您所收到的驗證的碼。 您會重新導向至登入頁面。

![瀏覽器] 以輸入代碼][2]

您輸入的程式碼後登入，請關閉瀏覽器] 以繼續在此案例。

![順利登入][3]

## <a name="switch-to-resource-manager-mode"></a>切換至 [資源管理員模式

    azure config mode arm

## <a name="create-the-resource-group"></a>建立資源群組

建立應用程式閘道器之前, 資源群組會建立包含應用程式閘道器。 下列顯示的命令。

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>建立虛擬網路

資源群組建立之後，應用程式閘道器會建立虛擬網路。  在下列範例中，地址的間距是 10.0.0.0/16 為上述情況備忘稿中所定義。

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>建立子網路

建立虛擬網路之後，應用程式閘道器被新增子網路。  如果您打算使用裝載於相同的虛擬網路應用程式閘道器的 web 應用程式的應用程式的閘道器，請務必有足夠的空間的另一個子網路。

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>建立應用程式閘道器

虛擬網路和子網路建立之後，應用程式閘道器的必要條件都已完成。 此外先前匯出的.pfx 檔憑證和憑證的密碼所需的下列步驟︰ 使用的後端的 IP 位址是為您的後端伺服器 IP 位址。 虛擬網路中的私人 IPs、 公用 ips 或您的後端伺服器的完整的網域名稱，可以是下列的值。

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] 可以執行下列命令建立期間提供的參數的清單︰ **azure 的網路應用程式-閘道器建立-協助**。

此範例會建立基本的應用程式的閘道器接聽、 後端資料庫、 後端 http 設定與規則的預設設定。 也會設定 SSL 卸載。 您可以修改以符合您的部署成功佈建後這些設定。
如果您已使用定義的 web 應用程式在上述步驟後端資料庫，建立後，負載平衡開始。

## <a name="next-steps"></a>後續步驟

瞭解如何建立自訂的狀況檢查瀏覽[建立自訂的狀況檢查](application-gateway-create-probe-portal.md)

瞭解如何設定 SSL 卸載及瀏覽[設定 SSL 卸載](application-gateway-ssl-arm.md)採取成本 SSL 解密關閉您的網頁伺服器

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png