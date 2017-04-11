<properties
   pageTitle="開始建立內部負載平衡器資源管理員] 中使用 [Azure 入口網站 |Microsoft Azure"
   description="瞭解如何建立內部負載平衡器資源管理員] 中使用 [Azure 入口網站"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Azure 入口網站中建立內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](load-balancer-get-started-ilb-classic-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>開始建立使用 Azure 入口網站內部負載平衡器

您可以使用下列步驟，從 Azure 入口網站中建立內部負載平衡器。

1. 開啟瀏覽器，瀏覽至[Azure 入口網站](http://portal.azure.com)，並登入您的 Azure 帳戶。
2. 在畫面左上方，按一下 [**新增** > **網路** > **負載平衡器**。
3. 在**建立負載平衡器**刀，輸入您的負載平衡器**名稱**。
4. 在**配置**] 下，按一下 [**內部**。
5. 按一下 [**虛擬網路**]，然後選取 [要建立負載平衡器虛擬網路。

    >[AZURE.NOTE] 如果看不到您想要使用的虛擬網路，請核取**位置**您正在使用的負載平衡器，並會相應地變更。

6. 按一下 [**子網路**，然後選取您要建立負載平衡器的子網路。
7. **IP 位址指派**中，按一下 [**動態**或**靜態**，根據您是否要或不修正 （靜態） 負載平衡器的 IP 位址。

    >[AZURE.NOTE] 如果您選取要使用的靜態 IP 位址，則必須提供的地址的負載平衡器。

8. **資源群組**] 底下指定負載平衡器，新的資源群組名稱或按一下**選取現有的**選項，然後選取 [現有的資源群組。
9. 按一下 [**建立**]。

## <a name="configure-load-balancing-rules"></a>設定負載平衡規則

負載平衡器建立之後, 瀏覽至要設定讓它的負載平衡器資源。
您需要先設定位址後端資料庫，並檢查設定負載平衡規則之前。

### <a name="step-1-configure-a-back-end-pool"></a>步驟 1︰ 設定後端資料庫

1. Azure 入口網站中，按一下 [**瀏覽** > **負載平衡器**]，然後按一下您先前所建立的負載平衡器。
2. 在**設定**刀中，按一下 [**後端資料庫**]。
3. 在**後端位址集區**刀中，按一下 [**新增**]。
4. **新增後端資料庫**刀中，為後端資料庫中，輸入**名稱**，然後按一下**[確定]**。

### <a name="step-2-configure-a-probe"></a>步驟 2︰ 設定檢查

1. Azure 入口網站中，按一下 [**瀏覽** > **負載平衡器**]，然後按一下您先前所建立的負載平衡器。
2. 在 [**設定**刀中，按一下 [**探查**]。
3. 在**探查**刀，按一下 [**新增**]。
4. 在**新增探查**刀中，輸入**名稱**的探查。
5. [**通訊協定**]、 選取**HTTP** （適用於網站） 或**TCP** （適用於其他 TCP 基礎應用程式中）。
6. 在**連接埠**，指定存取探查時要使用的連接埠。
7. 在**路徑**（僅限 HTTP 探查），指定為檢查所使用的路徑。
8. **底下**指定如何經常探查應用程式。
9. **不佳的閥值**，請在 [指定多少次後端虛擬機器標示為不佳之前應該會失敗。
10. 按一下**[確定**] 以建立探查。

### <a name="step-3-configure-load-balancing-rules"></a>步驟 3︰ 設定負載平衡規則

1. Azure 入口網站中，按一下 [**瀏覽** > **負載平衡器**]，然後按一下您先前所建立的負載平衡器。
2. 在 [**設定**刀中，按一下 [**負載平衡規則**]。
3. 在**負載平衡規則**刀中，按一下 [**新增**]。
4. 在**新增負載平衡規則**刀中，輸入**名稱**的規則。
5. [**通訊協定**]、 選取**HTTP** （適用於網站） 或**TCP** （適用於其他 TCP 基礎應用程式中）。
6. 在 [**連接埠**，指定連接埠用戶端連線至負載平衡器中。
7. 在**後端連接埠**，指定要使用的後端資料庫中的連接埠 （通常負載平衡器連接埠和後端連接埠是相同）。
8. 在**後端資料庫**，選取您先前所建立的後端資料庫。
9. 在**工作階段持續性**，選取您想要保存工作階段的方式。
10. 在**閒置逾時 （分鐘）**，指定閒置逾時。
11. **浮動 IP (傳回直接 server)**中，按一下 [**停用**或**啟用**。
12. 按一下**[確定]**。

## <a name="next-steps"></a>後續步驟

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)