<properties 
    pageTitle="將資源移到新的資源群組 |Microsoft Azure" 
    description="使用 Azure 資源管理員資源移至新的資源群組或訂閱。" 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>將資源移到新的資源群組或訂閱

本主題說明如何將資源移至新的訂閱或同一份訂閱中的新 [資源] 群組。 您可以使用入口網站、 PowerShell、 Azure CLI 或 REST API 移動資源。 本主題中的移動作業可提供給您不需要任何協助從 Azure 支援。

一般而言，當您決定的移動資源︰

- 帳單寄送的目的，資源需要在不同的訂閱。
- 資源不再共用相同的生命週期為與先前群組的資源。 您想要將其移至新的資源群組，因此您可以從其他的資源，分別管理資源。

移動時的資源，來源群組和目標群組會遭鎖定作業期間。 撰寫及刪除作業直到完成移動封鎖在群組]。

您無法變更資源的位置。 移動資源只將其移到新的資源群組。 新的資源群組可能會有不同的位置，但不會變更資源的位置。

> [AZURE.NOTE] 本文將說明如何將資源在現有的 Azure 帳戶 offering。 如果您確實想要變更您繼續使用您現有的資源時 （例如，從預先支付 pay-as-you-go 升級） 提供的 Azure 帳戶，請參閱[切換至另一個優惠 Azure 訂閱](billing-how-to-switch-azure-offer.md)。 

## <a name="checklist-before-moving-resources"></a>移動資源之前的檢查清單

有日前資源執行的一些重要步驟。 確認下列條件，避免發生錯誤。

