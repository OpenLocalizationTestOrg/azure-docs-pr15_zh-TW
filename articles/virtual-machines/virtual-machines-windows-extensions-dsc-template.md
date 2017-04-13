<properties
   pageTitle="想要狀態設定資源管理員範本 |Microsoft Azure"
   description="資源管理員範本定義 Azure 使用範例和疑難排解所需的狀態設定"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Windows VMSS 和 Azure 資源管理員範本所需的狀態設定
本文說明[所需的狀態設定副檔名處理常式](virtual-machines-windows-extensions-dsc-overview.md)資源管理員範本。 

## <a name="template-example-for-a-windows-vm"></a>Windows vm 範本範例

下列程式碼片段會移至 [資源] 區段的範本。

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Windows VMSS 範本範例

VMSS 節點具有的 [內容] 區段，包含 「 VirtualMachineProfile 「，」 extensionProfile 」 屬性。 DSC 會新增至 「 延伸模組 」。 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
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

## <a name="detailed-settings-information"></a>設定的詳細的資訊

下列結構描述適用於 Azure DSC 副檔名 Azure 資源管理員範本中的設定部分。

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>詳細資料
| 屬性名稱 | 類型 | 描述 |
| --- | --- | --- |
| settings.wmfVersion | 字串 | 指定應該安裝在您 VM Windows Management Framework 版本。 將此屬性設定為 「 最新的 「 安裝最新版的 WMF。 只有目前的可能值，這個屬性會**'4.0 」、 「 5.0 「，」 5.0PP 」 和 「 最新的 「**。 這些可能的值是主旨更新。 預設值為 「 最新]。|
| settings.configuration.url | 字串 | 指定要下載 DSC 設定 zip 檔案的 URL 位置。 如果所提供的 URL 存取需要 SA 權杖，必須先將 SA token 值設定 protectedSettings.configurationUrlSasToken 屬性。 如果定義 settings.configuration.script 及/或 settings.configuration.function 需要此屬性。 |
| settings.configuration.script | 字串 | 指定包含 DSC 設定的定義的指令碼的檔案名稱。 這個指令碼必須是從 configuration.url 屬性所指定的 URL 下載 zip 檔案的根資料夾中。 如果定義 settings.configuration.url 及/或 settings.configuration.script 需要此屬性。 |
| settings.configuration.function | 字串 | 指定 DSC 設定的名稱。 名為 「 設定必須包含在 configuration.script 所定義的指令碼。 如果定義 settings.configuration.url 及/或 settings.configuration.function 需要此屬性。 |
| settings.configurationArguments | 集合 | 定義您想要將您的 DSC 設定任何參數。 此屬性不會加密。 |
| settings.configurationData.url | 字串 | 指定要下載設定資料 (.pds1) 檔案做為您 DSC 設定輸入的 URL。 如果所提供的 URL 存取需要 SA 權杖，必須先將 SA token 值設定 protectedSettings.configurationDataUrlSasToken 屬性。|
| settings.privacy.dataEnabled | 字串 | 啟用或停用遙測集合。 此屬性的值只可以是**'啟用'，「 停用' '，或 $null**。 離開此屬性，空白或 null，可讓遙測。 預設值是。 [詳細資訊](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | 集合 | 定義要下載 WMF 替代的位置。 [詳細資訊](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | 集合 | 定義您想要將您的 DSC 設定任何參數。 此屬性已加密。 |
| protectedSettings.configurationUrlSasToken | 字串 | 指定 SA 權杖，來存取 configuration.url 定義的 URL。 此屬性已加密。 |
| protectedSettings.configurationDataUrlSasToken | 字串 | 指定 SA 權杖，來存取 configurationData.url 定義的 URL。 此屬性已加密。 |

## <a name="settings-vs-protectedsettings"></a>設定與 ProtectedSettings
所有設定會都儲存在 VM 設定文字檔案。
在 [設定] 下的內容是公用屬性，因為他們未加密中設定文字檔案。
「 ProtectedSettings 」 下的屬性加密的憑證，並不會顯示在 VM 此檔案中的 [純文字。

如果設定需要認證，他們可以包含在 protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>範例

下列範例是衍生自[DSC 副檔名處理常式概觀] 頁面](virtual-machines-windows-extensions-dsc-overview.md)的 「 開始 」 區段。
此範例使用部署副檔名，而不是 cmdlet 資源管理員範本。 儲存 「 IisInstall.ps1 」 設定，放在。ZIP 檔案，然後上傳的檔案中可存取的 URL。 此範例使用 Azure blob 儲存體，但可以下載。從任何任意位置的 ZIP 檔案。

在 [Azure 資源管理員範本，下列程式碼會指示 VM 下載正確的檔案，並執行適當的 PowerShell 函數︰

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>更新先前的格式
在舊版格式 （包含 ModulesUrl、 ConfigurationFunction、 SasToken 或內容的公用屬性） 自動調整為目前的格式，並執行往常一樣。

下列結構描述是什麼上述設定結構描述看起來像︰

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

以下是如何將舊版格式調整為目前的格式︰

| 屬性名稱 | 上一個結構描述相等值 |
| --- | --- |
| settings.wmfVersion | 設定。WMFVersion |
| settings.configuration.url | 設定。ModulesUrl |
| settings.configuration.script | 第一組件中的設定。ConfigurationFunction (之前 '\\\\」) |
| settings.configuration.function | 第二部分設定的詳細資訊。ConfigurationFunction (之後 '\\\\」) |
| settings.configurationArguments | 設定。屬性 |
| settings.configurationData.url | protectedSettings.DataBlobUri （不含 SA 權杖） |
| settings.privacy.dataEnabled | 設定。Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | 設定。AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | 設定。SasToken |
| protectedSettings.configurationDataUrlSasToken | 從 protectedSettings.DataBlobUri SA 權杖 |


## <a name="troubleshooting---error-code-1100"></a>疑難排解-1100年，出現錯誤碼
錯誤碼 1100年表示 DSC 副檔名為使用者輸入問題。
這些錯誤的文字並變數，因此可能會變更。
以下是一些您可能會遇到的錯誤和修正問題。

### <a name="invalid-values"></a>不正確的值
「 Privacy.dataCollection 都會 '{0}'。 僅限可能的值、 '啟用 」 及 「 停用 「 「 「 WmfVersion 都會 '{0}'。 可能的值是... 和 「 最新的 「 」

問題︰ 不允許提供的值。

解決方案︰ 變更為有效的值不正確的值。 請參閱 [詳細資料] 區段中的資料表。

### <a name="invalid-url"></a>無效的 URL
「 ConfigurationData.url 都會 '{0}'。 這不是有效的 URL 「 「 DataBlobUri 都會 '{0}'。 這不是有效的 URL 「 「 Configuration.url 都會 '{0}'。 這不是有效的 URL 」

問題︰ A 提供 URL 不正確。

解決方案︰ 檢查您的所有提供的 Url。 請確認所有 Url 都解決有效的位置且副檔名為可以存取遠端電腦上。

### <a name="invalid-configurationargument-type"></a>無效的 ConfigurationArgument 類型
[輸入無效 configurationArguments {0}]

問題︰ ConfigurationArguments 屬性無法解析 Hashtable 物件。 

解決方案︰ 請您 ConfigurationArguments 屬性 Hashtable。 請遵循先前範例中所提供的格式。 留意報價、 逗號及括號。

### <a name="duplicate-configurationarguments"></a>重複 ConfigurationArguments
「 找重複的引數 '{0}' 到公開和受保護的 configurationArguments 中 」

問題︰ 公用設定中的 ConfigurationArguments 及受保護的設定] 中 ConfigurationArguments 包含具有相同名稱的內容。

解決方案︰ 移除重複的屬性。

### <a name="missing-properties"></a>遺漏屬性
「 Configuration.function 需要 configuration.url configuration.module 指定或 」

「 Configuration.url 需要該 configuration.script 指定 」

「 Configuration.script 需要該 configuration.url 指定 」

「 Configuration.url 需要該 configuration.function 指定 」

「 ConfigurationUrlSasToken 需要該 configuration.url 指定 」

「 ConfigurationDataUrlSasToken 需要該 configurationData.url 指定 」

問題︰ 已定義的屬性必須在另一遺漏的屬性。

解決方案︰ 
- 提供遺失的屬性。
- 移除需要遺失屬性的屬性。


## <a name="next-steps"></a>後續步驟
深入了解 DSC 與虛擬機器縮放比例設定以[使用 DSC 副檔名為 Azure 虛擬機器縮放設定](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

[DSC 的安全認證管理](virtual-machines-windows-extensions-dsc-credentials.md)上尋找更多詳細資料。 

如需有關 Azure DSC 副檔名處理常式的詳細資訊，請參閱[Azure 所需的狀態設定副檔名處理常式的簡介](virtual-machines-windows-extensions-dsc-overview.md)。 

如需有關 PowerShell DSC，[請造訪 PowerShell 文件中心](https://msdn.microsoft.com/powershell/dsc/overview)的詳細資訊。 
