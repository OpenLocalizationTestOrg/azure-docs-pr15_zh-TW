<properties
    pageTitle="建立修復計劃 |Microsoft Azure" 
    description="建立 Azure 網站復原容錯及復原群組的虛擬機器和實體伺服器修復計劃。" 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="create-recovery-plans"></a>建立修復計劃

Azure 網站復原服務佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 Azure，或第二個內部部署資料中心，可將複寫機器。 概述讀[什麼是 Azure 網站復原？](site-recovery-overview.md)。


## <a name="overview"></a>概觀

本文提供有關建立和自訂修復計劃的資訊。 

修復計劃包含一或多個包含虛擬機器或您想要放在一起容錯的實體伺服器的排序群組。 修復計劃執行下列動作︰

- 定義機器容錯，然後開始在一起的群組。
- 模型中復原計劃群組在一起分組的電腦之間的相依性。 例如，如果您想要透過失敗，並顯示特定應用程式您想要群組虛擬機器中相同的復原計劃群組該應用程式。
- 自動化，和擴充容錯移轉。 您可以執行的測試，計劃或意外的容錯移轉復原計劃。 您可以自訂指令碼、 Azure 自動化，與手動動作修復計劃。

修復計劃會顯示在網站復原入口網站中的**復原方案**。


在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="before-you-start"></a>在您開始之前

請注意下列事項︰