1. 服務必須啟用移動資源的能力。 請參閱下方的清單，如需哪些[服務可讓移動資源](#services-that-enable-move)的資訊。
1. 來源與目的地訂閱必須有相同的[Active Directory 租用戶](./active-directory/active-directory-howto-tenant.md)中。 若要移動至新的租用戶，請連絡支援人員。
2. 移動資源的資源提供者必須註冊的目標訂閱。 如果沒有出現，您收到錯誤訊息指出**訂閱未註冊的資源類型**。 時可能遇到此問題將資源移至新的訂閱，但該訂閱從未使用與該資源類型。 若要瞭解如何檢查註冊狀態和註冊資源提供者，請參閱[資源提供者與類型。](../resource-manager-supported-services.md#resource-providers-and-types)
4. 如果您要移動應用程式服務應用程式，您檢閱[應用程式服務限制](#app-service-limitations)。
4. 如果您要移動修復服務相關聯的資源，檢閱[修復服務限制](#recovery-services-limitations)
5. 如果您要移動部署透過傳統模式的資源，檢閱[傳統部署限制](#classic-deployment-limitations)。

## <a name="when-to-call-support"></a>若要打電話給支援人員的時機

您可以移動到本主題中所顯示的自助作業大部分的資源。 使用自助作業︰

- 將資源管理員資源。
- 移動傳統的資源，根據[傳統部署限制](#classic-deployment-limitations)。 

當您需要時，請連絡支援人員︰

- 將您的資源移至新的 Azure 帳戶 （與 Active Directory 租用戶）。
- 移動傳統的資源，但有限制問題。

## <a name="services-that-enable-move"></a>啟用移動的服務

現在，啟用 [移動到新的資源群組和訂閱的服務是︰

- API 管理
- 應用程式服務應用程式 （web 應用程式）-請參閱[應用程式服務限制](#app-service-limitations)
- 自動化
- 批次
- CDN
- 雲端服務，請參閱[傳統部署限制](#classic-deployment-limitations)
- 資料工廠
- DNS
- DocumentDB
- HDInsight 叢集
- IoT 集線器
- 索引鍵保存庫 
- 媒體服務
- 行動裝置的互動
- 通知集線器
- 操作的深入見解
- Redis 快取
- 排程器
- 搜尋
- 服務匯流排
- 儲存空間
- 儲存空間 （傳統）-請參閱[傳統部署限制](#classic-deployment-limitations)
- SQL 資料庫伺服器的資料庫與伺服器必須位於相同的資源群組。 當您將 SQL server 時，也會移所有其資料庫。
- 虛擬機器-不過，並不支援移動至新訂閱，它憑證儲存在金鑰保存庫
- 虛擬機器 （傳統）-請參閱[傳統部署限制](#classic-deployment-limitations)
- 虛擬網路

## <a name="services-that-do-not-enable-move"></a>請不要移動的服務

在目前未啟用移動資源的服務，包括︰

- 應用程式的閘道器
- 應用程式的深入見解
- Express 路由
- 修復服務地窖-也不移動修復服務保存庫相關聯的計算、 網路和儲存空間的資源，請參閱[修復服務限制](#recovery-services-limitations)。
- 憑證金鑰保存庫中儲存的虛擬機器
- 虛擬機器縮放設定
- 虛擬網路 （傳統）-請參閱[傳統部署限制](#classic-deployment-limitations)
- VPN 閘道器

## <a name="app-service-limitations"></a>應用程式服務限制

使用應用程式服務應用程式工作時，您無法移動只是應用程式服務方案。 若要將應用程式服務應用程式，是您的選項︰

- 移動到新的資源群組還沒有應用程式服務資源的資源群組中的應用程式服務方案及所有其他的應用程式服務資源。 這項需求表示您必須移動即使不是使用應用程式服務方案相關聯的應用程式服務資源。 
- 將應用程式移至不同的資源群組，但保留原始的 [資源] 群組中的所有應用程式服務方案。

如果您原始的資源群組也包含應用程式的深入見解資源，您無法移動的資源，因為應用程式的深入見解目前也不會啟用移動作業。 如果您在移動應用程式服務應用程式時包含應用程式的深入見解資源，整個移動作業失敗。 不過，應用程式的深入見解與應用程式服務方案不需要位於相同的資源群組作為應用程式的應用程式正常運作。

例如，如果您的資源群組包含︰

- **網頁的**哪些相關聯**計劃的**和**應用程式的深入見解的**
- **web b**與**計劃 b**和**應用程式-獲得深入見解-b**相關聯

是您的選項︰

- 將**網頁的****計劃的****網頁 b**和**計劃-b**
- 將**網頁的**和**web b**
- 將**網頁的**
- 移動**網頁 b**

所有其他組合涉及 [移動無法移動 （應用程式獲得深入見解） 的資源類型] 或 [留下移動的應用程式服務方案 （任何類型的應用程式服務資源） 無法保留下來的資源類型。

如果您的 web 應用程式位於不同的資源群組低於其應用程式服務方案，但您想要移動視訊至新的資源群組，您必須執行移動兩個步驟。 例如︰

- **網頁的**位於**web 群組**
- **計劃的**位於**計劃群組**
- 您想要**的網頁**和**計劃的**位於**結合群組**

若要完成此移動，請以下列順序執行兩個不同的移動作業︰

1. 將**網頁的****計劃**群組
2. 將**網頁的**和**計劃的****結合**群組。

您可以將應用程式服務的憑證移至新的資源群組或沒有問題的訂閱。 不過，如果您的 web 應用程式會包含您的外部購買應用程式上傳的 SSL 憑證，您就必須再繼續 web 應用程式刪除憑證。 例如，您可以執行下列步驟︰

1. 刪除 web 應用程式中的上傳的憑證
2. 移動 web 應用程式
3. 上傳至 web app 的憑證

## <a name="recovery-services-limitations"></a>復原服務限制

將不會啟用儲存空間，網路或計算用來設定 Azure 網站復原損毀修復的資源。 

例如，假設您已設定的內部部署電腦儲存帳戶 (Storage1) 複寫，並想得到後移轉後至 Azure 虛擬機器 (VM1) 附加至虛擬網路 (Network1) 為受保護的電腦。 您無法移動任何-Storage1、 VM1，以及 Network1-這些 Azure 資源的資源群組內的相同的訂閱或訂閱。

## <a name="classic-deployment-limitations"></a>傳統的部署限制

移動到 [傳統] 模型部署資源的選項而有所不同是否移動至新訂閱或訂閱的資源。 

### <a name="same-subscription"></a>同一份訂閱

將資源從某個資源群組移到同一份訂閱中的另一個資源群組時，就會套用下列限制︰

- 無法移動 （傳統） 的虛擬網路。
- 虛擬機器 （傳統） 必須移動雲端服務。 
- 移動包含所有其虛擬機器時，只會移動雲端服務。
- 可以一次移動一個雲端服務。
- 只有一個 （傳統） 的儲存空間帳戶可以移動，一次。
- 儲存帳戶 （傳統） 無法移動以相同的操作虛擬機器或雲端服務。

若要移動到新資源群組內同一份訂閱的傳統的資源，請使用透過[網站](#use-portal)、 [PowerShell 的 Azure](#use-powershell)、 [Azure CLI](#use-azure-cli)或[REST API](#use-rest-api)的標準移動作業。 當您移動資源管理員資源的使用，您可以使用相同的作業。

### <a name="new-subscription"></a>新的訂閱

將資源移至新訂閱時，就會套用下列限制︰

- 在訂閱中的所有傳統資源必須移動以相同的操作。
- 目標訂閱不能包含任何其他傳統的資源。
- 移動僅可透過個別的 REST API 傳統移動的要求。 移至新訂閱的傳統的資源時，無法使用標準的資源管理員移動命令。

若要移動至新訂閱的傳統的資源，您必須使用傳統的資源特定的其餘部分作業。 執行下列步驟，將傳統的資源移至新訂閱。

1. 核取來源訂閱的跨訂閱移動可以參與。 使用下列作業︰

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     邀請本文中，包括︰

         {
           "role": "source"
         }

     驗證作業的回應是以下列格式︰

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. 核取目的地訂閱的跨訂閱移動可以參與。 使用下列作業︰

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     邀請本文中，包括︰

         {
           "role": "target"
         }

     回應位於來源訂閱驗證相同的格式。

3. 如果這兩個訂閱通過驗證，將傳統的所有資源一個訂閱到另一個訂閱進行下列操作︰

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    邀請本文中，包括︰

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

可能要幾分鐘的時間執行作業。 

## <a name="use-portal"></a>使用入口網站

若要將資源移至**同一份訂閱**中的新 [資源] 群組中，選取包含這些資源的資源群組，然後選取 [**移動**] 按鈕。

![移動資源](./media/resource-group-move-resources/edit-rg-icon.png)

或者，若要移動至**新訂閱**的資源，請選取包含這些資源的資源群組，然後選取 [編輯訂閱] 圖示。

![移動資源](./media/resource-group-move-resources/change-subscription.png)

選取要移動的資源和目的地資源群組]。 確認您需要更新這些資源的指令碼，然後選取**[確定]**。 如果您在上一個步驟中選取 [編輯訂閱] 圖示，您也必須選取目的地訂閱。

![選取目的地](./media/resource-group-move-resources/select-destination.png)

在**通知**，您會看到正在移動作業。

![顯示移動狀態](./media/resource-group-move-resources/show-status.png)

完成後，您會收到通知的結果。

![顯示移動結果](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>使用 PowerShell

若要將現有的資源移到另一個資源群組或訂閱，使用 [**移動 AzureRmResource** ] 命令。

第一個範例會示範如何將某個資源移到新的資源群組。

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

第二個範例會示範如何將多個資源移到新的資源群組。

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

若要移動至新訂閱，包括**DestinationSubscriptionId**參數的值。

系統要求您確認您要移動的指定的資源。

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>使用 Azure CLI

若要將現有的資源移到另一個資源群組或訂閱，使用**移動 azure 資源**命令。 您需要提供移動資源的識別碼。 您可以取得資源識別碼，使用下列命令︰

    azure resource list -g sourceGroup --json

會傳回下列格式︰

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

下列範例會示範如何將儲存空間帳戶移至新的資源群組。 **-I**參數中提供的資源識別碼逗點分隔清單的移動。

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
系統要求您確認您要移動指定的資源。

## <a name="use-rest-api"></a>使用 REST API

若要將現有的資源移到另一個資源群組或訂閱，請執行︰

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

邀請本文中，您可以指定目標資源群組和資源，若要移動。 如需有關移動其餘作業的詳細資訊，請參閱[移動資源](https://msdn.microsoft.com/library/azure/mt218710.aspx)。


## <a name="next-steps"></a>後續步驟
- 若要瞭解 PowerShell cmdlet 來管理您的訂閱，請參閱[使用 PowerShell 的 Azure 與資源管理員](powershell-azure-resource-manager.md)。
- 若要瞭解 Azure CLI 命令，管理您的訂閱，請參閱[使用 Azure CLI 與資源管理員](xplat-cli-azure-resource-manager.md)。
- 若要瞭解入口網站的功能，管理您的訂閱，請參閱[使用 Azure 入口網站管理資源](./azure-portal/resource-group-portal.md)。
- 若要瞭解如何將邏輯組織套用至您的資源，請參閱[使用標籤以組織您的資源](resource-group-using-tags.md)。
