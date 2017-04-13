<properties 
   pageTitle="管理資源管理員] 中，使用 [預覽] 入口網站的 NSGs |Microsoft Azure"
   description="瞭解如何管理現存 NSGs 資源管理員] 中，使用 [預覽] 入口網站"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>管理 NSGs 使用 [預覽] 入口網站

> [AZURE.SELECTOR]
- [入口網站](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>擷取資訊

您可以檢視您現有的 NSGs、 擷取規則的現有的 NSG，及找出哪些資源 NSG 相關聯。

### <a name="view-existing-nsgs"></a>檢視現有的 NSGs
若要檢視所有現有 NSGs 訂閱，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**瀏覽 >** > **網路安全性群組**。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. 核取清單中**網路安全性群組**刀 NSGs。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

若要檢視的清單 NSGs**路由 NSG**的 [資源] 群組中，請遵循下列步驟。 

1. 按一下 [**資源群組 >** > **路由 NSG** > **...**。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. 在清單中的資源，尋找項目顯示 NSG] 圖示，**資源**刀以下所示。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>NSG 的所有規則都清單

若要檢視的名稱為**NSG FrontEnd**NSG 規則，請遵循下列步驟。 

1. 從**網路安全性群組**刀或**資源**劍如上所示，按一下 [ **NSG FrontEnd**]。
2. 在 [**設定**] 索引標籤中，按一下 [**輸入的安全性規則**]。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. **連入安全性規則**刀會顯示，如下所示。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. 在 [**設定**] 索引標籤中，按一下 [**輸出安全性規則**，請參閱輸出規則。

>[AZURE.NOTE] 若要檢視預設規則，請按一下 [顯示規則刀頂端的 [**預設規則**] 圖示。

### <a name="view-nsgs-associations"></a>檢視 NSGs 關聯

若要檢視的資源是**NSG FrontEnd** NSG 的關聯，請遵循下列步驟。

1. 從**網路安全性群組**刀或**資源**劍如上所示，按一下 [ **NSG FrontEnd**]。
2. 在 [**設定**] 索引標籤中，按一下 [檢視哪些子網路是 NSG 相關的**子網路**]。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. 在 [**設定**] 索引標籤中，按一下 [檢視 Nic 哪些 NSG 相關的**網路介面**。

## <a name="manage-rules"></a>管理規則

您可以新增規則到現有的 NSG、 編輯現有的規則，並移除規則。

### <a name="add-a-rule"></a>新增規則

若要新增允許**輸入**流量其**NSG FrontEnd** NSG 任何電腦從連接埠**443**的規則，請遵循下列步驟。

1. 從**網路安全性群組**刀或**資源**劍如上所示，按一下 [ **NSG FrontEnd**]。
2. 在 [**設定**] 索引標籤中，按一下 [**輸入的安全性規則**]。
3. 在**輸入安全性規則**刀，按一下 [**新增**]。 中**新增輸入的安全性規則**刀，填入的值，如下所示，然後再按一下**[確定]**。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. 幾秒鐘之後，請注意新規則中**輸入的安全性規則**刀。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>變更規則

若要變更**網際網路**只允許輸入的流量上方建立的規則，請遵循下列步驟。

1. 從**網路安全性群組**刀或**資源**劍如上所示，按一下 [ **NSG FrontEnd**]。
2. 在 [**設定**] 索引標籤中，按一下上方所建立的規則。
3. 在**允許 https**刀，如下所示，變更 [**來源**] 屬性，然後按一下 [**儲存**。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>刪除規則

若要刪除先前所建立的規則，請遵循下列步驟。

1. 從**網路安全性群組**刀或**資源**劍如上所示，按一下 [ **NSG FrontEnd**]。
2. 在 [**設定**] 索引標籤中，按一下上方所建立的規則。
3. 在 [**允許 https**刀中，按一下 [**刪除**]，然後按一下**[是]**。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>管理關聯

您可以將子網路 NSG 和 Nic 關聯。 您也可以中斷 NSG 從任何它與相關聯的資源。

### <a name="associate-an-nsg-to-a-nic"></a>建立以 NIC NSG 之間的關聯

建立關聯**NSG FrontEnd** NSG 至**TestNICWeb1** NIC，請遵循下列步驟。

1. 從**網路安全性群組**刀或**資源**劍如上所示，按一下 [ **NSG FrontEnd**]。
2. 在 [**設定**] 索引標籤上，按一下 [**網路介面** > **關聯** > **TestNICWeb1**。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>就從 NIC NSG

若要中斷**NSG FrontEnd** NSG 從**TestNICWeb1** NIC，請遵循下列步驟。

1. 從 Azure 入口網站中，按一下 [**資源群組 >** > **路由 NSG** > **...** >  **TestNICWeb1**。
2. 在 [ **TestNICWeb1**刀中，按一下 [**變更安全性...** > **None**.

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] 您也可以使用此刀關聯到任何現有的 NSG NIC。

### <a name="dissociate-an-nsg-from-a-subnet"></a>中斷子網路 NSG

若要中斷**NSG FrontEnd** NSG **FrontEnd**子網路，請遵循下列步驟。

1. 從 Azure 入口網站中，按一下 [**資源群組 >** > **路由 NSG** > **...** >  **TestVNet**。
2. 在 [**設定**刀中，按一下 [**子網路** > **FrontEnd** > **網路安全性群組** > **無**。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. 在**主選單**刀中，按一下 [**儲存**]。

![Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>建立子網路 NSG 之間的關聯

若要再次關聯**NSG FrontEnd** NSG **FronEnd**子網路，請遵循下列步驟。

1. 從 Azure 入口網站中，按一下 [**資源群組 >** > **路由 NSG** > **...** >  **TestVNet**。
2. 在 [**設定**刀中，按一下 [**子網路** > **FrontEnd** > **網路安全性群組** > **NSG FrontEnd**。
3. 在**主選單**刀中，按一下 [**儲存**]。

>[AZURE.NOTE] 您也可以從 thh NSG**設定**刀，子網路關聯 NSG。

## <a name="delete-an-nsg"></a>刪除 NSG

您只可以刪除任何資源不相關聯 NSG。 若要刪除 NSG，請遵循下列步驟。

1. 從 Azure 入口網站中，按一下 [**資源群組 >** > **路由 NSG** > **...** >  **NSG FrontEnd**。
2. 在**設定**刀中，按一下 [**網路介面**。
3. 如果有列出任何 Nic，請按一下 NIC，，然後遵循步驟 2 中[Dissociate 從 NIC NSG](#Dissociate-an-NSG-from-a-NIC)。
4. 針對每個 NIC 重複步驟 3
5. 在**設定**刀中，按一下 [**子網路**]。
6. 如果有列出任何子網路，請按一下 [子網路，請依照下列步驟 2 和 3 [Dissociate NSG 從子網路](#Dissociate-an-NSG-from-a-subnet)。
7. 捲動以**NSG FrontEnd**刀，然後按一下 [**刪除** > **[是]**。

[Azure 入口網站-NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>後續步驟

- [啟用記錄](virtual-network-nsg-manage-log.md)NSGs。
