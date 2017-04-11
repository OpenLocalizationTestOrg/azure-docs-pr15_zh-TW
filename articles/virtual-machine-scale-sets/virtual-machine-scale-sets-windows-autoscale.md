<properties
    pageTitle="自動調整大小 Windows 虛擬機器縮放比例設定 |Microsoft Azure"
    description="設定 Windows 虛擬機器縮放比例設定使用 PowerShell 的 Azure 自動縮放"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>自動在虛擬機器縮放比例設定縮放機器

虛擬機器縮放比例設定讓您輕鬆地部署及管理相同的虛擬機器作為一組。 縮放比例設定為 hyperscale 應用程式]，提供高調整及自訂計算圖層，支援 Windows 平台圖像、 Linux 平台圖像、 自訂圖像和副檔名。 如需有關縮放比例設定的詳細資訊，請參閱[虛擬機器縮放] 設定](virtual-machine-scale-sets-overview.md)。

本教學課程教您如何建立 Windows 虛擬機器的縮放設定，並自動調整機器，在設定。 您建立設定，並設定來建立 Azure 資源管理員範本，並將其使用 PowerShell 的 Azure 部署縮放比例。 如需有關範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 若要進一步瞭解自動縮放比例縮放設定，請參閱[自動縮放比例與虛擬機器縮放] 設定](virtual-machine-scale-sets-autoscale-overview.md)。

本文中，您部署下列資源和擴充功能︰

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

如需有關資源管理員資源的詳細資訊，請參閱[Azure 計算、 網路和儲存提供者下 Azure 資源管理員](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)。

## <a name="step-1-install-azure-powershell"></a>步驟 1︰ 安裝 Azure PowerShell

如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入 Azure 的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>步驟 2︰ 建立資源群組和儲存帳戶

1. **建立資源群組**– 所有資源必須都部署資源群組。 使用[新增 AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)建立名為**vmsstestrg1**的資源群組。

2. **建立儲存帳戶**– 此儲存帳戶是儲存範本。 使用[新增 AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx)建立名為**vmsstestsa**儲存帳戶。

## <a name="step-3-create-the-template"></a>步驟 3︰ 建立範本
Azure 資源管理員範本可讓您的部署及管理 Azure 資源一起使用 JSON 描述的資源和相關聯的部署參數值。

1. 在您最愛的編輯器中，建立檔案 C:\VMSSTemplate.json 並新增初始 JSON 結構支援範本。

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. 參數並非必要，但它們提供範本部署時，請輸入值。 新增參數父項目新增至範本的下列參數。

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - 設定個別虛擬機器用來存取刻度電腦的名稱。
    - 儲存範本的儲存體帳戶名稱。
    - 若要在縮放比例設定一開始建立虛擬機器中的數字。
    - 名稱和虛擬機器上系統管理員帳戶的密碼。
    - 建立以支援小數位數的資源的名稱前置詞設定。

3. 變數可以用於範本，若要指定可能會經常變更的值或需要建立的參數值組合的值。 新增這些變數變數父項目新增至範本。

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
          "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

  - DNS 網路介面，可使用的名稱。
    - IP 位址的名稱和虛擬網路和子網路的前置字元。
    - 名稱和識別項的虛擬的網路負載平衡器及網路介面。
    - 儲存帳戶名稱，以比例電腦相關聯的帳戶設定。
    - 診斷副檔名虛擬機器上所安裝的設定。 如需有關診斷擴充功能的詳細資訊，請參閱[建立的監控和範本 Azure 資源管理員的診斷與 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)。

4. 新增資源父項目新增至範本的儲存空間帳戶資源。 此範本使用循環播放，建立建議的五個儲存帳戶的作業系統磁碟和診斷資料的儲存位置。 此設定的帳戶可支援最多 100 個虛擬機器中縮放設定，這是目前的最大值。 以字母指示加上的範本在參數中所提供的前置詞的變數中定義的名稱為每個儲存帳戶。

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. 加入虛擬網路資源。 如需詳細資訊，請參閱[網路資源提供者](../virtual-network/resource-groups-networking.md)。

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. 新增負載平衡器與網路介面可以使用的公用 IP 位址資源。

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. 新增負載平衡器資源所使用的縮放比例設定。 如需詳細資訊，請參閱[Azure 資源管理員的支援負載平衡器](../load-balancer/load-balancer-arm.md)。

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. 新增網路介面資源所使用的個別的虛擬機器。 縮放比例設定在電腦無法存取的公用 IP 位址，因為不同的虛擬機器中建立相同的虛擬網路遠端存取機器。

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. 新增個別的虛擬機器中的縮放比例設定為相同的網路。

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"        
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
              ]
            }
          }
        },

