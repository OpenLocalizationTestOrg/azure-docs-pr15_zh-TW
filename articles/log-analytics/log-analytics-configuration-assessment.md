<properties
    pageTitle="設定評估解決方案中記錄分析 |Microsoft Azure"
    description="在記錄檔分析組態評估方案可讓您與您的系統管理中心 Operations Manager server 基礎結構的目前狀態的詳細資訊時使用 Operations Manager 代理程式或 Operations Manager 管理群組。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="configuration-assessment-solution-in-log-analytics"></a>設定評估解決方案中記錄狀況分析

在記錄檔分析組態評估方案可協助您尋找透過通知和知識庫建議潛在的伺服器設定問題。

本方案需要系統管理中心 Operations Manager。 設定評估無法如果您只使用直接連線代理程式。

您的瀏覽器檢視中設定評估解決方案的部分資訊需要 Silverlight 外掛程式。

>[AZURE.NOTE] 2016 年 7 月 5 日，設定評估方案不再可加入記錄分析工作區，此方案將不再可供現有使用者 2016 年 8 月 1 日之後。 適用於 SQL Server 或 Active Directory 中使用此方案的客戶，我們建議您改為使用[SQL Server 評估](log-analytics-sql-assessment.md)、 [Active Directory 評估](log-analytics-ad-assessment.md)和[Active Directory 複寫狀態](log-analytics-ad-replication-status.md)的解決方案。 使用設定評估，在 Windows 版的客戶 HYPER-V，與系統管理中心的虛擬機器管理員中，我們建議您使用的事件集合，變更追蹤取得完整檢視的任何問題，您的環境中的功能。

![設定 [評估] 方塊](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

設定資料收集監控伺服器，然後傳送至雲端的 OMS 服務進行處理。 邏輯會套用至收到的資料和雲端服務記錄的資料。 以下區域顯示之伺服器的處理的資料︰

- **通知︰**顯示監控伺服器發生組態相關、 積極通知。 這些所產生的 Microsoft 客戶及最佳作法，從欄位的支援組織 (CSS) 所設計的規則。
- **知識庫建議︰**顯示所建議的基礎結構; 中找到的工作負載的 Microsoft 知識庫文章這些會自動看到建議根據您的電腦學習 [透過使用的設定。
- **伺服器和分析負載︰**顯示伺服器與 OMS 監督的工作量。

![設定評估儀表板](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### <a name="technologies-you-can-analyze-with-configuration-assessment"></a>您可以分析組態評估與技術

OMS 組態評估分析下列負載︰

- Windows Server 2012 與 Microsoft HYPER-V Server 2012
- Windows Server 2008 及 Windows Server 2008 R2，包括︰
    - Active Directory
    - HYPER-V 主機
    - 一般的作業系統
- SQL Server 2008 及更新版本
    - SQL Server 資料庫引擎
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 與 Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 與 Lync Server 2010
- 系統管理中心 2012 SP1 虛擬機器管理員

SQL Server 支援下列 32 位元與 64 位元版本的分析︰

- SQL Server 2016 的所有版本
- SQL Server 2014-所有版本
- SQL Server 2008 和 2008 R2-所有版本

SQL Server 資料庫引擎分析上所有支援的版本。 此外，在 WOW64 實作中執行時，會支援 32 位元版本的 SQL Server。

## <a name="installing-and-configuring-the-solution"></a>安裝及設定解決方案
安裝和設定方案，請使用下列資訊。

- Operations Manager，才能設定評估解決方案。
- 您必須在每個要評估其設定的電腦上 Operations Manager 代理程式。
- 新增到使用中[新增記錄分析解決方案從方案庫](log-analytics-add-solutions.md)所述的程序 OMS 工作區設定評估解決方案。  有不需要進行其他設定。

## <a name="configuration-assessment-data-collection-details"></a>設定評估資料集合詳細資料

設定評估收集設定資料、 中繼資料，並使用您已啟用代理程式的 [狀態] 資料。

下表顯示資料集合方法和其他詳細資料收集為設定評估的方式。

| 平台 | 直接代理程式 | 是 SCOM 代理程式 | Azure 儲存體 | 必要時，是 SCOM 嗎？ | 透過管理群組傳送是 SCOM 代理程式的資料 | 集合頻率 |
|---|---|---|---|---|---|---|
|Windows|![無](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![[是]](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![無](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|            ![[是]](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![[是]](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| 按兩次每日|

下表顯示藉由設定評估收集的資料類型的範例︰

|**資料類型**|**欄位**|
|---|---|
|設定|[客戶編號、 AgentID、 EntityID、 ManagedTypeID、 ManagedTypePropertyID、 CurrentValue，ChangeDate|
|中繼資料|BaseManagedEntityId、 ObjectStatus、 OrganizationalUnit、 ActiveDirectoryObjectSid、 PhysicalProcessors、 NetworkName、 IPAddress、 ForestDNSName、 NetbiosComputerName、 VirtualMachineName、 LastInventoryDate、 HostServerNameIsVirtualMachine、 IP 位址、 NetbiosDomainName、 LogicalProcessors、 DNSName、 顯示名稱、 DomainDnsName、 ActiveDirectorySite、 PrincipalName、 OffsetInMinuteFromGreenwichTime|
|狀態|StateChangeEventId、 StateId、 NewHealthState、 OldHealthState、 內容、 TimeGenerated、 TimeAdded、 StateId2、 BaseManagedEntityId、 MonitorId、 HealthState、 上次修改、 LastGreenAlertGenerated、 DatabaseTimeModified|

## <a name="configuration-assessment-alerts"></a>設定評估通知
您可以檢視及管理通知設定評估 [提醒] 頁面。 提醒會告訴您偵測到的問題，原因，以及如何解決問題。 他們也會提供設定的設定，您可能會導致效能問題的環境中的相關資訊。

![通知檢視](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] 從其他通知中記錄分析組態評估通知有所不同。 在瀏覽器檢視通知需要 Silverlight 外掛程式。

當您選取的項目或類別的 [提醒] 頁面上的項目時，您會看到伺服器或套用至每個項目通知的工作負載的清單。

![提醒的清單](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

每個通知會包含的 Microsoft 知識庫文章的連結。 這些文章提供提醒的其他資訊。

>[AZURE.TIP] 根據預設，顯示通知數目上限是 2000。 若要檢視更多的提醒，請按一下 [提醒的清單上方的 [通知] 列。

您可以按一下清單檢視也許可以協助您解決的問題產生提醒的原因的知識庫文件中的任何項目。

![知識庫文章](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

您可以管理提醒的規則，忽略特定規則的類別。

![管理提醒的規則](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## <a name="knowledge-recommendations"></a>知識庫建議
當您檢視知識庫建議時，就被顯示記錄搜尋結果列出建議的工作量及提供其他相關提醒的電腦的 Microsoft 知識庫文件。

![知識庫建議的搜尋結果](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## <a name="servers-and-workloads-analyzed"></a>伺服器和分析的工作量
當您檢視知識庫建議時，被顯示記錄搜尋結果列出所有伺服器和從 Operations Manager OMS 已知的工作量。

![伺服器與負載](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## <a name="next-steps"></a>後續步驟

- 若要檢視詳細的設定評估資料使用[記錄分析中的記錄檔搜尋](log-analytics-log-searches.md)。
