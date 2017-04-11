## <a name="virtual-network"></a>虛擬網路
虛擬網路 (VNET) 和子網路資源協助定義 Azure 中執行的工作負載的安全邊界。 VNet 在於位址空間，定義為 CIDR 區塊的集合。 

>[AZURE.NOTE] 網路管理員熟悉 CIDR 示意圖。 如果您不熟悉 CIDR，[深入了解更多](http://whatismyipaddress.com/cidr)。

![具有多個子網路 VNet](./media/resource-groups-networking/Figure4.png)

VNets 包含下列屬性。

|屬性|描述|範例值|
|---|---|---|
|**addressSpace**|位址首碼 CIDR 法 VNet 組成的集合|192.168.0.0/16|
|**子網路**|子網路 VNet 組成的集合|請參閱[子網路](#Subnets)下方。|
|**ipAddress**|指派給物件的 IP 位址。 這是唯讀的屬性。|104.42.233.77|

### <a name="subnets"></a>子網路
子網路子資源的 VNet，而有助於定義在使用 IP 位址首碼 CIDR 區塊的地址空格的區段。 Nic 可以新增至子網路，並連線到 Vm，提供各種工作負載的連線。

子網路包含下列屬性。 

|屬性|描述|範例值|
|---|---|---|
|**addressPrefix**|單一組成 CIDR 法子網路的地址前置詞|192.168.1.0/24|
|**networkSecurityGroup**|套用至子網路 NSG|請參閱[NSGs](#Network-Security-Group)|
|**routeTable**|路由表套用至子網路|請參閱[UDR](#Route-table)|
|**ipConfigurations**|連線到子網路 Nic 所用的 IP 設定物件的集合|請參閱[UDR](#Route-table)|


範例 VNet JSON 格式︰

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>其他資源

- 取得更多有關[VNet](../articles/virtual-network/virtual-networks-overview.md)。
- 讀取 VNets [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt163650.aspx)。
- 讀取子網路[REST API 參考文件](https://msdn.microsoft.com/library/azure/mt163618.aspx)。