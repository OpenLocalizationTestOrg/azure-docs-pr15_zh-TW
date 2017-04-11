<properties
    pageTitle="最佳化您的環境中記錄分析 SQL 評估解決方案 |Microsoft Azure"
    description="您可以使用 SQL 評估解決方案定期評估風險及您伺服器的環境的狀況。"
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
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="optimize-your-environment-with-the-sql-assessment-solution-in-log-analytics"></a>最佳化您的環境中記錄分析 SQL 評估解決方案


您可以使用 SQL 評估解決方案定期評估風險及您伺服器的環境的狀況。 本文可協助您安裝解決方案，使您可以採取的潛在問題的修正動作。

此方案提供建議特定對您的部署的伺服器的優先順序的清單。 建議的分類跨六個重點，幫助您快速瞭解風險並採取矯正。

所提供的建議為基礎的知識和數以千計的客戶造訪 Microsoft 工程師而獲得的體驗。 每個建議提供問題可能會影響您的原因，以及如何實作建議的變更的相關指引。

您可以選擇最重要至您的組織及追蹤您進度執行風險免費與健全環境的焦點區域。

您已新增方案評估已完成、 摘要後焦點區域的資訊會顯示您的環境中的基礎結構的**SQL 評估**儀表板上。 下列各節說明如何使用**SQL 評估**儀表板，您可以檢視，然後需要 SQL server 基礎結構的 [建議的動作的位置上的資訊。

![SQL 評估] 方塊的圖像](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![SQL 評估儀表板的圖像](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>安裝及設定解決方案
SQL 評估適用於所有目前支援的標準、 開發人員] 及企業版版本的 SQL Server 版本。

安裝和設定方案，請使用下列資訊。

- 在已安裝 SQL Server 的伺服器上必須安裝代理程式。
- SQL 評估解決方案需要每個具有 OMS 代理程式的電腦上安裝的.NET Framework 4。
- 使用 SQL 評估 Operations Manager 代理程式，請時，必須使用作業管理員 Run-As 帳戶。 如需詳細資訊，請參閱下方的[Operations Manager 執行方式 OMS 的帳戶](#operations-manager-run-as-accounts-for-oms)。

    >[AZURE.NOTE] MMA 代理程式不支援作業管理員 Run-As 帳戶。

- 新增到使用中[新增記錄分析解決方案從方案庫](log-analytics-add-solutions.md)所述的程序 OMS 工作區的 SQL 評估解決方案。 有不需要進行其他設定。

>[AZURE.NOTE] 您已新增方案之後，AdvisorAssessment.exe 檔案會新增到與代理程式的伺服器。 設定資料閱讀，然後傳送至雲端的 OMS 服務進行處理。 邏輯會套用至收到的資料和雲端服務記錄的資料。

## <a name="sql-assessment-data-collection-details"></a>SQL 評估集合詳細資料

SQL 評估收集 WMI 資料、 登錄資料、 效能資料，並使用您已啟用代理程式的 SQL Server 動態管理檢視結果。

下表顯示資料集合方法代理程式，無論作業管理員 (是 SCOM) 是必要項目，以及如何通常資料會收集代理程式。

| 平台 | 直接代理程式 | 是 SCOM 代理程式 | Azure 儲存體 | 必要時，是 SCOM 嗎？ | 透過管理群組傳送是 SCOM 代理程式的資料 | 集合頻率 |
|---|---|---|---|---|---|---|
|Windows|![[是]](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![[是]](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![無](./media/log-analytics-sql-assessment/oms-bullet-red.png)|    ![無](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![[是]](./media/log-analytics-sql-assessment/oms-bullet-green.png)|   7 天|

## <a name="operations-manager-run-as-accounts-for-oms"></a>可供 OMS 帳戶的執行方式 operations Manager

記錄 OMS 用途 Operations Manager 代理程式與管理群組，來收集及傳送至 OMS 服務的資料分析。 管理套件提供工作負載的 OMS 基礎值新增服務。 每個工作量需要不同的安全性內容，例如網域的帳戶中執行管理套件的工作量特定權限。 您需要設定作業管理員執行為帳戶提供認證資訊。

若要設定 SQL 評估 Operations Manager 的執行方式帳戶，請使用下列資訊。

### <a name="set-the-run-as-account-for-sql-assessment"></a>設定 SQL 評估的執行方式帳戶

 如果您已經在使用 SQL Server 管理套件，您應該使用該執行為帳戶。

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>SQL 執行為帳戶設定操作主控台

>[AZURE.NOTE] 如果您使用 OMS 直接代理程式，而不是 SCOM 代理程式，管理封包一律會執行中的本機系統帳戶的安全性內容。 略過步驟 1-5，並執行 T SQL 或 Powershell 範例中，指定 NT AUTHORITY\SYSTEM 的使用者名稱。

1. Operations Manager 中開啟作業主控台，然後再按一下 [**管理**。

2. **執行以設定**] 底下按一下 [**設定檔**]，開啟**OMS SQL 評估執行為設定檔**。

3. 在**執行方式帳戶**頁面上，按一下 [**新增**。

4. 選取包含所需的 SQL Server 的認證以 Windows 執行為帳戶，或按一下 [**新增**]，建立一個。
    >[AZURE.NOTE] 執行的帳戶類型必須是 Windows。 執行方式帳戶也必須裝載 SQL Server 執行個體的所有 Windows 伺服器上的本機管理員群組的一部分。

5. 按一下 [**儲存**]。

6. 修改，然後執行下列 T SQL 範例執行為帳戶，才能執行 SQL 評估的最低權限授與每一個 SQL Server 執行個體。 不過，您不需要這麼做，如果是執行為帳戶已是 SQL Server 執行個體的系統管理員伺服器角色的一部分。

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>若要使用 Windows PowerShell 將 SQL 執行為帳戶設定

開啟 PowerShell 視窗，然後當您已更新，您的資訊，請執行下列指令碼︰

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>了解如何建議的優先順序

所做的每個建議提供加權值，識別建議的相對重要性。 顯示只的十個最重要的建議。

### <a name="how-weights-are-calculated"></a>如何計算粗細

Weightings 會根據三個主要因素彙總的值︰

- 識別問題會導致問題*的機率*。 較高的機率等於較大的整體分數，建議。

- 在您的組織可能會發生問題如果問題*的影響*。 較高的影響等於較大的整體分數，建議。

- 實作建議所需的*項目*。 較高的投入比等於較小的整體分數，建議。

每個建議加權是可用的每個焦點區域的總分數的百分比表示。 例如，如果安全性和法規遵循焦點區域中的建議分數的 5%，實作的建議會增加您整體的安全性和法規遵循的成績的 5%。

### <a name="focus-areas"></a>焦點區域

**安全性和法規遵循**-此焦點區域會顯示潛在安全性威脅和遭侵害等，公司政策和技術、 法律和法規遵循需求的建議。

**可用性及業務連續性**-此焦點區域會顯示建議服務可用性、 恢復基礎結構，與商業保護。

**效能與延展性**-此焦點區域會顯示建議，可協助您組織的 IT 基礎架構放大，請確定您的 IT 環境符合目前的效能需求，且可以回覆變更基礎結構需求。

**升級，移轉與部署**-此焦點區域會顯示建議，幫助您升級與移轉，將 SQL Server 部署至現有的基礎結構。

**作業和監視**-此焦點區域會顯示協助簡化 IT 作業、 實作預防維護]，以及最大化效能的建議。

**變更和設定管理**-此焦點區域會顯示建議，幫助保護日常作業，請確定沒有負面影響您的基礎結構，建立變更控制項的程序，變更，以追蹤及稽核系統設定。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>您的目標應該分數 100%，每個焦點區域中？

非必要。 建議為基礎的知識和千分位客戶瀏覽過由 Microsoft 工程師所獲得的體驗。 不過，沒有兩個伺服器基礎架構相同，而可能較多或較少與您相關的特定的建議。 例如，某些安全性的建議可能較不相關，如果您的虛擬機器上公開至網際網路。 可用性建議可能會降低相關低優先順序臨機操作的資料收集與報告提供的服務。 成熟商務重要的問題可能會啟動比較不重要。 您可能要找出哪些焦點區域是您的優先順序，並查看您的分數變更一段時間的方式。

每個建議包含重要性的相關指引。 您應該使用本指南評估是否實作建議適用於，提供您的 IT 服務性質與貴組織的業務需求。

## <a name="use-assessment-focus-area-recommendations"></a>使用評估焦點區域的建議

您可以使用 OMS 評估解決方案之前，您必須安裝的解決方案。 若要閱讀更多有關安裝解決方案，請參閱[從方案庫新增記錄分析解決方案](log-analytics-add-solutions.md)。 安裝之後，您可以檢視摘要建議使用 OMS 中的 [概觀] 頁面上的 [SQL 評估] 方塊。

檢視您的基礎結構，然後向下切入至建議合併彙算的規範評量。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>檢視的焦點區域的建議和採取修正動作

1. 在 [**概觀**] 頁面上，按一下 [ **SQL 評估**磚。
2. 在**SQL 評定**] 頁面上，檢閱摘要資訊的焦點區域刀其中一種，然後按一下其中一個檢視該焦點區域的建議。
3. 在任何焦點區域頁面，您可以檢視您的環境所做的優先順序的建議。 若要檢視詳細資料的建議所做的原因**受影響的物件**] 下按一下 [建議]。  
    ![SQL 評估建議的圖像](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. 您可以採取**的建議動作**所建議的修正動作。 當收件者的項目時，供日後評估會記錄建議動作是和法規遵循分數會增加。 更正後的項目會顯示為**傳遞的物件**。

## <a name="ignore-recommendations"></a>略過的建議

如果您有您想要略過的建議，您可以建立 OMS 會以避免出現在您評估結果的建議使用的文字檔案。

### <a name="to-identify-recommendations-that-you-will-ignore"></a>若要找出您會略過的建議

1.  登入您的工作區，並開啟記錄搜尋。 使用下列查詢清單建議失敗，您的環境中的電腦。

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    以下是顯示記錄搜尋查詢的螢幕擷取畫面︰![失敗的建議](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.  選擇您想要略過的建議。 您會使用 RecommendationId 的下一個程序中的值。


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>建立及使用 IgnoreRecommendations.txt 文字檔案

1.  建立一個名為 IgnoreRecommendations.txt 檔案。
2.  貼上或輸入每個 RecommendationId 每個您想要忽略分開，然後儲存並關閉檔案 OMS 的建議。
3.  將檔案下列資料夾中每一部電腦上您要 OMS 略過的建議。
    - 使用 Microsoft 監控代理程式 （連線直接或透過 Operations Manager）-在電腦上*系統磁碟機*︰ \Program Files\Microsoft 監控 Agent\Agent
    - Operations Manager 管理伺服器-*系統磁碟機*︰ \Program Files\Microsoft 系統管理中心 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>若要確認建議會略過

1.  下一步排程評估執行預設每 7 天後，指定的建議標示為已略過，並不會出現在評估儀表板。
2.  您可以使用下列的記錄檔搜尋查詢清單所有過的建議。

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.  如果您稍後決定您想要查看過的建議，移除任何 IgnoreRecommendations.txt 的檔案，或您可以移除 RecommendationIDs 它們。

## <a name="sql-assessment-solution-faq"></a>SQL 評估解決方案的常見問題集

*評估的執行頻率？*
- 評估執行每 7 天。

*有設定評估的執行頻率的方式嗎？*
- 不行。

*如果發現其他伺服器，我已新增 SQL 評估解決方案之後，將它評估嗎？*
- 是的一旦探索它已開啟，然後從評估每 7 天。

*如果解除伺服器，時，會從評估？*
- 如果伺服器不會將資料送出 3 的週，就會移除。

*沒有資料收集程序的名稱是什麼？*
- AdvisorAssessment.exe

*多久需要收集的資料？*
- 在伺服器上的實際資料集合會約 1 小時。 在有大量的 SQL 執行個體或資料庫的伺服器上花費更長的時間。

*會收集哪些類型的資料？*
- 收集下列類型的資料︰
    - WMI
    - 登錄
    - 效能計數器
    - SQL 動態管理檢視。

*有設定時收集資料的方式嗎？*
- 不行。

*為什麼需要設定執行為帳戶？*
- SQL server 執行 SQL 查詢數量。 執行順序，必須使用以執行為帳戶與 SQL 檢視伺服器狀態權限。  此外，才能查詢 WMI，所需本機系統管理員認證。

*為什麼顯示前 10 建議事項嗎？*
- 可讓您的工作的詳細甚清單，而不是我們建議您將焦點集中在第一次處理優先順序的建議。 您解決這些問題之後，就可以使用其他的建議。 如果您想要查看詳細的清單，您可以檢視所有建議使用 OMS 記錄搜尋。

*有略過建議的方式嗎？*
- 是的請參閱[略過建議](#ignore-recommendations)上述一節。



## <a name="next-steps"></a>後續步驟

- 若要檢視詳細的 SQL 評估資料與建議[搜尋記錄](log-analytics-log-searches.md)。
