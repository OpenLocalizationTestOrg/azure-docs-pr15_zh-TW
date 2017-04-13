<properties 
   pageTitle="部署固定的公用 ip 使用 Azure 入口網站中資源管理員 VM |Microsoft Azure"
   description="瞭解如何部署 Vm 固定的公用 ip 使用 zure 入口網站中資源管理員"
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
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>部署 VM 固定的公用 ip 使用 Azure 入口網站

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>使用靜態的公用 IP 建立 VM 

若要建立 VM 靜態的公用 IP 位址 Azure 入口網站中，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至[Azure 入口網站](https://portal.azure.com)，如果有需要，請登入您的 Azure 帳戶。
2. 在左邊的角落的入口網站，按一下 [**新增**>>**計算**>**Windows Server 2012 R2 資料中心**。
3. 在**選取的部署模型**] 清單中，選取**資源管理員**，然後按一下 [**建立**]。
4. **基本概念**刀，如下所示，輸入 VM 資訊，然後按一下**[確定]**。

    ![Azure 入口網站-基本概念](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. 在 [**選擇大小**刀，如下所示，按一下 [**標準] A1** ，然後按一下**選取**。

    ![Azure 入口網站-選擇的大小](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. 在**設定**刀中，按一下 [**公用 IP 位址**，然後中**建立的公用 IP 位址**刀中，在 [**工作分派**] 底下，按一下 [**靜態**如下所示。 然後按一下**[確定]**。

    ![Azure 入口網站-建立的公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. 在**設定**刀中，按一下**[確定]**。
8. 檢閱**摘要**刀，如下所示，然後再按一下**[確定]**。

    ![Azure 入口網站-建立的公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. 請注意您的儀表板中新的方塊。

    ![Azure 入口網站-建立的公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. 如下所示，VM 建立後，會顯示**設定**刀

    ![Azure 入口網站-建立的公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)