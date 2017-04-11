<properties
    pageTitle="若要建立提醒 Azure 服務使用跨平台的命令列介面 (CLI) |Microsoft Azure"
    description="若要建立 Azure 通知，可以觸發通知或自動化符合您指定的條件時使用的命令列介面。"
    authors="rboucher"
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
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>使用跨平台的命令列介面 (CLI) 建立 Azure 服務的通知

> [AZURE.SELECTOR]
- [入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>概觀

本文將說明如何設定 Azure 通知使用的命令列介面 (CLI)。

>[AZURE.NOTE] Azure 監視器 2016 年 9 月 25，直到是稱為 「 Azure 深入資訊 」 的新名稱。 不過，命名空間，因此下列命令仍然包含 「 深入資訊]。

您會收到通知監控指標，或 Azure 服務上的事件。

- **度量值**-的度量單位，指定值交叉您指派以任意方向臨界值時的通知觸發程序。 就是它觸發兩時條件符合時第一次，然後之後時的條件不符合。    
- **活動記錄事件**的*每個*事件，或，只在特定數目的事件發生時，才可以觸發程序通知。

您可以設定提醒觸發時，請執行下列動作︰

- 傳送電子郵件通知給服務管理員和共同管理員
- 傳送電子郵件至您指定的其他電子郵件。
- 通話 webhook
- 開始執行 Azure runbook （僅從這次 Azure 入口網站）

您可以設定，並取得通知使用的規則的相關資訊

- [Azure 入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [命令列介面 (CLI)](insights-alerts-command-line-interface.md)
- [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


您永遠可以接收命令的說明輸入命令和放-協助結尾。 例如︰

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>建立使用 CLI 通知的規則

1. 執行的必要條件和登入 Azure。 請參閱[Azure 監視器 CLI 範例](insights-cli-samples.md)。 簡單來說，安裝 CLI，並執行這些命令。 他們協助您登入，顯示您使用的，並準備執行 Azure 監視器命令訂閱。


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  現有的規則，在 [資源群組] 清單中，使用下列表單**azure 通知規則清單的深入見解** *[選項] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. 若要建立規則，您需要先有幾項重要的資訊。
    - 您想要設定的提醒和資源**資源識別碼**
    - **公制定義**該資源可用的

    若要取得資源識別碼的其中一個方法是使用 Azure 入口網站。 已經建立假設資源，在入口網站中加以選取。 然後在下一個刀中，選取 [*內容*在 [*設定*] 區段底下。 *資源識別碼*是下一個刀的欄位。 另一個方法是使用[Azure 資源檔案總管](https://resources.azure.com/)]。

    範例資源識別碼 web 應用程式

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    若要取得這些指標前一個範例中，資源和清單之可用的度量單位，請使用下列 CLI 命令︰  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_是可用的度量單位 （每隔 1 分鐘） 的方式。 使用不同的資料粒度，讓您不同公制的選項。


4. 若要建立公制為基礎的通知規則，請使用下列格式的命令︰

    **azure 獲得深入見解通知規則公制設定***[選項] &lt;ruleName&gt; &lt;位置&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;運算子&gt;&lt;閥值&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    下列範例會設定網站資源的警示。 通知的觸發程序時持續收到任何流量 5 分鐘與一次時收到任何流量 5 分鐘。

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. 若要建立 webhook，或當提醒啟動時，傳送電子郵件，請先建立電子郵件及/或 webhooks。 然後立即建立規則之後。 您無法建立關聯 webhook 或以電子郵件建立規則使用 CLI。

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. 若要建立提醒的活動記錄中的特定條件，請使用表單︰

    **獲得深入見解通知規則記錄設定***[選項] &lt;ruleName&gt; &lt;位置&gt; &lt;resourceGroup&gt; &lt;operationName&gt; *

    例如

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    OperationName 會對應到事件類型的活動記錄中的項目。 範例包括*Microsoft.Compute/virtualMachines/delete*和*microsoft.insights/diagnosticSettings/write*。

    您可以使用 PowerShell 命令[取得 AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx)以取得可能 operationNames 的清單。 或者，您可以使用 [Azure 入口網站查詢活動記錄，並尋找特定過您想要建立提醒的作業。 顯示圖形的記錄檔的檢視中的好記的名稱作業。 查看項目的 JSON，並拉出 OperationName 值。   

7. 您可以驗證提醒有已正確建立查看個別的規則。

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. 若要刪除規則，請使用表單的命令︰

    **獲得深入見解通知規則刪除**[選項]&lt;resourceGroup&gt; &lt;ruleName&gt;

    這些命令刪除本文中先前建立的規則。

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>後續步驟

* 您可以[概略 Azure 監控](monitoring-overview.md)包括類型的資訊收集並監控。
* 深入瞭解[設定 webhooks 中通知](insights-webhooks-alerts.md)。
* 進一步瞭解[Azure 自動化 Runbooks](..\automation\automation-starting-a-runbook.md)。
* 前往[概觀收集診斷記錄](monitoring-overview-of-diagnostic-logs.md)您的服務收集詳細的頻率高的度量。
* 取得[指標集合的概觀](insights-how-to-customize-monitoring.md)，請確定您的服務是可用和回應。
