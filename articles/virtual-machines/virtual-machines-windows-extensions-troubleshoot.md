<properties
   pageTitle="疑難排解 Windows VM 副檔名失敗 |Microsoft Azure"
   description="深入了解 Azure Windows VM 副檔名失敗的疑難排解"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 副檔名失敗的疑難排解

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>檢視副檔名狀態
從 Azure PowerShell 執行 azure 資源管理員範本。 後執行的範本，您可以從 Azure 資源總管或命令列工具檢視副檔名狀態。

以下是範例︰

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

以下是輸出範例︰

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>疑難排解副檔名失敗

### <a name="re-running-the-extension-on-the-vm"></a>再次執行 VM 上的 [副檔名

如果您正在執行指令碼，VM 上使用自訂指令碼副檔名，您有時候遇到 VM 已成功建立，但無法指令碼錯誤。 在這些 conditons，修復此錯誤的建議的方式是移除副檔名為，然後再重新執行範本。
附註︰ 在未來，這項功能會增強移除需要解除安裝副檔名。


#### <a name="remove-the-extension-from-azure-powershell"></a>移除 PowerShell 的 Azure 副檔名

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

後擴充功能已經移除，則可以重新執行 VM 上執行指令碼的範本。
