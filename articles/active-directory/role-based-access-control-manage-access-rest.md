<properties
    pageTitle="管理角色型存取控制以 REST API"
    description="管理角色型存取控制以 REST API"
    services="active-directory"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="multiple"
    ms.tgt_pltfrm="rest-api"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-role-based-access-control-with-the-rest-api"></a>管理角色型存取控制以 REST API

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

角色型存取控制 (RBAC) 中 Azure 入口網站和 Azure 資源管理員 API 可協助您管理存取您的訂閱和精密的層級的資源。 使用此功能，您都可以在特定範圍，指派給他們的某些角色來授與 Active Directory 使用者、 群組或服務原則的存取權。

## <a name="list-all-role-assignments"></a>列出所有的角色指派

列出所有在指定的範圍和 subscopes 的角色指派。

到清單中的角色指派]，您必須擁有存取權`Microsoft.Authorization/roleAssignments/read`範圍內的作業。 所有的內建的角色會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI**取得**的方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

URI，內進行下列替換若要自訂您的要求︰

1. *{範圍}*換成您要清單中的角色指派的範圍。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. 取代 2015年-07-01 *{api 版本}* 。

3. *{篩選}*換成您想要套用篩選的角色指派清單的條件︰

  - 清單只指定的範圍，不包括在 subscopes 角色指派角色的指派︰`atScope()`    
  - 清單中的特定使用者、 群組或應用程式的角色指派︰`principalId%20eq%20'{objectId of user, group, or service principal}'`  
  - 列出為特定的使用者，包括繼承自群組的角色指派 |`assignedTo('{objectId of user}')`

### <a name="response"></a>回應

狀態碼︰ 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>取得角色指派的相關資訊

取得所指定的角色指派識別碼單一的角色指派的相關資訊。

若要取得角色指派的相關資訊，您必須存取`Microsoft.Authorization/roleAssignments/read`作業。 所有的內建的角色會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI**取得**的方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI，內進行下列替換若要自訂您的要求︰

1. *{範圍}*換成您要清單中的角色指派的範圍。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. *{角色指派-識別碼}*取代的角色指派 GUID 識別碼。

3. 取代 2015年-07-01 *{api 版本}* 。

### <a name="response"></a>回應

狀態碼︰ 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>建立角色指派

針對指定的主體授與指定的角色指定範圍內建立角色指派。

若要建立角色指派，您必須存取`Microsoft.Authorization/roleAssignments/write`作業。 內建的角色，只有*擁有者*和*使用者存取系統管理員*會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI**放**方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI，內進行下列替換若要自訂您的要求︰

1. *{範圍}*換成您要建立的角色指派的範圍。 當您建立的角色指派在上層範圍時，所有的子範圍都會繼承相同的角色指派。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1   
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. *{角色指派-識別碼}*換成新的 GUID，成為新的角色指派的 GUID 識別碼。

3. 取代 2015年-07-01 *{api 版本}* 。

邀請內文中，提供下列格式中的值︰

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| 項目名稱     | 所需 | 類型   | 描述 |
|------------------|----------|--------|-------------|
| roleDefinitionId | [是]      | 字串 | 角色的識別碼。 識別項格式為︰`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId      | [是]      | 字串 | Azure AD 的主體 （使用者、 群組或服務本金） 指派給角色的 objectId。 |

### <a name="response"></a>回應

狀態碼︰ 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>刪除角色指派

刪除角色指派在指定的範圍。

若要刪除角色指派，您必須存取`Microsoft.Authorization/roleAssignments/delete`作業。 內建的角色，只有*擁有者*和*使用者存取系統管理員*會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI 中的 [**刪除**的方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI，內進行下列替換若要自訂您的要求︰

1. *{範圍}*換成您要建立的角色指派的範圍。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. 取代的角色指派識別碼 GUID *{角色指派-識別碼}* 。

3. 取代 2015年-07-01 *{api 版本}* 。

### <a name="response"></a>回應

狀態碼︰ 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>列出所有的角色

列出所有可供在指定的範圍內的工作分派的角色。

清單角色，您必須擁有存取權`Microsoft.Authorization/roleDefinitions/read`範圍內的作業。 所有的內建的角色會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI**取得**的方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

URI，內進行下列替換若要自訂您的要求︰

1. *{範圍}*換成您要的角色] 清單中的範圍。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源 /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. 取代 2015年-07-01 *{api 版本}* 。

3. *{篩選}*換成您想要套用篩選的角色清單的條件︰

  - 清單其子範圍的工作分派在指定的範圍，以及任何可用的角色︰`atScopeAndBelow()`
  - 搜尋角色確切的顯示名稱︰ `roleName%20eq%20'{role-display-name}'`。 使用 URL 編碼形式的角色確切的顯示名稱。 例如，`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>回應

狀態碼︰ 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>取得角色的相關資訊

