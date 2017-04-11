<properties
   pageTitle="資源管理員範本儲存 |Microsoft Azure"
   description="會顯示資源管理員的結構描述部署儲存為範本的帳戶。"
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>儲存帳戶範本結構描述

建立儲存的帳戶。

## <a name="schema-format"></a>結構描述格式

若要建立儲存帳戶，請新增下列結構描述至您的範本的 [資源] 區段。

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>值

下表描述您要設定結構描述中的值。

| 名稱 | 值 |
| ---- | ---- |
| 類型 | 列舉<br />所需<br />**Microsoft.Storage/storageAccounts**<br /><br />若要建立資源類型。 |
| apiVersion | 列舉<br />所需<br />**2015 06-15**或**2015年 05-01-預覽**<br /><br />要用來建立資源 API 版本。 | 
| 名稱 | 字串<br />所需<br />介於 3 至 24 個字元，只數字和英文小寫字母。<br /><br />若要建立的儲存體帳戶名稱。 在所有 Azure，名稱必須都是唯一的。 請考慮使用[uniqueString](resource-group-template-functions.md#uniquestring)函數與您的命名慣例，在下面的範例所示。 |
| 位置 | 字串<br />所需<br />支援儲存的帳戶區域。 若要判斷有效的區域，請參閱[支援的區域](resource-manager-supported-services.md#supported-regions)。<br /><br />如果要主控儲存帳戶的區域。 |
| 屬性 | 物件<br />所需<br />[屬性的物件](#properties)<br /><br />指定儲存建立的帳戶類型的物件。 |

<a id="properties" />
### <a name="properties-object"></a>屬性的物件

| 名稱 | 值 |
| ---- | ---- | 
| accountType | 字串<br />所需<br />**Standard_LRS**、 **Standard_ZRS**、 **Standard_GRS**、 **Standard_RAGRS**或**Premium_LRS**<br /><br />儲存帳戶類型。 允許的值對應到標準本機多餘、 標準區域重複、 標準的地理多餘、 標準讀取權限地理-多餘，和進階版本機重複。 如需這些帳戶類型的資訊，請參閱[Azure 儲存體複寫](./storage/storage-redundancy.md )。 |

    
## <a name="examples"></a>範例

下列範例會根據 [資源群組識別碼的唯一名稱部署標準本機多餘的儲存空間帳戶。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>快速入門範本

有許多包含儲存帳戶的快速入門範本。 下列範本說明一些常見的案例︰

- [建立標準儲存的帳戶](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [簡單的 Windows VM 的部署](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [簡單的 Linux VM 的部署](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [建立 CDN 設定檔，以儲存帳戶為原點 CDN 端點](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [建立使用 Powershell DSC 延伸 9 Vm 高 Availabilty SharePoint 伺服器陣列](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [簡單的部署 5 的節點安全 WAD 啟用的服務布料的轉印圖樣叢集](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [從 Windows 的圖像與 4 的空白資料磁碟建立虛擬機器](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>後續步驟

- 如需儲存空間的一般資訊，請參閱[簡介 Microsoft Azure 儲存體](./storage/storage-introduction.md)。
- 例如範本的新儲存帳戶使用虛擬機器，請參閱[部署簡單 Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ] 或 [[部署簡單的 Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)。
