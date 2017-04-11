<properties
   pageTitle="Azure 記錄整合常見問題集 |Microsoft Azure"
   description="此常見問題集解答關於 Azure 記錄整合的問題。"
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Azure 記錄整合常見問題集 (faq)

此常見問題集解答關於 Azure 記錄整合，可讓您從 Azure 資源的原始記錄整合內部部署安全性資訊及事件管理 (SIEM) 系統的服務問題。 此整合提供整合儀表板的所有的資產，內部部署或雲端，以便您可以彙總、 相互關聯、 分析和相關聯的應用程式的安全性事件的提醒。

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>我可以看到儲存帳戶從 Azure 記錄整合抽取從 Azure VM 記錄？

執行 [ **azlog 來源清單**中的 [命令]。

## <a name="how-can-i-update-the-proxy-configuration"></a>我要如何更新 proxy 設定？

如果您的 proxy 設定不會直接允許 Azure 儲存裝置存取權，請開啟**AZLOG。執行檔。設定** **c:\Program Files\Microsoft Azure 記錄整合**中的檔案。 更新包含貴組織的 proxy 位址**defaultProxy**節的檔案。 更新完畢後，停止和開始使用命令**網路停駐點 azlog**和**網路開始 azlog**的服務。

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>我可以看到 Windows 事件的訂閱資訊？

新增來源時將**subscriptionid**附加好記的名稱。

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

事件 XML 有中繼資料，如下所示，包括訂閱識別碼。

![事件 XML][1]

## <a name="error-messages"></a>錯誤訊息

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>當正在執行命令**azlog createazureid**，為什麼收到下列錯誤？

錯誤︰

  *無法建立 AAD 應用程式-租用戶 72f988bf-86f1-41af-91ab-2d7cd011db37-原因 = '禁止 」 的訊息 = '足夠的權限以完成作業。 」*

在 [訂閱的 Azure 登入有權存取的所有 Azure AD 租用戶中建立服務主要嘗試**Azlog createazureid** 。 如果您 Azure 的登入只有該 Azure AD 租用戶中的來賓使用者，然後命令失敗，並 「 足夠的權限以完成作業。 」 要求將您的帳戶以租用戶中的使用者的租用戶管理員。

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>當正在執行命令**azlog 授權**，為什麼收到下列錯誤？

錯誤︰

  *警告建立角色指派-AuthorizationFailed︰ 在用戶端janedo@microsoft.com'使用物件識別碼 」 fe9e03e4-4dad-4328-910f-fd24a9660bd2 」 不需要執行動作 'Microsoft.Authorization/roleAssignments/write' 授權對範圍 ' / 訂閱/70 d 95299 d689 4 c 97-b971-0d8ff0000000 」。*

**Azlog 授權**] 命令會提供訂閱指派角色的閱讀程式本金 Azure AD 服務 （使用**Azlog createazureid**建立）。 如果 Azure 登入不是共同的系統管理員或訂閱的擁有者，則會使用 「 授權失敗 」 錯誤訊息。 Azure 角色型存取控制 (RBAC) 的共同管理員或擁有者才能完成此動作。

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>哪裡可以找到屬性的定義中稽核記錄？

請參閱︰

- [稽核作業與資源管理員](../resource-group-audit.md)
- [列出 Azure 監視器 REST API 訂閱中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>哪裡可以找到詳細資料 Azure 資訊安全中心的通知上？

請參閱[管理與回應在 Azure 資訊安全中心的安全性提醒](../security-center/security-center-managing-and-responding-alerts.md)。

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>如何修改內容會收集與 VM 診斷嗎？

如需詳細資訊，如何取得，修改]，請參閱[使用 PowerShell 啟用 Azure 虛擬機器執行 Windows 中的診斷](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)和 Windows *(WAD)*中設定 Azure 診斷設定。 以下是範例︰

### <a name="get-the-wad-config"></a>取得 WAD 設定

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>修改 WAD 設定

下列範例是設定位置收集僅 EventID 4624 及 EventId 4625 從安全性事件記錄檔。 Microsoft 反惡意程式碼的事件會收集從系統事件記錄檔。 請參閱 [使用事件] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) 的 XPath 運算式的詳細資訊。

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>設定 WAD 組態設定

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

之後進行變更，請檢查以確保正確的事件會收集的儲存空間帳戶。

如果您有 Azure 記錄整合的相關問題，請傳送電子郵件[AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