取得所指定的角色定義識別碼單一角色的相關資訊。 若要使用的顯示名稱的單一角色的相關資訊，請參閱[] 清單中所有的角色](role-based-access-control-manage-access-rest.md#list-all-roles)。

若要取得角色的相關資訊，您必須存取`Microsoft.Authorization/roleDefinitions/read`作業。 所有的內建的角色會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI**取得**的方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI，內進行下列替換若要自訂您的要求︰

1. *{範圍}*換成您要清單中的角色指派的範圍。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. *{角色-定義-識別碼}*取代角色定義的 GUID 識別碼。

3. 取代 2015年-07-01 *{api 版本}* 。

### <a name="response"></a>回應

狀態碼︰ 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>建立自訂的角色
建立自訂的角色。

若要建立自訂的角色，您必須存取`Microsoft.Authorization/roleDefinitions/write`上所有作業`AssignableScopes`。 內建的角色，只有*擁有者*和*使用者存取系統管理員*會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI**放**方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI，內進行下列替換若要自訂您的要求︰

1. 取代自訂的角色的第一個*AssignableScope* *{範圍}* 。 下列範例會顯示如何指定之不同層級的範圍。

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. *{角色-定義-識別碼}*換成新 GUID，它會變成 GUID 的識別項新的自訂角色。

3. 取代 2015年-07-01 *{api 版本}* 。

邀請內文中，提供下列格式中的值︰

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 項目名稱 | 所需 | 類型 | 描述 |
|--------------|----------|------|-------------|
| 名稱         | [是] | 字串   | 自訂角色 GUID 識別項。    |
| properties.roleName               | [是] | 字串   | 顯示自訂角色的名稱。 最大值是 128 個字元。                        |
| properties.description            | 無  | 字串   | 自訂角色的描述。 最大值 1024年個字元。                                               |
| properties.type                   | [是] | 字串   | 設定為 「 CustomRole 」。                                         |
| properties.permissions.actions    | [是] | String] | 指定自訂的角色授與的作業的巨集指令字串的陣列。             |
| properties.permissions.notActions | 無  | String] | 指定要排除的自訂角色授與的作業的作業的巨集指令字串的陣列。 |
| properties.assignableScopes       | [是] | String] | 在您可以使用自訂的角色的範圍陣列。   |

### <a name="response"></a>回應

狀態碼︰ 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>更新自訂的角色

修改自訂的角色。

若要修改自訂角色，您必須存取`Microsoft.Authorization/roleDefinitions/write`上所有作業`AssignableScopes`。 內建的角色，只有*擁有者*和*使用者存取系統管理員*會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI**放**方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI，內進行下列替換若要自訂您的要求︰

1. 取代自訂的角色的第一個*AssignableScope* *{範圍}* 。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. *{角色-定義-識別碼}*取代自訂 GUID 識別碼。

3. 取代 2015年-07-01 *{api 版本}* 。

邀請內文中，提供下列格式中的值︰

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 項目名稱 | 所需 | 類型 | 描述 |
|--------------|----------|------|-------------|
| 名稱         | [是]      | 字串 | 自訂角色 GUID 識別項。 |
| properties.roleName | [是] | 字串 | 更新自訂角色的顯示名稱。 |
| properties.description | 無 | 字串 | 更新自訂角色的描述。 |
| properties.type | [是] | 字串 | 設定為 「 CustomRole 」。 |
| properties.permissions.actions | [是] | String] | 指定要更新的自訂角色授與存取權的作業的巨集指令字串的陣列。 |
| properties.permissions.notActions | 無 | String] | 指定要排除的作業更新自訂角色授與作業的巨集指令字串的陣列。 |
| properties.assignableScopes | [是] | String] | 可以在其中使用更新的自訂角色的範圍陣列。 |

### <a name="response"></a>回應

狀態碼︰ 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>刪除自訂的角色

刪除自訂的角色。

若要刪除自訂的角色，您必須存取`Microsoft.Authorization/roleDefinitions/delete`上所有作業`AssignableScopes`。 內建的角色，只有*擁有者*和*使用者存取系統管理員*會授與存取這項作業。 如需有關角色指派及管理存取 Azure 資源的詳細資訊，請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

### <a name="request"></a>要求

使用下列 URI 中的 [**刪除**的方法︰

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI，內進行下列替換若要自訂您的要求︰

1. *{範圍}*換成您要刪除的角色定義範圍。 下列範例會示範如何指定不同層級的範圍︰

  - 訂閱︰ /subscriptions/ {訂閱識別碼}  
  - 資源群組︰ /subscriptions/ {訂閱識別碼} / resourceGroups/myresourcegroup1  
  - 資源︰ /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. *{角色-定義-識別碼}*取代的自訂角色 GUID 角色定義識別碼。

3. 取代 2015年-07-01 *{api 版本}* 。

### <a name="response"></a>回應

狀態碼︰ 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```


[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