10. 新增的虛擬機器比例設定資源，然後指定診斷副檔名所安裝的所有的虛擬機器，在縮放比例設定]。 許多此資源設定類似的虛擬機器資源。 主要差異是指定的虛擬機器數縮放比例設定和 upgradePolicy 指定如何進行更新虛擬機器中的容量項目。 縮放比例設定不會建立直到建立的所有儲存帳戶與指定 dependsOn 項目。

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "MicrosoftWindowsServer",
                      "offer": "WindowsServer",
                      "sku": "2012-R2-Datacenter",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                        "properties": {
                          "publisher": "Microsoft.Azure.Diagnostics",
                          "type": "IaaSDiagnostics",
                          "typeHandlerVersion": "1.5",
                          "autoUpgradeMinorVersion": true,
                          "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. 新增 autoscaleSettings 資源定義的縮放比例設定調整根據在縮放比例設定電腦的處理器使用量的方式。

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    在此教學課程中，這些值很重要︰

    - **metricName** -這個值是我們 wadperfcounter 變數中所定義的效能計數器相同。 使用該變數，診斷副檔名收集**Processor(_Total)\%處理器時間**計數器。
    - **metricResourceUri**此值是資源識別碼虛擬機器縮放比例設定。
    - **timeGrain** – 此值會收集指標的方式。 在此範本，它會設定為一分鐘。
    - **統計資料**-此值會決定計量組合以容納自動縮放比例動作的方式。 可能的值︰ 平均值、 最小值] 的最大值。 在此範本，會收集虛擬機器的平均總 CPU 使用率。
    - **timeWindow** – 此值會的收集執行個體資料時的範圍。 它必須介於 5 到 12 小時。
    - **timeAggregation** – 此值會決定如何是否會收集的資料合併一段時間。 預設值是平均值。 可能的值︰ 平均值、 最小值、 最大值，最後一個、 總計、 計數。
    - **運算子**– 這個值是用來比較公制資料和臨界值的運算子。 可能的值︰ NotEquals GreaterThan、 GreaterThanOrEqual、 LessThan、 LessThanOrEqual 等於。
    - **閥值**– 這個值是觸發比例巨集指令的值。 此範本中機器會新增至 [設定時的平均的 CPU 使用率之間集中機器超過 50%的比例。
    - **方向**– 此值會決定達成臨界值時應該採取的動作。 可能的值會增加或減少。 此範本，在縮放比例設定的虛擬機器而增加︰ 如果臨界值超過 50%，在 [已定義的時間] 視窗中。
    - **輸入**– 這個值是動作應該發生，必須設定為 [ChangeCount 類型。
    - **值**– 這個值是新增或移除縮放比例設定虛擬機器數。 此值必須大於或等於 1。 預設值為 1。 在此範本機器中的小數位數的數字設定增加 1 符合閾值時。
    - **cooldown** – 這個值是時間的下一個動作前，自上次的縮放比例動作等待量。 此值必須是一分鐘之間一週。

12. 儲存範本檔案。    

## <a name="step-4-upload-the-template-to-storage"></a>步驟 4︰ 範本上傳至儲存體

可以上傳的範本，只要您知道名稱與您在步驟 1 建立的儲存空間帳戶的主索引鍵。

1.  在 Microsoft Azure PowerShell] 視窗中，設定一個變數，指定您在步驟 1 建立的儲存體帳戶名稱。

            $storageAccountName = "vmstestsa"

2.  設定指定儲存帳戶的主索引鍵的變數。

            $storageAccountKey = "<primary-account-key>"

    您可以取得此按鍵 Azure 入口網站中檢視儲存帳戶資源時，請按一下索引鍵的圖示。

3.  建立用來驗證儲存帳戶作業儲存帳戶內容的物件。

            $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

4.  建立儲存範本的容器。

            $containerName = "templates"
            New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob

5.  範本檔案上傳至新的容器。

            $blobName = "VMSSTemplate.json"
            $fileName = "C:\" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx

## <a name="step-5-deploy-the-template"></a>步驟 5︰ 部署範本

現在，您建立的範本，您就可以開始部署資源。 使用這個命令來啟動程序︰

    New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

當您按下輸入時，系統提示您以提供您指定的變數中的值。 提供以下值︰

    vmName: vmsstestvm1
      vmSSName: vmsstest1
      instanceCount: 5
      adminUserName: vmadmin1
      adminPassword: VMpass1
      resourcePrefix: vmsstest

它應該採取的成功部署的所有資源 15 分鐘。

>[AZURE.NOTE] 您也可以使用部署資源的入口網站的功能。 使用此連結: 「 https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>」

## <a name="step-6-monitor-resources"></a>步驟 6︰ 監視器資源

您可以取得有關使用這些方法的虛擬機器縮放比例設定的資訊︰

 - Azure 入口網站中的目前，就可以使用入口網站的資訊的一段有限。
 - [Azure 資源檔案總管](https://resources.azure.com/)中-這項工具最適合探索您的縮放設定的目前狀態。 請遵循此路徑，您應該會看到您所建立的縮放比例設定的執行個體檢視︰

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell-使用此命令，以取得一些資訊︰

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

        Or

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

 - 連線到另一個虛擬機器，就像您想要的任何其他電腦，然後您可以從遠端存取縮放比例設定為監視個別的程序中的虛擬機器。

>[AZURE.NOTE] 完成的 REST API，以取得縮放比例設定的相關資訊，請參閱[虛擬機器縮放設定](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>步驟 7︰ 移除資源

您會使用 Azure 中的資源，因為永遠是好的做法，若要刪除不再需要的資源。 您不需要的每個資源個別刪除資源群組。 您可以刪除資源群組和它的所有資源會自動被都刪除。

    Remove-AzureRmResourceGroup -Name vmsstestrg1

如果您想要保留您的資源群組，您可以刪除只設定小數位數。

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"

## <a name="next-steps"></a>後續步驟

- 管理您剛建立使用[管理集中虛擬機器小數位數的虛擬機器](virtual-machine-scale-sets-windows-manage.md)中的資訊的縮放設定。
- 深入瞭解檢閱[虛擬機器比例集垂直自動調整大小](virtual-machine-scale-sets-vertical-scale-reprovision.md)的垂直縮放比例
- 尋找監控功能[監視器 PowerShell 的 Azure 快速啟動範例](../monitoring-and-diagnostics/insights-powershell-samples.md)Azure 監視器的範例
- 深入瞭解通知功能中[使用自動調整大小動作來傳送電子郵件和 webhook Azure 監視器的提醒通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- 瞭解如何[使用稽核記錄以傳送電子郵件和 webhook Azure 監視器的提醒通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
