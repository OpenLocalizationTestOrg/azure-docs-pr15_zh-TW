<properties
   pageTitle="事件的回應使用 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件會說明如何使用 Azure 資訊安全中心應計案例。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>使用 Azure 資訊安全中心事件的回應
許多組織瞭解如何將只在遭受攻擊之後回應安全性事件。 若要降低成本及產生的損害，請務必有計畫進行攻擊之前事件回應。 您可以使用 Azure 資訊安全中心中不同階段的事件的回應。

## <a name="incident-response-planning"></a>事件回應規劃

有效的方案而定三個核心功能︰ 能夠保護，偵測到，和回應威脅。 保護是關於防止事件、 偵測的最早，識別威脅並回應是收回攻擊及還原系統降低破壞的影響。

本文會使用從[Microsoft Azure 安全性回應在雲端](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678)的文件，安全性應計階段，如下圖所示︰

![事件回應生命週期](./media/security-center-incident-response/security-center-incident-response-fig1.png)

您可以使用資訊安全中心偵測、 評估和診斷階段。 以下是如何資訊安全中心會有幫助的三個初始應計階段的範例︰

- **偵測**︰ 檢閱第一個事件法律調查證據的指示。
    - 範例︰ 檢閱，高優先順序安全性警示中的資訊安全中心儀表板，初始驗證。
- **評估**︰ 執行初始評估取得更多有關可疑的活動。
    - 範例︰ 取得詳細資訊的安全性提醒中。
- **診斷**︰ 進行技術調查並找出內含項目、 降低及因應措施策略。
    - 範例︰ 依照補救資訊安全中心的特定的安全性提醒中所述。

遵循此案例將示範如何運用資訊安全中心安全性事件的偵測、 評估和診斷/回覆階段。 [安全性事件](security-center-incident.md)資訊安全中心是彙總的資源對齊[刪除鏈](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/)圖樣的所有通知。 事件會出現在[的安全性警告](security-center-managing-and-responding-alerts.md)] 方塊與。 事件會顯示相關通知，可讓您取得更多有關每個項目清單。 資訊安全中心也會顯示獨立安全性警訊，也可以用來追蹤可疑的活動。

## <a name="scenario"></a>案例

Contoso 最近移轉其內部部署資源的一些 Azure，包括一些虛擬機器為基礎的業務負載與 SQL 資料庫。 目前，Contoso 核心電腦安全性事件回應小組 (CSIRT) 有問題，因為其目前的應計工具不整合的安全性智慧調查安全性問題。 整合缺乏的階段偵測 （太多誤判），以及評估和診斷階段期間，也會發生問題。 屬於此移轉，他們會決定要採用資訊安全中心，以協助他們解決這個問題的。

第一個階段的這個移轉完成後 onboarded 所有資源和收件者的所有資訊安全中心安全性建議。 Contoso CSIRT 是用來處理電腦安全性事件焦點。 小組所組成的一組人員負責安全性事件。 小組成員有明確定義之責任，才能確保沒有回應的區域會發現。

以這個案例中，我們將焦點上的下列角色屬於 Contoso CSIRT 的角色︰

![事件回應生命週期](./media/security-center-incident-response/security-center-incident-response-fig2.png)

劉瓊位於安全性作業。 她責任包括︰

- 監控和回應安全性威脅繞著時鐘。
- 提升雲端工作負載的擁有者或安全性分析師視。

Sam 安全性分析師並他責任包括︰

- 調查攻擊。
- Idfix 修正通知。
- 使用工作負載擁有者，若要決定並套用降低。

如您所見，有劉瓊和 Sam 不同職責，而且必須共同作業共用資訊安全中心 」 的資訊。

## <a name="recommended-solution"></a>建議的解決方案

由於劉瓊與 Sam 有不同的角色，他們會取得日常活動的相關資訊使用資訊安全中心的不同區域。 劉瓊會使用**的安全性警告**，做為其每日監視的一部分。

![安全性警告](./media/security-center-incident-response/security-center-incident-response-fig3.png)

劉瓊會偵測與評估階段期間使用的安全性警告。 劉瓊完成初始評估後，她可能會升級 Sam 問題，如果是必要的額外的調查。 此時，Sam 會使用所提供的資訊安全中心，有時候搭配其他資料來源，移至 [診斷階段的資訊。


## <a name="how-to-implement-this-solution"></a>如何執行此解決方案

若要查看如何使用 Azure 資訊安全中心應計案例中，我們將會在偵測與評估階段，請遵循劉瓊的步驟，然後查看 [Sam 功能來診斷問題。

### <a name="detect-and-assess-incident-response-stages"></a>偵測並評估應計階段

劉瓊 Azure 入口網站登入，並在資訊安全中心主控台中運作。 監控活動她每天的一部分，她開始檢閱高優先順序安全性警訊藉由執行下列步驟︰

1. 按一下 [**安全性警告**] 方塊，並存取**安全性警訊**刀。
    ![安全性警訊刀](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] 以這個案例中，有劉瓊會進行評估惡意 SQL 活動提醒中，在前圖所示。
2. 按一下**惡意 SQL 活動**通知，並檢閱**惡意 SQL 活動**刀中受攻擊的資源︰ ![事件詳細資料](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    在此刀，有劉瓊可能需要備忘稿有關受攻擊的資源，如何多次攻擊發生，而且它已偵測到時。
3. 按一下 [**攻擊資源**以取得詳細資訊，此攻擊的相關資訊。

閱讀描述之後, 會將有劉瓊相信這不是誤判和她應該升級 Sam 這種情況。

### <a name="diagnose-incident-response-stage"></a>診斷應計階段

Sam 接收劉瓊的大小寫，並開始檢閱資訊安全中心建議補救步驟。

![事件回應生命週期](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>其他資源

事件回應小組也可以利用[安全性中心 Power BI](security-center-powerbi.md)能夠看到不同類型的報表。 這些報表可協助他們期間進一步調查以視覺化方式呈現、 分析和篩選建議和安全性警訊。 調查程序期間使用其安全性資訊和事件管理 (SIEM) 解決方案的公司，他們也可以[整合其解決方案資訊安全中心](security-center-integrating-alerts-with-log-integration.md)。 您也可以使用[Azure 記錄整合工具](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/)整合 Azure 稽核記錄檔和虛擬機器 (VM) 安全性事件。 若要調查攻擊，您可以使用這項資訊搭配資訊安全中心提供的資訊。


## <a name="conclusion"></a>結束時

事件發生貴組織很重要，並將正面影響事件的處理方式之前，請召集小組。 監控資源適用的工具也有助於此小組採取正確的步驟來修復安全性事件。 資訊安全中心[偵測功能](security-center-detection-capabilities.md)可協助快速回應安全性事件，並修復安全性問題的 IT。