- 復原計劃不應使用單一與多個網路介面卡混合 Vm。 這是因為混合這些 Vm 不允許 Azure 雲端服務中。
- 您可以延伸修復計劃指令碼與手動的動作。 請注意下列事項︰
    - 撰寫使用 Windows PowerShell 指令碼。
    - 確認指令碼會使用試用攔截區塊，以便適當地處理的例外狀況。 如果有例外狀況的指令碼會停止執行和任務會顯示為失敗。  如果發生錯誤，就不會執行任何剩餘部分的指令碼。 如果這樣當您執行的意外的容錯移轉時，會繼續復原計劃。 如果您執行的計劃的容錯移轉時，會發生這，將會停止復原計劃。 如果這種情況，修正指令碼、 確認，如預期執行，然後再執行一次復原計劃。
    - [寫入主機] 命令無法運作中復原計劃指令碼，而且指令碼會失敗。 如果您想要建立輸出，建立 proxy 指令碼，接著執行您主要的指令碼，並確保所有輸出都傳送出使用 >> 命令。
    - 如果不會傳回 600 數秒內，指令碼時發生逾時。
    - 如果項目會寫到 STDERR，指令碼會分類為失敗。 這項資訊會顯示在 [指令碼執行詳細資料。
    - 如果您使用的 VMM 部署中，請注意:

        - VMM 服務帳戶的內容中執行復原計劃中的指令碼。 請確定此帳戶的指令碼是位置，在遠端共用上有讀取權限，並測試 VMM 服務帳戶權限層級執行指令碼。
        - 在 Windows PowerShell 模組中傳送 VMM cmdlet。 當您安裝 VMM 主控台安裝 VMM Windows PowerShell 模組。 VMM 模組可載入指令碼指令碼中使用下列命令︰ 匯入模組-名稱 virtualmachinemanager。 [取得更多詳細資料](hhttps://technet.microsoft.com/library/hh875013.aspx)。
        - 請確定您有一個以上的文件庫伺服器 VMM 部署中。 依預設 VMM 伺服器的文件庫共用路徑位於本機資料夾名稱 MSCVMMLibrary VMM 伺服器。
        - 如果您的文件庫共用路徑遠端 (或本機但不是會與 MSCVMMLibrary 共用，請依下列方式設定共用 (使用\\libserver2.contoso.com\share\ 作為範例):
            - 開啟 [登錄編輯程式，然後瀏覽至 HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure 網站 Recovery\Registration。
            -  編輯 ScriptLibraryPath 的值，並將值\\libserver2.contoso.com\share\.指定完整的 FQDN。 提供共用位置的權限。
            -  請確定您測試的指令碼具有相同的權限 VMM 服務帳戶，以確保獨立的測試指令碼執行相同的方式，他們會在修復計劃中的使用者帳戶。 在 VMM 伺服器上，設定執行原則略過，如下所示︰
                -  開啟 64 位元的 Windows PowerShell 主控台使用提高權限的權限。
                -  [類型]:**設定 executionpolicy 略過**]。 [取得更多詳細資料](https://technet.microsoft.com/library/ee176961.aspx)。

## <a name="create-a-recovery-plan"></a>建立一個修復計劃

您可以在其中建立一個修復計劃的方式取決於您的網站復原部署。

- **複製 VMware Vm 和實體伺服器**，您建立計劃，並新增包含虛擬機器和修復計劃的實體伺服器複寫群組。
- **複製 HYPER-V Vm （在 VMM 雲朵）**，您建立計劃，並從 VMM 雲端新增受保護的 HYPER-V 虛擬機器，修復計劃。
- **複製 HYPER-V Vm （不在 VMM 雲朵）**，建立計劃，並從 [保護] 群組中新增受保護的 HYPER-V 虛擬機器，修復計劃。
- **舊複寫**，建立一個方案，並將包含要復原計劃的虛擬機器複寫群組。 選取複寫群組，而不是特定的虛擬機器中，因為複寫群組中的所有虛擬機器必須放在一起都容錯 （容錯移轉時所發生的儲存層第一次）。


建立一個修復計劃如下所示︰

1. 按一下 [**復原方案**] 索引標籤 >**建立復原計劃**。
指定復原計劃的來源和目標的名稱。 來源伺服器必須啟用錯誤移轉及復原的虛擬機器。

    - 如果您複製從 VMM VMM 選取**來源類型** > **VMM**，與來源及目標 VMM 伺服器。 按一下 [ **HYPER-V** ，請參閱設定為使用 HYPER-V 複本的雲朵]。 
    - 如果您複製從 VMM VMM 使用舊選取**來源類型** > **VMM**，與來源及目標 VMM 伺服器。 按一下 [**舊**若要查看雲朵舊複寫的設定]。
    - 如果您複製 VMM 從 Azure 選取**來源類型** > **VMM**。  選取來源 VMM 伺服器和**Azure**做為目標。
    - 如果您從 HYPER-V 網站複製選取**來源類型** > **HYPER-V 網站**。 選取 [為來源和**Azure**做為目標的 [網站]。
    - 如果您從 VMware 或實體的內部部署伺服器複製至 Azure，選取 [為來源和目標**Azure** [設定伺服器

2. 在 [**選取虛擬機器**中，選取您想要復原計劃中新增到預設的群組 (群組 1) 的虛擬機器 （或複寫群組）。

## <a name="customize-recovery-plans"></a>自訂修復計劃

新增受保護之後虛擬機器或預設復原複寫群組計劃] 群組中，並建立自訂的方案︰

- **新增新的群組**，您可以新增其他修復計劃群組。 您新增的順序編號您加入的群組。 您可以新增七個群組。 您可以新增更多的電腦或複寫群組至這些新的群組。 請注意，虛擬機器或複寫群組只能包含一個復原計劃] 群組中。
- **新增指令碼**，您可以新增的指令碼之前或之後復原計劃] 群組。 當您新增指令碼時，會將一組新群組的動作。 名稱，例如會建立一組的測試步驟 1] 群組︰ 群組 1︰ 測試步驟。 在此設定會列出所有的測試步驟。 請注意，您可以只指令碼上新增主要網站是否您有部署 VMM 伺服器。
- **新增手動動作**，您可以新增手動之前或之後復原計劃群組執行的動作。 復原計劃執行時，它停止插入手動動作，請點，並對話方塊會提示您指定的手動的動作已完成。

## <a name="extend-recovery-plans-with-scripts"></a>擴充指令碼修復計的劃

您可以將指令碼新增至修復計劃︰

- 如果您有 VMM 來源網站 VMM 伺服器上建立指令碼，並將其加入修復計劃中。
- 如果您複製至 Azure 您可以整合 Azure 自動化 runbooks 修復計劃

### <a name="create-a-vmm-script"></a>建立 VMM 指令碼


建立指令碼如下所示︰

1. 在文件庫共用，建立新的資料夾，例如\<VMMServerName > \MSSCVMMLibrary\RPScripts。 將它放在來源和目標 VMM 伺服器。
2. 建立指令碼 (例如 RPScript)，並核取 [如預期般運作。
3. 將指令碼置於這個位置\<VMMServerName > \MSSCVMMLibrary 之來源及目標 VMM 伺服器上的。

### <a name="create-an-azure-automation-runbook"></a>建立 Azure 自動化 runbook

您可以藉由執行 Azure 自動化 runbook 計劃的一部份擴充修復計劃。 [閱讀更多](site-recovery-runbook-automation.md)。


### <a name="add-custom-settings-to-a-recovery-plan"></a>新增自訂的設定來復原計劃

1. 開啟您想要自訂復原計劃。
2. 按一下以新增虛擬機器或新的群組。
3. 若要新增的指令碼或手動動作按一下在 [**步驟**] 清單中的任何項目，，然後按一下 [**指令碼**或**手動的動作**。 指定是否要新增的指令碼或指令之前或之後選取的項目。 使用 [**移**] 及 [**下移**] 命令按鈕向上或向下移動指令碼的位置。
4. 如果您要新增 VMM 指令碼，請選取 [**容錯移轉至 VMM 指令碼**，並在**指令碼路徑**中輸入共用的相對路徑。 是的我們的範例，時在共用位置\\ <VMMServerName>\MSSCVMMLibrary\RPScripts，指定路徑︰ \RPScripts\RPScript.PS1。
5. 如果您要新增 Azure 自動化執行活頁簿]，指定 runbook 所在， **Azure 自動化帳戶**，然後選取適當的**Azure Runbook 指令碼**。
5. 執行復原計劃以確保指令碼能如預期操作錯誤後的移轉。


## <a name="next-steps"></a>後續步驟

您可以執行不同類型的容錯移轉上復原計劃，包括測試容錯移轉，檢查您的環境和計劃或意外的錯誤後移轉。 [進一步瞭解](site-recovery-failover.md)。


 
