<properties
    pageTitle="使用標籤以組織 Azure 資源 |Microsoft Azure"
    description="顯示如何套用的標籤來組織的帳單及管理資源。"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>使用標籤以組織 Azure 資源

資源管理員可讓您以邏輯方式組織套用標籤的 [資源。 標記包含您所定義的內容，找出資源的金鑰/值組。 若要標記為屬於相同的類別的資源，請套用相同標籤這些資源。

當您與特定標記的資源時，您會看到資源從您的所有資源群組。 您不是限制為只有相同資源的群組中，這可讓您組織部署關聯無關的方式資源的資源。 當您需要組織的帳單或管理資源，標籤就會很有幫助。

您新增到資源或資源群組每一個標籤就會自動新增至訂閱整個分類。 您也可以預先分類填入您的訂閱標籤名稱與您想要使用的資源在未來標記的值。

每個資源或資源群組不能超過 15 的標記。 標籤名稱為 512 個字元，並標記值僅限於 256 個字元。

> [AZURE.NOTE] 您只能在支援資源管理員作業的資源套用標籤。 如果您透過傳統部署模型建立虛擬機器、 虛擬網路或儲存空間 (例如透過 [傳統] 入口網站)，您無法將標籤套用至該資源。 若要標記的支援，重新部署這些資源透過資源管理員。 所有其他的資源支援標記。

## <a name="templates"></a>範本

若要標記資源部署時，只要將**標籤**項目新增至您要部署的資源，並提供 [標籤名稱] 和 [值。 [標籤名稱] 和 [值不需要預先存在於中您的訂閱。 您可以針對各個資源提供最多為 15 的標籤。

下列範例顯示含有標記的儲存空間帳戶。

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

資源管理員目前不支援處理的標籤名稱和值的物件。 相反地，傳遞物件標籤值，但您必須仍指定標籤名稱，如下列範例所示。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>入口網站

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure CLI

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API

入口網站和 PowerShell 兩者都可以使用[資源管理員 REST API](https://msdn.microsoft.com/library/azure/dn848368.aspx)幕後運作。 如果您需要整合到另一個環境標記，取得標籤與 [資源識別碼上取得並更新修補程式通話的標籤設定。


## <a name="tags-and-billing"></a>標記與帳單

如需支援服務，您可以使用標籤，將付款的資料分組。 例如，[虛擬機器整合與 Azure 資源管理員](./virtual-machines/virtual-machines-windows-compare-deployment-models.md)可讓您定義，並套用的標籤來組織虛擬機器的帳單的使用方式。 如果您正在執行多個 Vm 不同的組織，您可以在成本中心所使用的標籤] 群組中的使用方式。  
您也可以使用 [標記，以執行階段環境中; 分類成本例如，在生產環境中執行的 Vm 帳單使用方式。

您可以擷取標記的[Azure 資源使用狀況和 RateCard Api](billing-usage-rate-card-overview.md) ，或是使用逗點分隔值 (CSV) 檔案的相關資訊。 您可以下載使用檔案從[Azure 帳戶入口網站](https://account.windowsazure.com/)或[EA 入口網站](https://ea.azure.com)。 如需以程式設計方式存取帳單資訊的詳細資訊，請參閱[獲得深入見解到消耗 Microsoft Azure 資源](billing-usage-rate-card-overview.md)。 如 REST API 作業，請參閱[Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。

當您下載服務支援帳單和標籤的標籤會顯示在 [**標記**] 欄中的使用狀況 CSV 檔案。 如需詳細資訊，請參閱[瞭解為 Microsoft Azure 帳單](billing/billing-understand-your-bill.md)。

![請參閱帳單中的標籤](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>後續步驟

- 您可以透過您的訂閱，以自訂的原則套用限制和慣例。 您所定義的原則可能會要求所有資源的特定標籤的值。 如需詳細資訊，請參閱[使用原則管理資源，並控制存取權](resource-manager-policy.md)。
- 部署資源時，使用 PowerShell 的 Azure 簡介資訊，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](./powershell-azure-resource-manager.md)。
- 部署資源時，使用 Azure CLI 簡介資訊，請參閱[使用 Mac、 Linux，和 Azure 資源管理 Windows Azure CLI](./xplat-cli-azure-resource-manager.md)。
- 若要使用入口網站簡介資訊，請參閱[使用 Azure 入口網站管理 Azure 資源](./azure-portal/resource-group-portal.md)  
