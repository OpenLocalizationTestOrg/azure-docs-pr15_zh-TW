<properties 
   pageTitle="Azure 傳統入口網站中設定 VPN 閘道 |Microsoft Azure"
   description="本文會引導您完成設定您的虛擬網路 VPN 閘道和變更閘道器 VPN 路由類型。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>設定 VPN 閘道傳統部署模型


如果您想要建立安全跨內部部署與之間的連線 Azure 您內部部署的位置，必須要有 vpn 才能閘道器連線的設定。 在傳統的部署模型，閘道器可以是下列其中一種 VPN 路由︰ 靜態或動態。 您所選擇的類型，取決於您的網路設計的方案和您想要使用的內部部署 VPN 裝置。 

例如，某些連線選項，例如點為網站連線，需要動態路由閘道器。 如果您想要設定您的閘道器至支援點網站 (P2S) 連線和至網站 (S2S) 連線，您必須設定動態路由閘道，即使網站-可設定 [閘道器 VPN 路由類型。 

此外，必須確認您要使用的連線的裝置支援您想要建立 VPN 路由類型。 請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。


**瞭解這份文件** 

本文撰寫傳統部署模型使用[傳統的入口網站](https://manage.windowsazure.com)（不 Azure 入口網站）。 

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>設定概觀

下列步驟會引導您完成您要有 vpn 才能閘道器設定 Azure 傳統入口網站中。 這些步驟適用於建立使用傳統的部署模型的虛擬網路的閘道器。 目前，而非所有閘道器組態設定可在 Azure 入口網站。 如果是，我們會建立一組新套用至 Azure 入口網站的指示進行。


1. [建立您 VNet VPN 閘道器](#create-a-vpn-gateway)

1. [收集您 VPN 裝置設定的資訊](#gather-information-for-your-vpn-device-configuration)

1. [設定您要有 vpn 才能裝置](#configure-your-vpn-device)

1. [確認您的區域網路範圍和 VPN 閘道器 IP 位址](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>開始之前

設定您的閘道之前，您必須建立虛擬網路。 若要建立虛擬跨內部部署連線網路的步驟，請參閱[設定虛擬網路網站-VPN 連線](vpn-gateway-site-to-site-create.md)或[設定以點為網站 VPN 連線的虛擬網路](vpn-gateway-point-to-site-create.md)。 然後，使用下列步驟，設定 VPN 閘道和收集您需要設定 VPN 裝置的資訊。 

如果您已經有 VPN 閘道器，而且您想要變更 VPN 路由類型，請參閱[如何變更您的閘道器的 VPN 路由類型](#how-to-change-the-vpn-routing-type-for-your-gateway)。

## <a name="create-a-vpn-gateway"></a>建立 VPN 閘道器

1. [Azure 傳統入口網站](https://manage.windowsazure.com)，在 [**網路**] 頁面中，確認虛擬網路的 [狀態] 欄會**建立**。

1. 在 [**名稱**] 欄中，按一下虛擬網路的名稱。

1. 在**儀表板**頁面，請注意此 VNet 沒有尚未設定的閘道器。 當您進行設定您的閘道器的步驟，您會看到此狀態。

![無法建立閘道器](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


接下來，在頁面底部，按一下 [**建立閘道器**。 您可以選取 [*靜態路由*] 或 [*動態路由*。 選取的 VPN 路由類型而定幾個因素。 例如，VPN 裝置的支援，以及是否需要支援點-網站的連線。 請檢查[相關 VPN 裝置的虛擬網路連線](vpn-gateway-about-vpn-devices.md)以驗證您必須要有 vpn 才能路由類型。 一旦建立閘道器，您無法變更閘道器 VPN 路由類型不刪除並重新建立閘道器之間。 當系統提示您確認您要建立閘道器時，請按一下 [**是**]。

![閘道器 VPN 路由類型](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

在您的閘道器建立時，請注意在頁面上的閘道器圖形變成黃色顯示 [*建立閘道器*。 花費 45 分鐘，若要建立閘道器。 閘道器已完成，您可以使用其他設定的設定向前移動之前等待。

![建立閘道器](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

當閘道器變更*連線*時，您可以收集您需要 VPN 裝置的資訊。

![閘道器的連線](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>收集您 VPN 裝置設定的資訊

在建立閘道器之後，收集您 VPN 裝置設定的資訊。 虛擬網路的**儀表板**頁面上找到這項資訊︰

1. **閘道器的 IP 位址-**在**儀表板**頁面上找的 IP 位址。 您無法將閘道器建立完畢後，請參閱，直到。

1. **共用金鑰-**按一下畫面底部的**管理鍵**。 按一下以將其複製到剪貼簿，然後貼上並儲存索引鍵的索引鍵] 旁的圖示。 此按鈕只能有單一 S2S VPN 通道時。 如果您有多個 S2S VPN 通道，請使用*取得虛擬網路閘道器共用金鑰*API 或 PowerShell 指令程式。

![管理索引鍵](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>設定您要有 vpn 才能裝置

完成前一個步驟之後，您或您的網路系統管理員必須要有 vpn 才能裝置建立連線的設定。 VPN 和裝置有關的詳細資訊，請參閱[關於 VPN 裝置的虛擬網路連線](vpn-gateway-about-vpn-devices.md)。

VPN 裝置已設定之後，您可以在儀表板頁面上檢視您更新的連線資訊，您 VNet 的項目。

您也可以執行下列命令以測試您的連線︰

|                      | Cisco ASA             | Cisco ISR/ASR         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **檢查主模式 Sa**  | 顯示加密 isakmp 索 | 顯示加密 isakmp 索 | 取得 ike cookie  | 顯示安全性 ike 安全性關聯   |
| **檢查快速模式 Sa** | 顯示加密 ipsec 索  | 顯示加密 ipsec 索  | 取得索          | 顯示安全性 ipsec 安全性關聯 |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>確認您的區域網路範圍和 VPN 閘道器 IP 位址

### <a name="verify-your-vpn-gateway-ip-address"></a>請確認您要有 vpn 才能閘道器的 IP 位址

連線正確的閘道器，VPN 裝置的 IP 位址必須正確設定所指定您跨內部部署設定的區域網路。 一般而言，這被設定網站的設定程序時。 不過，如果您先前使用此區域網路使用不同的裝置，或 IP 位址已變更此區域網路，請編輯這些設定來指定正確的閘道器 IP 位址。

1. 驗證您的閘道器 IP 位址，在入口網站左窗格中按一下 [**網路**，然後選取 [**區域網路**] 頁面的頂端。 您會看到您所建立的每個區域網路 VPN 閘道器位址。 若要編輯的 IP 位址，請選取 VNet，然後按一下 [在頁面底部的 [**編輯**。

1. 在 [**指定的區域網路詳細資料**] 頁面上編輯的 IP 位址、，然後按一下頁面底部的下一步的箭號。

1. 在**指定的地址空間**頁面上，按一下右下儲存設定的核取記號。

### <a name="verify-the-address-ranges-for-your-local-networks"></a>驗證您的本機網路的位址範圍

為正確的流量透過閘道器至您內部部署的位置，您需要確認已指定每個 IP 位址範圍。 每個範圍必須列於 Azure**區域網路**設定。 根據您的內部部署位置的網路連線，這可能是較大的工作。 繫結的 IP 位址列的範圍內所包含的流量會透過虛擬網路 VPN 閘道傳送。 您的清單沒有為私人的範圍，但您會想要驗證您的內部部署設定可接收輸入的流量的範圍。

若要新增或編輯的區域網路的範圍，請使用下列步驟。

1. 若要編輯的區域網路的 IP 位址範圍，按一下**網路**上的入口網站的左窗格，然後選取頁面頂端的**區域網路**。 在入口網站中，若要檢視您所列出的範圍最簡單的方法是在 [**編輯**網頁。 若要查看您的範圍，請選取 VNet，然後按一下 [在頁面底部的 [**編輯**。

1. 在**指定的區域網路詳細資料**頁面上，不進行任何變更。 按一下頁面底部的下一步箭號。

1. 在**指定的地址空間**頁面上，變更您的網路地址空間。 然後按一下 [核取記號以儲存您的設定]。

## <a name="how-to-view-gateway-traffic"></a>如何檢視閘道器流量

您可以檢視您的閘道器和閘道器流量虛擬網路**儀表板**頁面。

您可以在**儀表板**頁面上檢視下列項目︰

- 在傳送到您的閘道] 中的資料與資料的資料量。

- DNS 伺服器所指定的虛擬網路的名稱。

- 閘道和 VPN 裝置之間的連線。

- 用來設定您的閘道器連線到您要有 vpn 才能裝置共用索引鍵。


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>如何變更您的閘道器的 VPN 路由類型

某些連線設定只適用於特定的閘道器路由類型，因為您可能會發現您需要變更閘道器的現有的 VPN 閘道器的 VPN 路由類型。 例如，您可能要新增點為網站連線至現有網站-連線含有靜態閘道器。 點-網站連線要求動態閘道器。 這表示 P2S 連線的設定，您必須將閘道器 VPN 路由類型從變更靜態動態。

如果您需要變更閘道器 VPN 路由類型，您會刪除現有的閘道器，，，然後建立新的路由類型的 [新增閘道器。 您不需要刪除整個虛擬網路變更閘道器路由類型。

變更之前閘道 VPN 路由類型，請務必確認您要有 vpn 才能裝置支援您想要使用的路由類型。 若要下載新路由設定範例，並檢查 VPN 裝置需求，請參閱[關於 VPN 裝置的虛擬網路連線](vpn-gateway-about-vpn-devices.md)。

>[AZURE.IMPORTANT] 當您刪除虛擬網路 VPN 閘道器時，指派給閘道 VIP 放開。 當您重新建立閘道器時，會指派新的 VIP。

1. **刪除現有的 VPN 閘道器。**

    在**儀表板**頁面虛擬網路上，瀏覽至頁面底部，然後按一下 [**刪除閘道器**。 閘道器已刪除的通知，請等候。 當您收到通知，在螢幕上的將閘道器已被刪除時，您可以建立新的閘道器。

1. **建立新的 VPN 閘道器。**

    使用頁面頂端的程序建立新的閘道器︰[建立 VPN 閘道器](#create-a-vpn-gateway)。


## <a name="next-steps"></a>後續步驟

您可以新增虛擬機器至虛擬網路。 瞭解[如何建立自訂的虛擬機器](../virtual-machines/virtual-machines-windows-classic-createportal.md)。

如果您想要設定點為網站 VPN 連線時，請參閱[設定點為網站 VPN 連線](vpn-gateway-point-to-site-create.md)。

 
