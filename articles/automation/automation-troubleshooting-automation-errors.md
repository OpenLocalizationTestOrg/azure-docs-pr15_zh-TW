<properties
   pageTitle="Azure 自動化錯誤處理 |Microsoft Azure"
   description="本文提供基本的錯誤處理步驟來疑難排解並修正常見的 Azure 自動化錯誤。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="自動化錯誤，錯誤處理"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>錯誤處理常見 Azure 自動化錯誤的秘訣

本文說明一些常見的 Azure 自動化錯誤可能會遇到，並會建議可能的錯誤處理的步驟。

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>使用 Azure 自動化 runbooks 時疑難排解驗證錯誤  

### <a name="scenario-sign-in-to-azure-account-failed"></a>案例︰ 登入失敗的 Azure 帳戶

**錯誤︰**使用 [新增 AzureAccount 或登入 AzureRmAccount cmdlet 時，您會收到 「 Unknown_user_type:: 未知使用者 Type 」 的錯誤。

**錯誤的原因︰**如果不是有效的認證資產名稱，或使用者名稱和密碼，您用來設定自動化認證資產不是有效值，就會發生這個錯誤。

**疑難排解秘訣︰**若要決定了什麼問題，請執行下列步驟︰  

1. 請確定您沒有任何特殊字元，包括**@**自動化認證資產名稱會用來連線到 Azure 中的字元。  

2. 核取您可以使用的使用者名稱和密碼儲存於本機 PowerShell ise [以系統編輯器 Azure 自動化認證。 您可以在 PowerShell ise [以系統執行下列 cmdlet:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. 如果您驗證失敗，這表示您還沒有正確地設定您的 Azure Active Directory 認證。 請參閱[Azure 使用 Azure Active Directory 至 Authenticating](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)部落格文章，以取得正確設定的 Azure Active Directory 帳戶。  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>案例︰ 找不到 Azure 訂閱

**錯誤︰**您收到錯誤訊息 「 訂閱名稱``<subscription name>``找不到 「 使用選取 AzureSubscription 或選取 AzureRmSubscription cmdlet 時。

**錯誤的原因︰**如果不是有效的訂閱名稱，或嘗試訂閱詳細資料的 Azure Active Directory 使用者未設定為訂閱的系統管理員，就會發生這個錯誤。

**疑難排解秘訣︰**若要判斷您已正確地通過驗證 Azure 和有權存取您嘗試選取的訂閱，請執行下列步驟︰  

1. 請確定您在執行**選取 AzureSubscription**指令程式之前，先執行**新增 AzureAccount** 。  

2. 如果您仍然會看到這則錯誤訊息，藉由新增**取得 AzureSubscription** cmdlet 追蹤**新增 AzureAccount**指令程式修改您的程式碼，然後執行的程式碼。  現在請確認取得 AzureSubscription 的輸出是否包含您的訂閱明細。  
    * 如果您沒有看到輸出中的任何訂閱詳細資料，這表示訂閱還未初始化。  
    * 如果您看到訂閱詳細資料，輸出中的，確認您使用正確的訂閱名稱或識別碼的**選取 AzureSubscription**指令程式。   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>案例︰ Azure 驗證失敗，因為已啟用多重因素驗證

**錯誤︰**您會收到錯誤 」 新增 AzureAccount: AADSTS50079︰ 強式驗證註冊 （證明向上） 是必要 」 時 Azure 驗證使用 Azure 的使用者名稱和密碼。

**錯誤的原因︰**如果您有多因素驗證 Azure 帳戶上時，您無法使用的 Azure Active Directory 使用者驗證 Azure。  不過，您需要使用憑證或主要服務 Azure 進行驗證。

**疑難排解秘訣︰**若要使用的憑證透過 Azure 服務管理 cmdlet，請參閱[建立及新增管理 Azure 服務的憑證。](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) 若要使用的服務主要透過 Azure 資源管理員 cmdlet，請參閱建立[使用 Azure 入口網站的服務主要](./resource-group-create-service-principal-portal.md)及[驗證服務主要與 Azure 資源管理員。](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>使用 runbooks 時疑難排解常見錯誤

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>案例︰ Runbook 失敗，因為還原序列化的物件

**錯誤︰**您 runbook 失敗時，發生錯誤 」 無法繫結參數``<ParameterName>``。 無法轉換``<ParameterType>``的值類型 Deserialized``<ParameterType>``輸入``<ParameterType>``」。

**錯誤的原因︰**如果您 runbook PowerShell 工作流程，它會儲存複雜的物件還原序列化格式以保留您的 runbook 狀態，如果暫停工作流程。  

**疑難排解秘訣︰**  
下列三種解決方案的任何會修正這個問題︰

1. 如果您線路複雜物件到另一個 cmdlet，將這些 cmdlet InlineScript 中。  
2. 從複雜的物件，而非整個物件傳遞給傳遞的名稱或您需要的值。  

3. 使用 PowerShell runbook，而不是 PowerShell 工作流程 runbook。  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>案例︰ Runbook 工作失敗，因為超過配置的配額

**錯誤︰**您 runbook 作業失敗時，發生錯誤 」 對此訂閱已達到次執行每月總工作的配額 」。

**錯誤的原因︰**作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。 此配額適用於所有類型的工作執行工作，例如測試工作、 從入口網站開始工作、 執行工作使用 webhooks 及排程來執行使用任一 Azure 入口網站或工作中資料中心。 若要瞭解關於價格自動化的詳細資訊請參閱[自動化價格](https://azure.microsoft.com/pricing/details/automation/)。

**疑難排解秘訣︰**如果您想要使用 500 分鐘以上的每個月的處理您需要變更您的訂閱從 Free 層以基本層。 您可以採取下列步驟，就可升級至基本層︰  

1. 登入您的 Azure 訂閱  
2. 選取您想要升級的自動化帳戶  
3. 按一下 [**設定** > **價格層及使用方式** > **價格層**  
4. 在 [**選擇您的價格層**刀中，選取 [**基本**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>案例︰ 執行 runbook 時，無法辨識的 Cmdlet

**錯誤︰**您 runbook 作業失敗時，發生錯誤 」``<cmdlet name>``︰ 字詞``<cmdlet name>``無法辨識的 cmdlet、 函數、 指令碼檔或可執行的程式名稱為。 」

**錯誤的原因︰**PowerShell 引擎找不到您使用的在您 runbook cmdlet 時，會發生這個錯誤。  這可能是因為包含 cmdlet 的模組會缺少帳戶、 有名稱衝突的使用者使用 runbook 名稱，或另一個模組中也有 cmdlet 和自動化無法解析的名稱。

**疑難排解秘訣︰**下列解決方案的任何會修正問題︰  

- 核取您所輸入的指令程式的名稱正確。  

- 請確定 cmdlet 存在自動化帳戶中有不會發生衝突。 若要確認是否存在 cmdlet，以開啟 runbook 編輯模式，然後搜尋您想要尋找在文件庫或執行指令程式**取得命令``<CommandName>``**。  一旦您已驗證的 cmdlet，就能使用該帳戶，並與其他 cmdlet 或 runbooks 沒有名稱衝突的將其新增至畫布上，並確保您使用的有效的參數，在您 runbook 中設定。  

- 如果您沒有名稱衝突的使用者，而且 cmdlet 兩個不同的模組中，您可以解決這個問題使用 cmdlet 的完整的名稱。 例如，您可以使用**ModuleName\CmdletName**。  

- 如果您執行 runbook 內部部署混合式背景工作群組中，然後確定裝載混合式工作者的電腦上安裝就模組/指令程式。


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>案例︰ 長時間執行 runbook 一致失敗的例外狀況: 「 作業無法繼續執行，因為它已重複收回從相同的檢查點 」。

**錯誤的原因︰**這是 Azure 自動化，如果它在長於 3 小時執行自動暫停 runbook 處理程序 」 展覽共用 「 監控設計的行為。 但是，傳回的錯誤訊息並不提供 「 接下來的內容] 選項。 Runbook 可以擱置的幾個原因。 暫停發生錯誤而大多。 例如，runbook、 網路連線失敗，或當機上執行 runbook Runbook 工作者遺漏的例外，所有會暫停並開始從其最後一個檢查點時繼續 runbook。

**疑難排解秘訣︰**若要避免此問題所記錄的解決方案是工作流程中使用檢查點。  若要瞭解更多請參閱[學習 PowerShell 工作流程](automation-powershell-workflow.md#Checkpoints)。  此部落格文章[Runbooks 中的 [使用檢查點](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/)可以找到更多徹底說明 」 展覽共用 」 和檢查點。


## <a name="troubleshoot-common-errors-when-importing-modules"></a>匯入模組時疑難排解常見錯誤

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>案例︰ 模組無法匯入或 cmdlet 無法執行匯入之後

**錯誤︰**模組匯入失敗，或匯入成功，但沒有 cmdlet 解壓縮。

**錯誤的原因︰**模組可能不成功匯入 Azure 自動化一些常見原因包括︰  

- 結構不符合自動化需求，才能在中的結構。  

- 模組是另一個尚未部署至您的自動化帳戶的模組而定。  

- 在模組遺失相依性] 資料夾中。  

- 用於**新增 AzureRmAutomationModule**指令程式上傳模組，並沒有提供完整的儲存空間路徑，或不會載入模組使用公開存取 URL。  

**疑難排解秘訣︰**  
下列解決方案的任何會修正問題︰  

- 請確定模組遵循下列格式︰  
ModuleName.Zip **->** ModuleName 或版本號碼**->**（ModuleName.psm1、 ModuleName.psd1）

- 開啟.psd1 檔案，請參閱模組是否有任何相依性。  如果是這樣，將這些模組上傳到您的自動化帳戶。  

- 請確定 [模組] 資料夾中的任何參照的.dlls。  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>疑難排解常見錯誤時使用的所需的狀態設定 (DSC)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>案例︰ 節點狀態為失敗，「 找不到 」 的錯誤

**錯誤︰**節點具有與**失敗**狀態含有錯誤報告 」 嘗試從伺服器 https:// 取得動作``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction 失敗，因為正確的設定``<guid>``找不到。 」

**錯誤的原因︰**通常會發生此錯誤節點指派給設定名稱 (例如 ABC) 時，而不是節點設定名稱 (例如 ABC。Web 伺服器上）。  

**疑難排解秘訣︰**  

- 請確認您要指定 「 節點設定名稱 」 而不是 「 設定名稱 」 的節點。  

- 若要使用 Azure 入口網站的節點，或使用 PowerShell cmdlet，您可以指定節點設定。
    - 節點設定為使用 Azure 入口網站的節點，才能開啟**DSC 節點**刀，然後選取節點並按一下 [**指派節點設定**] 按鈕。  
    - 若要指派節點設定以使用 PowerShell 指令程式的節點，請使用 [**設定 AzureRmAutomationDscNode** cmdlet


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>案例︰ 沒有節點設定 （MOF 檔案） 所產生編譯設定時

**錯誤︰**DSC 編譯工作暫停錯誤: 「 編譯已順利完成，但是沒有節點設定.mofs 產生 」。

**錯誤的原因︰**當您將產生運算式下列 DSC 設定中的**節點**關鍵字評估 $null 然後沒有節點設定。    

**疑難排解秘訣︰**  
下列解決方案的任何會修正問題︰  

- 請確定設定定義中的**節點**關鍵字] 旁的運算式不會評估所至 $null。  
- 如果您傳遞 ConfigurationData 編譯設定時，，請確定您從[ConfigurationData](automation-dsc-compile.md#configurationdata)傳遞需要設定的預期的值。


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>案例︰ DSC 節點報表會變成卡 」 中進度 」 狀態

**錯誤︰**DSC 代理程式輸出 「 找不執行個體與指定的屬性值。 」

**錯誤的原因︰**您已升級您 WMF 版本，而且已損毀 WMI。  

**疑難排解秘訣︰**請遵循[DSC 已知問題和限制](https://msdn.microsoft.com/powershell/wmf/limitation_dsc)部落格文章中的指示來修正問題。


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>案例︰ 無法使用 DSC 設定中的認證

**錯誤︰**DSC 編譯工作已擱置錯誤: 「 System.InvalidOperationException 錯誤處理內容類型的 「 認證``<some resource name>``︰ 轉換與儲存加密的密碼，為純文字 PSDscAllowPlainTextPassword 設定時，才允許為 true 」。

**錯誤的原因︰**您已使用的認證設定，但未提供適當的**ConfigurationData**將**PSDscAllowPlainTextPassword**設為 true，則每個節點設定。  

**疑難排解秘訣︰**  
- 請確定傳入適當**ConfigurationData** **PSDscAllowPlainTextPassword**設定為每個節點設定設定中所提及的 true。 如需詳細資訊，請參閱[Azure 自動化 DSC 資產](automation-dsc-compile.md#assets)。


## <a name="next-steps"></a>後續步驟

如果您已遵循上述的疑難排解步驟，需要額外的說明，本文中的任一點時，您可以︰

- 從 Azure 專家取得協助。 提交您的問題，即可[MSDN Azure 或堆疊溢位論壇。](https://azure.microsoft.com/support/forums/)

- 檔案 Azure 支援事件。 移至[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下**技術和帳單支援**] 底下的 [**取得支援**。

- 如果您正在尋找 Azure 自動化 runbook 解決方案或整合模組，張貼文章[指令碼](https://azure.microsoft.com/documentation/scripts/)中心的指令碼要求。

- Azure 自動化[使用者語音](https://feedback.azure.com/forums/34192--general-feedback)上張貼意見反應或功能的要求。
