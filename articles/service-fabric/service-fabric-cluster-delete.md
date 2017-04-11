<properties
   pageTitle="刪除 Azure 叢集和其資源 |Microsoft Azure"
   description="瞭解如何完全刪除服務布料的轉印圖樣叢集刪除包含叢集的資源群組或選擇性地刪除資源。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>刪除服務布料的轉印圖樣叢集 Azure 及它所使用的資源

服務布料的轉印圖樣叢集組成的許多其他 Azure 資源除了叢集資源本身。 因此，若要完全刪除服務布料的轉印圖樣叢集，您也需要刪除它由的所有資源。
有兩個選項: [刪除的叢集資源群組 （其中刪除叢集資源與 [資源] 群組中的任何其他資源） 或特別刪除 [叢集資源，其相關聯的資源 （但不是在 [資源] 群組中的其他資源）。

>[AZURE.NOTE] 刪除叢集資源，**並不會**刪除所有其他的資源所組成的服務布料的轉印圖樣叢集。

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>刪除整個資源群組 （路由） 中的服務布料的轉印圖樣叢集

這是最簡單的方法，以確保您刪除叢集，包括資源群組相關聯的所有資源。 您可以刪除資源群組使用 PowerShell 或透過 Azure 入口網站。 如果您的資源群組與服務布料的轉印圖樣叢集不相關的資源，您可以刪除特定的資源。

### <a name="delete-the-resource-group-using-azure-powershell"></a>刪除使用 PowerShell 的 Azure 資源群組

您也可以藉由執行下列 cmdlet，PowerShell 的 Azure 刪除資源群組。 請確定 Azure PowerShell 1.0 或更大已安裝在您的電腦上。 如果您不這麼做之前，請依照下列步驟所述[如何安裝和設定 Azure PowerShell。](../powershell-install-configure.md)

開啟 PowerShell 視窗，然後執行下列 PS cmdlet:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

會提示您確認刪除時，如果您沒有使用*-強制*選項。 在 [確認路由和其包含的所有資源會刪除。

### <a name="delete-a-resource-group-in-the-azure-portal"></a>刪除 Azure 入口網站中的 [資源] 群組  

1. 登入[Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至您想要刪除的服務布料的轉印圖樣叢集。
3. 按一下在 [叢集基本資訊] 頁面上的資源群組]。
4. 如此會開啟 [**資源群組基本資訊**] 頁面。
5. 按一下 [**刪除**]。
6. 依照指示完成的資源群組刪除該頁面上。

![資源群組刪除][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>刪除叢集資源與資源使用，但不是在 [資源] 群組中的其他資源

如果您的資源群組有您想要刪除的服務布料的轉印圖樣叢集相關的資源，則若要刪除整個資源群組更容易。 如果您想要選擇性地刪除資源群組中的資源一個接一個，然後遵循下列步驟。

如果您部署使用入口網站，或使用服務布料的轉印圖樣資源管理員範本的範本藝廊叢集，叢集使用的所有資源的都標記以下列兩個標籤。 您可以使用這些決定您想要刪除的資源。

***標記 #1:***索引鍵 = clusterName，值 = '叢集的名稱 」

***標記 #2:***索引鍵 = resourceName，值 = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>刪除特定 Azure 入口網站中的資源

1. 登入[Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至您想要刪除的服務布料的轉印圖樣叢集。
3. 移至**所有設定**基礎刀上。
4. **資源管理**] 下的**標籤**上按一下 [設定刀中。
5. 按一下其中一個**標籤**中標記刀以取得該標記的所有資源的清單。

    ![資源標籤][ResourceTags]

6. 一旦您已標記的資源的清單，按一下每個資源，並將它們刪除。

    ![標記的資源][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>刪除使用 PowerShell 的 Azure 的資源

您可以藉由執行下列 cmdlet，PowerShell 的 Azure 刪除一個接一個的資源。 請確定 Azure PowerShell 1.0 或更大已安裝在您的電腦上。 如果您不這麼做之前，請依照下列步驟所述[如何安裝和設定 Azure PowerShell。](../powershell-install-configure.md)

開啟 PowerShell 視窗，然後執行下列 PS cmdlet:

```powershell
Login-AzureRmAccount
```
資源的每個要刪除，請執行下列動作︰

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

若要刪除叢集資源，請執行下列動作︰

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>後續步驟
閱讀下列也進一步瞭解升級叢集和分割服務︰

- [深入了解叢集升級](service-fabric-cluster-upgrade.md)
- [深入了解分割的最大比例狀態服務](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
