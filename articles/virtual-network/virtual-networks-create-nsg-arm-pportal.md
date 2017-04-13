<properties 
   pageTitle="如何在 ARM 模式下使用 Azure 入口網站中建立 NSGs |Microsoft Azure"
   description="瞭解如何建立並部署 NSGs ARM 使用 Azure 入口網站中"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>如何管理 NSGs 使用 Azure 入口網站

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[建立 NSGs 傳統部署模型中](virtual-networks-create-nsg-classic-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

範例 PowerShell 命令下方預期已經建立一個簡單環境根據上述情況。 如果您想要執行這份文件中所顯示的命令，第一次部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)建立測試環境**部署至 Azure**、 取代預設參數值，如有必要，，然後按一下依照入口網站中的指示進行。 下列步驟使用**路由 NSG**作為範本已部署至資源群組的名稱。

## <a name="create-the-nsg-frontend-nsg"></a>建立 NSG FrontEnd NSG

若要建立**NSG FrontEnd** NSG，如下圖所示，在上述情況，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**瀏覽 >** > **網路安全性群組**。

    ![Azure 入口網站-NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. 在**網路安全性群組**刀中，按一下 [**新增**]。
  
    ![Azure 入口網站-NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. 在**建立網路安全性群組**刀中，建立名為 [*路由 NSG*資源] 群組中的*NSG FrontEnd* NSG，然後按一下 [**建立**。

    ![Azure 入口網站-NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>在 [現有的 NSG 中建立規則

若要在現有 NSG 從 Azure 入口網站中建立規則，請遵循下列步驟。

2. 按一下 [**瀏覽 >** > **網路安全性群組**。

3. 在 [NSGs 清單中，按一下 [ **NSG FrontEnd** > **輸入的安全性規則**

    ![Azure 入口網站-NSG FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. 在**輸入的安全性規則**清單中，按一下 [**新增**]。

    ![Azure 入口網站-新增規則](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. 在**新增輸入的安全性規則**刀中，建立規則，以允許存取透過*TCP*連接埠*80*任何 vm 任何來源*200*的優先順序命名*網頁規則*，然後按一下**[確定]**。 請注意，大部分的這些設定已預設值。

    ![Azure 入口網站-規則設定](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. 在數秒後，您會看到 NSG 新規則。

    ![Azure 入口網站-新增規則](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. 重複步驟 6，建立名為*rdp 規則*的優先順序，為*250*允許其存取透過*TCP*連接埠*3389*任何 vm 任何來源輸入的規則。

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>建立主選單子網路 NSG 之間的關聯

1. 按一下 [**瀏覽 >** > **資源群組** > **路由 NSG**。
2. 在 [**路由 NSG**刀中，按一下 [ **...** >  **TestVNet**。

    ![Azure 入口網站-TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. 在 [**設定**刀中，按一下 [**子網路** > **FrontEnd** > **網路安全性群組** > **NSG FrontEnd**。

    ![Azure 入口網站的子網路設定](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. 在**主選單**刀中，按一下 [**儲存**]。

    ![Azure 入口網站的子網路設定](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>建立 NSG 後端 NSG

若要建立**NSG 後端**NSG 與**後端**子網路建立關聯，請遵循下列步驟。

1. 重複[建立 NSG FrontEnd NSG](#Create-the-NSG-FrontEnd-NSG) ]，建立名為*NSG 後端*NSG 中的步驟
2. 重複中[現有的 NSG 中建立規則](#Create-rules-in-an-existing-NSG)以建立下表中的 [**輸入**規則的步驟。

  	|輸入的規則|輸出規則|
  	|---|---|
  	|![Azure 入口網站-輸入規則](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Azure 入口網站-輸出規則](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. 重複步驟中[建立關聯到 FrontEnd 子網路 NSG](#Associate-the-NSG-to-the-FrontEnd-subnet)建立**NSG 後端**NSG 到**後端**的子網路之間的關聯。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[管理現有的 NSGs](virtual-network-manage-nsg-arm-portal.md)
- [啟用記錄](virtual-network-nsg-manage-log.md)NSGs。