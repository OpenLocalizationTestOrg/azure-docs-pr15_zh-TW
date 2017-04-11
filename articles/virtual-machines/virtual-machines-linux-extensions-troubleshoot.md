<properties
   pageTitle="疑難排解 Linux VM 副檔名失敗 |Microsoft Azure"
   description="深入了解 Azure Linux VM 副檔名失敗的疑難排解"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Azure Linux VM 副檔名失敗的疑難排解

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>檢視副檔名狀態
從 Azure CLI 執行 azure 資源管理員範本。 後執行的範本，您可以從 Azure 資源總管或命令列工具檢視副檔名狀態。

以下是範例︰

Azure CLI:

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>疑難排解 Extenson 失敗︰

### <a name="re-running-the-extension-on-the-vm"></a>再次執行 VM 上的 [副檔名

如果您正在執行指令碼，VM 上使用自訂指令碼副檔名，您有時遇到 VM 已成功建立，但無法指令碼錯誤。 在這些 conditons，修復此錯誤的建議的方式是移除副檔名為，然後再重新執行範本。
附註︰ 在未來，這項功能會增強移除需要解除安裝副檔名。

#### <a name="remove-the-extension-from-azure-cli"></a>從 Azure CLI 移除副檔名

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

其中 「 publsher 名稱 「 對應於副檔名類型從 「 azure vm 取得-執行個體-檢視 」 的輸出，而名稱是從範本副檔名資源的名稱

後擴充功能已經移除，則可以重新執行 VM 上執行指令碼的範本。
