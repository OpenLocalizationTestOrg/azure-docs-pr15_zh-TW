<properties
    pageTitle="若要建立提醒 Azure 服務使用 Azure 入口網站 |Microsoft Azure"
    description="若要建立 Azure 通知，可以觸發通知或自動化符合您指定的條件時使用 Azure 入口網站。"
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
    ms.date="09/23/2016"
    ms.author="robb"/>

# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>若要建立提醒 Azure 服務使用 Azure 入口網站

> [AZURE.SELECTOR]
- [入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>概觀

本文將說明如何設定 Azure 使用 Azure 中心的通知。   

您會收到通知監控指標，或 Azure 服務上的事件。

- **度量值**-的度量單位，指定值交叉您指派以任意方向臨界值時的通知觸發程序。 就是它觸發兩時條件符合時第一次，然後之後時的條件不符合。    
- **活動記錄事件**的*每個*事件，或，只在特定數目的事件發生時，才可以觸發程序通知。


您可以設定提醒觸發時，請執行下列動作︰

- 傳送電子郵件通知給服務管理員和共同管理員
- 傳送電子郵件至您指定的其他電子郵件。
- 通話 webhook
- 開始執行 Azure runbook （僅從 Azure] 入口網站）

您可以設定，並取得通知使用的規則的相關資訊

- [Azure 入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [命令列介面 (CLI)](insights-alerts-command-line-interface.md)
- [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>在 [度量單位，Azure 入口網站上建立提醒規則

1. 在[入口網站](https://portal.azure.com/)中，找出的資源您感興趣監視並加以選取。

2. 監控區段底下，選取 [**通知**或**提醒的規則**]。 文字和圖示會有些許的不同的資源。  

    ![監控](./media/insights-alerts-portal/AlertRulesButton.png)


3. 選取 [**新增提醒**] 命令，然後填入欄位。

    ![新增提醒](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **名稱**警示規則，然後選擇 [**描述**，其中也會顯示通知電子郵件中。
5. 選取您想要監視，然後選擇 [公制**條件**並**臨界**值的**度量**。 也選擇公制規則須滿足如下的通知的觸發程序之前的時間**週期**。 因此，例如，如果您使用的期間 」 PT5M 」 80 %cpu 尋找您的提醒，提醒會觸發 CPU 已經一致上述 80 %5 分鐘。 一旦發生的第一個觸發程序，再次觸發時 CPU 保持下方 80 %5 分鐘。 [CPU 度量就會發生每 1 分鐘。   

6. 如果您想要的系統管理員和共同管理員會透過電子郵件傳送的提醒，則會啟動時，請核取**...電子郵件擁有者**。

7. 如果您想要收到通知提醒或其他電子郵件，請將其新增**額外的管理員 email(s)** ] 欄位中。 以分號區隔-分隔多個電子郵件*email@contoso.com;email2@contoso.com*

8. 如果您希望提醒會啟動時，將有效的 URI **Webhook**欄位中。

9. 如果您使用 Azure 自動化，您可以選取 Runbook 提醒，則會啟動時執行。

10. 選取**[確定]**完成建立提醒。   

在幾分鐘，提醒已啟動，並為先前所述的觸發程序。

## <a name="managing-your-alerts"></a>管理您的通知

當您建立通知之後時，您可以選取和︰

- 檢視圖表顯示單位臨界值和前一天的實際值。
- 編輯或刪除。
- **停用**或**啟用**，如果您想要暫時停止或繼續收到通知，該警示。



## <a name="next-steps"></a>後續步驟

* 您可以[概略 Azure 監控](monitoring-overview.md)包括類型的資訊收集並監控。
* 深入瞭解[設定 webhooks 中通知](insights-webhooks-alerts.md)。
* 進一步瞭解[Azure 自動化 Runbooks](..\automation\automation-starting-a-runbook.md)。
* [診斷記錄的概觀](monitoring-overview-of-diagnostic-logs.md)，收集詳細的頻率高的度量您的服務。
* 取得[指標集合的概觀](insights-how-to-customize-monitoring.md)，請確定您的服務是可用和回應。
