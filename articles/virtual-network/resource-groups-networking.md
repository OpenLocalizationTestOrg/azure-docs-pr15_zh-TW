<properties
   pageTitle="網路資源提供者概觀 |Microsoft Azure"
   description="瞭解新的網路資源提供者中 Azure 資源管理員"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>網路資源提供者
今天商務成功，加強需要是建置和敏捷式、 彈性、 安全且可重複的方式管理大型網路應用程式的能力。 Azure 資源管理員 (ARM) 可讓您建立這類應用程式，以單一集合的資源] 群組中的資源。 這類資源是透過 ARM 在各種資源提供者管理。

Azure 資源管理員必須使用不同的資源提供者提供資源的存取權。 有三個主要的資源提供者︰ 網路、 儲存及計算。 這份文件討論的特性和網路資源提供者的優點包括︰

- **中繼資料**– 您可以新增資訊至 [資源使用標籤。 這些標記可用來追蹤資源使用量資源群組和訂閱。
- **較大的控制，您的網路**-資源彈性與結合的網路，您可以控制他們更細緻的方式。 這表示您需要更多的彈性，管理網路的資源。
- **更快速地設定**-因為網路資源彈性與結合，您可以建立及協調平行網路資源。 此已經大幅降低設定時間。
- **角色基礎存取控制**RBAC 提供預設角色，與特定的安全性範圍，除了允許建立的自訂安全管理角色。
- **更容易管理及部署**-容易部署及管理應用程式，因為您可以可以建立整個應用程式堆疊為單一集合的資源群組中的資源。 更快速部署，因為您可以只提供的範本 JSON 內容部署。
- **快速自訂**-您可以使用宣告式範本，若要啟用重複及快速部署自訂。
- **可重複自訂**-您可以使用宣告式範本，若要啟用重複及快速部署自訂。
- **管理介面**-您可以使用任何的下列介面，來管理您的資源︰
    - 其餘基礎 API
    - PowerShell
    - .NET SDK
    - Node.JS SDK
    - Java SDK
    - Azure CLI
    - 預覽入口網站
    - ARM 範本語言

## <a name="network-resources"></a>網路資源
您現在可以而不是由全部透過單一計算資源 （虛擬機器） 管理獨立管理網路資源。 這可確保較高的彈性和靈活性中撰寫資源群組中的複雜和大型的縮放比例基礎結構。

範例部署涉及多層的應用程式的概念性檢視會顯示下方。 您看到，例如 Nic、 公用 IP 位址和 Vm，每個資源可獨立管理。

![網路資源模型](./media/resource-groups-networking/Figure2.png)

每個資源包含一組通用的屬性，並設定其個別的屬性。 常見的內容是︰

|屬性|描述|範例值|
|---|---|---|
|**名稱**|唯一的資源名稱。 每個資源類型有自己的命名限制。|PIP01，VM01，NIC01|
|**位置**|Azure 的資源所在的區域|westus eastus|
|**識別碼**|唯一 URI 基礎識別碼|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

您可以檢查以下各節中的資源的個別屬性。

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>管理介面
您可以管理 Azure 網路資源使用不同的介面。 在這份文件中，我們將著重於列的介面︰ REST API 和範本。

### <a name="rest-api"></a>REST API
如先前所述，可以透過各種不同的介面，包括 REST API、.NET SDK、 Node.JS SDK、 Java SDK、 PowerShell、 CLI、 Azure 入口網站及範本管理網路資源。

Rest API 的符合 HTTP 1.1 通訊協定規格。 一般 URI 結構的 api 會介紹如下︰

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

而且大括弧括住的參數代表下列元素︰

- **訂閱 id** -您 Azure 訂閱的識別碼。
- **資源者命名空間**-提供者所用的命名空間。 網路資源提供者的值就是*Microsoft.Network*。
- **地區名稱**的 Azure 地區名稱

當撥打電話至 REST API 支援下列 HTTP 方法︰

- **將**-用來建立指定類型、 資源修改資源屬性或變更資源之間的關聯。
- **取得**-用來擷取能夠資源的資訊。
- **刪除**-用來刪除現有的資源。

要求並回應符合 JSON 裝載格式。 如需詳細資訊，請參閱[Azure 資源管理 Api](https://msdn.microsoft.com/library/azure/dn948464.aspx)。

### <a name="arm-template-language"></a>ARM 範本語言
除了管理資源強制 （透過 Api 或 SDK），您也可以使用宣告式的程式設計樣式來建立和管理網路資源使用 ARM 範本的語言。

下面 – 提供範本的範例表示法

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

範本是主要 JSON 描述的資源，並插入透過參數的執行個體值。 下列範例可以用於建立虛擬網路具有 2 的子網路。

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

您可以選擇時使用的範本，以手動方式提供的參數值，或者您可以使用參數檔案。 下列範例會顯示可能的一組參數值來搭配上述範本︰

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


使用範本的主要優點是︰

- 您可以建立複雜的基礎結構，以宣告式的 [資源] 群組中。 ARM 會處理的建立的資源，包括相依性管理協調。
- 基礎結構可以建立可重複方式跨不同地區和在區域內只要變更參數。
- 宣告式引導短前置重疊時間建置範本和推出基礎結構。

範例範本，請參閱[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)。

如需有關 ARM 範本語言的詳細資訊，請參閱[Azure 資源管理員範本語言](../resource-group-authoring-templates.md)。

上述範例範本用途的虛擬網路和子網路資源。 有其他網路資源，您可以使用如下所示︰

### <a name="using-a-template"></a>使用範本

使用 PowerShell AzureCLI，或是執行 [按一下以從 GitHub 部署可以從範本部署至 Azure 服務。 若要部署服務 GitHub 中的範本，請執行下列步驟︰

1. 從 GitHub 開啟 template3 檔案。 例如，開啟[兩個子網路虛擬網路](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network)。
2. 按一下 [**部署至 Azure**，然後登入使用您的認證 Azure 入口網站。
3. 驗證範本，然後再按一下 [**儲存**。
4. 按一下 [**編輯參數**，然後選取一個位置，例如*西美國*，vnet 和子網路。
5. 如有必要，變更**ADDRESSPREFIX**和**SUBNETPREFIX**參數，，然後按一下**[確定]**。
6. 按一下 [**選取資源群組**]，然後按一下您想要新增的 vnet 及子網路資源群組。 或者，您也可以按一下 [**建立新的或**建立新的資源群組。
3. 按一下 [**建立**]。 請注意 [顯示**部署佈建的範本**] 方塊。 一旦完成部署後，您會看到下列其中一個畫面。

![範例範本部署](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>後續步驟

[Azure 資源管理員範本語言](../resource-group-authoring-templates.md)

[Azure 網路-經常使用的範本](https://github.com/Azure/azure-quickstart-templates)

[計算資源的提供者](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)
