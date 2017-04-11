
<properties
   pageTitle="開始建立對使用 Azure 傳統入口網站的標準部署模型中的負載平衡器網際網路 |Microsoft Azure"
   description="瞭解如何建立網際網路圖示的負載平衡器在傳統的部署模型中使用 [Azure 傳統入口網站"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>開始建立網際網路圖示 Azure 傳統入口網站中的負載平衡器 （傳統）

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[瞭解如何建立網際網路圖示的負載平衡器使用 Azure 資源管理員](load-balancer-get-started-internet-arm-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>設定虛擬機器具網際網路負載平衡器

若要在雲端服務的虛擬機器中載入從網際網路平衡網路流量，您必須建立負載平衡的設定。 此程序假設您已經建立虛擬機器，而且他們是全都在相同的雲端服務。

**若要設定的虛擬機器負載平衡設定**

1. 在 Azure 傳統的入口網站中，按一下**虛擬機器**，，然後按一下虛擬機器中的負載平衡設定的名稱。

2. 按一下 [**結束點**，然後按一下 [**新增]**。

3. 在 [**新增到虛擬機器結束點**] 頁面上按一下右箭號。

4. 在 [**指定端點的詳細資料**] 頁面上︰

    * 在 [**名稱**] 輸入端點的名稱，或從預先定義的端點的常見的通訊協定的清單選取名稱。
    * 在 [**通訊協定**，請視需要選取所需的端點，請依照，類型的通訊協定。
    * 在**公用連接埠與私人連接埠**中，輸入您想要虛擬機器使用，請視需要連接埠號碼。 您可以使用 [虛擬機器上的私人的連接埠和防火牆規則，以符合您的應用程式的方式流量重新導向。 私人的連接埠可公用連接埠相同。 例如，網頁 (HTTP) 的流量的端點，您可以將連接埠 80 指派公開及私密金鑰連接埠。

5. 選取 [**建立負載平衡的設定**]，然後按一下 [向右箭號。

6. 在**設定的負載平衡設定**] 頁面中，輸入負載平衡集名稱，然後指派 Azure 負載平衡器探查行為的值，然後。 負載平衡器會使用探查，來判斷虛擬機器中的負載平衡設定是否可接收內送的流量。

7. 按一下 [核取記號，以建立負載平衡結束點]。 您會**是**看到的虛擬機器的**結束點**] 頁面的 [**負載平衡集名稱**] 欄中。

8. 在入口網站，按一下**虛擬機器**額外的虛擬機器中的負載平衡設定的名稱、 按一下 [**結束點**，然後按一下然後按一下 [**新增**。

9. 在 [**新增到虛擬機器結束點**] 頁面上按一下 [**新增至現有的負載平衡設定的結束點**選取負載平衡集的名稱，再按一下右箭號。

10. 在 [**指定端點的詳細資料**] 頁面中，輸入端點的名稱，然後按一下核取記號。

在負載平衡設定其他的虛擬機器，重複步驟 8 10。



## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

