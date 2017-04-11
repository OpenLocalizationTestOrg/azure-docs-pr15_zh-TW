## <a name="traffic-manager-profile"></a>流量管理員設定檔

流量經理和其子端點資源啟用 DNS 路由至 Azure 中和 Azure 以外的端點。 由路由原則方式來管理這類流量通訊群組。 流量管理員也可讓端點健康監視，而流量分發適當的端點的狀況。 

| 屬性 | 描述 |
|---|---|
|**trafficRoutingMethod**| 可能的值為*效能*、*加權*和*優先順序* | 
| **dnsConfig** | 設定檔的 FQDN | 
| **通訊協定** | 監控通訊協定可能的值為*HTTP*和*HTTPS*|
| **連接埠** | 監控連接埠 |  
| **路徑** | 監控路徑 |
| **結束點** |  容器的端點資源 | 

### <a name="endpoint"></a>結束點 

端點是子資源流量管理員設定檔。 它會表示服務或使用者散發流量的 web 端點根據流量管理員設定檔資源中的已設定原則。 

| 屬性 | 描述 | 
|---|---| 
| **類型** |  類型的端點，可能的值為*Azure 結束點*，*外部端點*和*巢狀端點* | 
| **targetResourceId** |  服務或網頁的端點的公用 IP 位址。 這可能是 Azure 或外部端點。 | 
| **粗細** | 用於流量管理端點粗細。 | 
| **優先順序** | 結束點，用於定義容錯移轉動作的優先順序 |

範例的資料傳輸 Manager Json 格式︰ 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>其他資源

如需詳細資訊，閱讀[REST API 文件的流量管理員](https://msdn.microsoft.com/library/azure/mt163664.aspx)。
