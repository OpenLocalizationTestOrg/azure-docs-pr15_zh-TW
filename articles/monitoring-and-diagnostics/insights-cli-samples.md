<properties
    pageTitle="Azure 監視器 CLI 快速入門範例。 |Microsoft Azure"
    description="Azure 監視器功能的範例 CLI 命令。 Azure 監視器是 Microsoft Azure 服務可讓您傳送提醒通知，請撥設定的遙測資料，並自動調整大小雲端服務、 虛擬機器和 Web 應用程式的數值為根據的 web Url。"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Azure 監視器跨平台 CLI 快速入門範例

本文將示範範例命令列介面 (CLI) 命令可協助您存取 Azure 監視器功能。 Azure 監視器可讓您自動調整大小雲端服務，虛擬機器 Web 應用程式並傳送提醒通知或通話期間，設定的遙測資料的數值為根據的 web Url。

>[AZURE.NOTE] Azure 監視器 2016 年 9 月 25，直到是稱為 「 Azure 深入資訊 」 的新名稱。 不過，命名空間，因此下列命令仍然包含 「 深入資訊]。


## <a name="prerequisites"></a>必要條件

如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](../xplat-cli-install.md)。 如果您不熟悉 Azure CLI，您可以閱讀更多關於在[使用 Azure CLI for Mac、 Linux 和 Windows Azure 資源管理員使用的商務連絡人](../xplat-cli-azure-resource-manager.md)。


在 Windows 中，請從[Node.js 網站](https://nodejs.org/)安裝 npm。 完成安裝作業，為系統管理員身分執行] 權限使用 CMD.exe 之後，安裝 npm 所在的資料夾中執行下列命令︰

```console
npm install azure-cli --global
```

接下來，請瀏覽至您想要然後在命令列輸入任何資料夾/位置︰

```console
azure help
```

## <a name="log-in-to-azure"></a>登入 Azure

第一個步驟是 Azure 帳戶登入。

```console
azure login
```

執行此命令之後，您必須登入，透過在螢幕上的指示進行。 之後，您看到您的帳戶、 TenantId 及預設訂閱識別碼。 所有的命令，在您預設的訂閱的內容中。

若要列出目前訂閱的詳細資訊，請使用下列命令。

```console
azure account show
```

若要變更工作內容至不同的訂閱，請使用下列命令。

```console
azure account set "subscription ID or subscription name"
```

若要使用 Azure 資源管理員和 Azure 監視器的命令，您必須在 Azure 資源管理員模式。

```console
azure config mode arm
```

若要檢視所有支援的 Azure 監視器命令的清單，請執行下列動作。

```console
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>檢視稽核記錄檔的訂閱

若要檢視稽核記錄的清單，請執行下列動作。

```console
azure insights logs list [options]
```

請嘗試下列動作以檢視所有可用的選項。

```console
azure insights logs list -help
```

以下是由 resourceGroup 清單記錄檔範例

```console
azure insights logs list --resourceGroup "myrg1"
```

範例呼叫者的清單記錄

```console
azure insights logs list --caller "myname@company.com"
```

範例在資源類型] 中的開始和結束日期呼叫者的清單記錄

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>使用通知
您可以使用資訊] 區段中，使用 [通知]。

### <a name="get-alert-rules-in-a-resource-group"></a>在 [資源群組取得通知的規則

```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>建立公制通知規則

```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>建立記錄檔通知規則

```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>建立 webtest 通知規則

```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>刪除警示規則

```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>記錄設定檔
使用記錄設定檔中使用此區段中的資訊。

### <a name="get-a-log-profile"></a>取得記錄檔

```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>新增不保留記錄檔

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>移除記錄檔

```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>新增保留記錄設定檔

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>新增保留與 EventHub 記錄檔

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>診斷程式
使用 [診斷設定中使用此區段中的資訊。

### <a name="get-a-diagnostic-setting"></a>取得診斷設定

```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>停用診斷設定

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>啟用診斷設定不保留

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>自動調整大小
使用此區段中的資訊，使用 [自動調整大小的設定。 您需要修改這些範例。

### <a name="get-autoscale-settings-for-a-resource-group"></a>取得資源群組自動縮放設定

```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>資源群組中，依名稱取得自動縮放設定

```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>設定 auotoscale 設定

```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
