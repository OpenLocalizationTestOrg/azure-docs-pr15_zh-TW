<properties
   pageTitle="使用所需的虛擬機器縮放比例設定的狀態設定 |Microsoft Azure"
   description="使用虛擬機器色階設定 Azure DSC 副檔名"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>使用虛擬機器色階設定 Azure DSC 副檔名

可利用[Azure 所需的狀態設定 (DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md)副檔名處理常式使用[虛擬機器縮放比例設定 (VMSS)](virtual-machine-scale-sets-overview.md) 。 VMSS 提供部署及管理大量的虛擬機器的方式，並可以 elastically 不按比例縮放和縮小載入的回應。 DSC 用來將 Vm 設定為其上線，讓他們執行的實際執行軟體。

## <a name="differences-between-deploying-to-vm-and-vmss"></a>部署 VM 和 VMSS 之間的差異

VMSS 的基礎範本結構與單一 VM 稍有不同。 具體來說，單一 VM 部署 「 virtualMachines 」 節點下的副檔名。 有 「 延伸模組 」 類型的項目 DSC 新增至範本的位置

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

VMSS 節點具有的 [內容] 區段，包含 「 VirtualMachineProfile 「，」 extensionProfile 」 屬性。 DSC 隨即新增至 「 延伸模組 」

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>VMSS 的行為

VMSS 的行為是單一 vm 的行為相同。 建立新的 VM 時，它會自動提供給 DSC 副檔名。 如果是較新版的 WMF 所需的分機號碼，VM 重新啟動之前先上線。 後，在線上，它會下載 DSC 設定.zip，並提供其 VM 上。 [Azure DSC 副檔名概觀](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md)中可以找到更多詳細資料。

## <a name="next-steps"></a>後續步驟 ##
檢查[DSC 副檔名 Azure 資源管理員範本](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md)。

瞭解如何[安全地 DSC 副檔名控點的認證](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md)。 

如需有關 Azure DSC 副檔名處理常式的詳細資訊，請參閱[Azure 所需的狀態設定副檔名處理常式的簡介](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md)。 

如需有關 PowerShell DSC，[請造訪 PowerShell 文件中心](https://msdn.microsoft.com/powershell/dsc/overview)的詳細資訊。 


