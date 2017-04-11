<properties
    pageTitle="Azure AD Connect 同步處理︰ 設定篩選 |Microsoft Azure"
    description="說明如何設定中 Azure AD Connect 同步處理的篩選。"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect 同步處理︰ 設定篩選
使用篩選，您可以控制哪一個物件時，應該出現在 Azure AD 上，從您的內部部署目錄。 預設的設定中設定樹系中所有的網域，會發生 「 所有 」 物件。 一般而言，這是建議的設定。 使用 Office 365 工作負載，例如 Exchange Online 和商務用 Skype，使用者可以完成的全域通訊清單，讓他們可以傳送電子郵件與通話每個人。 使用預設設定，將會被相同的 Exchange 或 Lync 內部部署實作一樣的經驗。

在某些情況下，需要進行一些變更為預設設定。 以下是一些範例︰

- 您打算使用[多重 Azure AD 目錄拓撲](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory)。 然後您要套用篩選，以控制的物件應為特定同步處理 Azure AD 目錄。
- 您可以執行試驗 Azure 或 Office 365，您只想 Azure AD 使用者子集。 在小型的試驗，不一定要以完成的全域通訊清單以示範功能。
- 您有多個服務帳戶及其他非個人帳戶，您不想在 Azure AD。
- 基於規範您不要刪除任何使用者帳戶內部部署。 您只停用它們。 但 Azure AD 中，您只想要有作用中的帳戶。

本文說明如何設定不同的篩選方式。

> [AZURE.IMPORTANT]Microsoft 不支援的修改或外部正式的記錄這些動作 Azure AD Connect 同步處理的作業。 任何動作，可能會產生不一致的或不受支援的 Azure AD Connect 同步處理狀態，如此一來，Microsoft 無法提供的技術支援這類部署。

## <a name="basics-and-important-notes"></a>基本功能和重要的備忘稿
Azure AD Connect 同步處理，您可以啟用篩選在任何時間。 如果您開始使用目錄同步處理的預設設定，然後設定篩選時，會篩選出的物件會不會再同步處理至 Azure AD。 這項變更，當做中 Azure AD 先前同步處理，但然後篩選過的任何物件在 Azure AD 中刪除。

在您開始進行之前會變更為篩選，請確定您[停用排程的任務](#disable-scheduled-task)，您不小心匯出尚未驗證是正確的變更。

篩選可以同時中移除多個物件，因為您想要確定開始 Azure ad 匯出的任何變更之前，您新增的篩選是正確。 您已完成設定步驟之後，強烈建議您匯出及變更 Azure AD 之前，請遵循[步驟](#apply-and-verify-changes)。

若要保護您意外刪除多個物件，[避免意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)功能預設為開啟。 如果您刪除多個物件，因為篩選 (預設為 500) 時，必須先依照允許前往 Azure AD 刪除本文中的步驟。

如果您是使用建置年 11 月 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250))，以篩選設定進行變更，並使用密碼同步處理之前，您就需要觸發完整的同步處理所有密碼完成之後，您的設定。 如需如何觸發密碼完整同步處理的步驟，請參閱[觸發程序完整的同步處理所有密碼](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords)。 如果您是在 1.0.9125 或更新版本，然後一般**完整的同步處理**動作也會計算如果應該同步密碼和不再需要此額外的步驟。

如果 Azure AD 中篩選錯誤不慎刪除**使用者**物件，您可以移除您的篩選設定中 Azure AD 重新建立使用者物件，然後再次同步處理您的目錄。 這個動作會將使用者從資源回收筒還原 Azure AD 中。 不過，您無法取消刪除其他物件的類型。 例如，如果您不小心刪除安全性群組，並將其用於 ACL 資源、 群組和其 Acl 無法復原。

Azure AD Connect 只會刪除要範圍中一次被視為的物件。 如果有 Azure AD 所建立的另一個同步處理引擎的物件，這些物件無法在範圍中新增篩選請勿移除它們。 例如，如果開始您會使用目錄同步伺服器它建立您整個目錄的完整複本中 Azure AD 和平行篩選功能從頭安裝新的 Azure AD Connect 同步處理伺服器，它不會移除額外的物件，建立的目錄同步。

安裝或升級至新版的 Azure AD Connect 時，會保留篩選設定。 一律是以驗證您的變更設定的不會不慎在升級後較新版本後再繼續執行第一個同步處理循環的最佳作法。

如果您有多個樹系，本主題中描述的篩選設定必須套用至每個樹系 （假設您想要的所有相同的設定）。

### <a name="disable-scheduled-task"></a>停用排程的任務
若要停用觸發同步處理循環圖每 30 分鐘內建排程器，請遵循下列步驟︰

1. 移至 PowerShell 提示。
2. 執行`Set-ADSyncScheduler -SyncCycleEnabled $False`停用排程器。
3. 在本主題中做的變更。
4. 執行`Set-ADSyncScheduler -SyncCycleEnabled $True`再次啟用排程器。

**如果您是使用前 1.1.105.0 Azure AD Connect 建置**  
若要停用 [排定的工作會觸發同步循環每 3 小時，請遵循下列步驟︰

1. 從 [開始] 功能表中開始**工作排程器**。
2. 直接在 [**工作排程器程式庫**] 底下，尋找名為 「 **Azure AD Sync 排程器**，以滑鼠右鍵按一下，然後選取 [**停用**任務。  
![工作排程器](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. 現在，您可以變更的設定，並從**同步處理服務管理員**主控台手動執行同步處理引擎。

當您完成所有的篩選變更後，不要忘記了返回並**啟用**工作一次。

## <a name="filtering-options"></a>篩選選項
下列的篩選設定類型可套用至目錄同步作業工具︰

- [**群組基礎**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)︰ 篩選以一個群組可以只在 [設定使用安裝精靈的初始安裝。 它不進一步討論本主題中。

- [**網域為基礎**](#domain-based-filtering)︰ 這個選項可讓您選取 [同步處理至 Azure AD 的網域。 也可讓您新增及移除從同步處理引擎設定的網域，如果您安裝 Azure AD Connect 同步處理後，對您的內部部署進行變更。

- [**組織單位基礎**](#organizational-unitbased-filtering)︰ 此篩選選項可讓您選取的 Ou 同步處理至 Azure AD。 選取 Ou 中的所有物件類型都，這個選項。

- [**屬性-基礎**](#attribute-based-filtering)︰ 這個選項可讓您篩選的物件的屬性值為基礎的物件。 您也可以有不同的篩選，針對不同的物件類型。

您可以同時使用多個篩選的選項。 例如，您可以使用 OU 型篩選以僅包含物件，在一個 OU 和的相同時間屬性為基礎的篩選來篩選進一步的物件。 當您使用多個篩選的方法時，篩選會使用篩選] 之間的邏輯 AND。

## <a name="domain-based-filtering"></a>網域為基礎的篩選
本節提供您設定您的網域篩選器的步驟。 如果您已加入，或在樹系中移除網域，安裝 Azure AD Connect 之後，您也必須更新的篩選設定。

若要變更的網域為基礎篩選慣用的方法是執行安裝精靈，變更[的網域和 Ou 篩選](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)。 在安裝精靈會自動在本主題中的所有工作。

如果您基於某種原因是無法執行安裝精靈，您應該才能執行下列步驟。

網域為基礎的篩選設定包含下列步驟︰

- [選取網域](#select-domains-to-be-synchronized)，應包含在同步處理。
- 針對每個新增和移除的網域，調整[執行設定檔](#update-run-profiles)。
- [套用並確認變更](#apply-and-verify-changes)。

### <a name="select-domains-to-be-synchronized"></a>選取 [同步處理的網域
**若要設定網域篩選，請執行下列步驟︰**

1. 登入伺服器執行 Azure AD Connect 同步處理的帳戶**ADSyncAdmins**安全性群組的成員。
2. 從 [開始] 功能表開始**同步處理服務**。
3. 選取**連接器**並在 [**連接器**] 清單中，選取連接器類型**Active Directory 網域服務**。 從 [**動作**]，選取 [**內容**]。  
![連接器內容](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. 按一下 [**設定目錄分割**。
5. 在**選取目錄分割**] 清單中，選取，然後視需要請取消選取網域]。 確認已選取您要同步處理的磁碟分割。  
![磁碟分割區](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
如果您變更了您的內部部署 AD 基礎架構與新增或移除網域樹系，然後按一下 [**重新整理**] 按鈕，以取得更新的清單。 當您更新時，會要求您輸入認證。 提供的讀取權限的任何認證儲存到您的內部部署 Active Directory。 它沒有是在對話方塊中已預先填入的使用者。  
![需要重新整理](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. 當您完成時，請按一下**[確定]**關閉 [**屬性**] 對話方塊。 如果您已將網域移除樹系，郵件快顯拒絕已移除網域及設定將會清除。
7. 繼續調整[執行設定檔](#update-run-profiles)。

### <a name="update-run-profiles"></a>更新執行的設定檔
如果您已更新您的網域篩選，您也需要更新執行的設定檔。

1. 在 [**連接器**] 清單中，請確定已選取您在上一個步驟中變更的連接器。 從 [**動作**] 下，選取 [**設定執行的設定檔**]。  
![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

您需要調整下列設定檔︰

- 完整匯入
- 完整的同步處理
- Delta 匯入
- Delta 同步處理
- 匯出

針對每個五個設定檔，請執行下列步驟針對每個**新增**的網域︰

1. 選取 [執行的設定檔，然後按一下 [**新的步驟**。
2. **設定步驟**在頁面上，在 [**類型**] 下拉式清單，選取具有相同名稱步驟類型為您設定的設定檔。 然後按一下 [**下一步**。  
![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. 在**連接器設定**] 頁面中，在 [**分割**] 下拉式清單，選取您已新增至您的網域篩選的網域名稱。  
![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. 若要關閉 [**設定執行的設定檔**] 對話方塊中，按一下 [**完成**]。

針對每個五個設定檔，請執行下列步驟針對每個**移除**網域︰

1. 選取 [執行的設定檔]。
2. 如果 GUID**磁碟分割**屬性的**值**，請選取執行的步驟，然後按一下 [**刪除的步驟**。  
![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

結果應該是您想要同步處理的每個網域應該會列出每個執行的設定檔中的步驟。

若要關閉 [**設定執行的設定檔**] 對話方塊中，按一下**[確定]**。

- 若要完成設定，[套用並確認變更](#apply-and-verify-changes)。

## <a name="organizational-unitbased-filtering"></a>組織單位為基礎的篩選
若要變更 OU 型篩選慣用的方法是執行安裝精靈，變更[的網域和 Ou 篩選](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)。 在安裝精靈會自動在本主題中的所有工作。

如果您基於某種原因是無法執行安裝精靈，您應該才能執行下列步驟。

**若要設定組織單位為基礎的篩選，請執行下列步驟︰**

1. 登入所使用的是**ADSyncAdmins**安全性群組的成員帳戶執行 Azure AD Connect 同步處理的伺服器。
2. 從 [開始] 功能表開始**同步處理服務**。
3. 選取**連接器**並在 [**連接器**] 清單中，選取連接器類型**Active Directory 網域服務**。 從 [**動作**]，選取 [**內容**]。  
![連接器內容](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. 按一下 [**設定目錄分割**，選取您想要設定的網域，然後按一下 [**容器**。
5. 出現提示時，提供給任何認證的讀取權限到您的內部部署 Active Directory。 它沒有是在對話方塊中已預先填入的使用者。
6. 在 [**選取 [容器**] 對話方塊中，清除 Ou 不想要與雲端目錄同步處理，然後按一下**[確定]**。  
![組織單位](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - 在 Windows 10 電腦順利進行同步處理至 Azure AD 應選取**電腦**容器。 如果您加入網域的電腦位於其他 Ou，請確定那些已選取。
  - 如果您有多個信任，應選取**ForeignSecurityPrincipals**容器。 此方塊可讓解析跨樹系安全性群組成員資格。
  - 如果您已啟用裝置回寫功能，應該已選取**RegisteredDevices** OU。 如果您使用另一個回寫功能，例如群組回寫，請確定下列位置選取。
  - 選取使用者、 iNetOrgPersons、 群組、 連絡人及電腦的所在位置的任何其他 OU。 在圖片]，所有這些位於 ManagedObjects OU。
7. 當您完成時，請按一下**[確定]**關閉 [**屬性**] 對話方塊。
8. 若要完成設定，[套用並確認變更](#apply-and-verify-changes)。

## <a name="attribute-based-filtering"></a>屬性架構的篩選
請確定您位於年 11 月 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) 或更新版本，搭配使用這些步驟建立。

根據屬性篩選是篩選物件最有彈性的方式。 若要控制幾乎所有物件應 Azure ad 同步處理時，您可以使用的[宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)power。

套用篩選二者[輸入](#inbound-filtering)到 metaverse Active directory 和[輸出](#outbound-filtering)metaverse 從 Azure ad。 建議您以套用篩選上輸入，因為這是最簡單的方法，若要維持。 輸出篩選應該只能用於視需要前就可以進行評估加入來自多個樹系的物件。

### <a name="inbound-filtering"></a>輸入篩選
輸入根據篩選使用預設的設定未同步處理設定值 metaverse 屬性 cloudFiltered 物件移至 Azure AD 應有的位置。 如果這個屬性的值設為**True**時，不是同步物件。 它不應該設定為 [ **False**的設計。 若要確保其他規則可以參與一個值，此屬性僅應該要有的值**，則為 True**或**NULL** （沒有）。

中輸入的篩選，您可以使用的**範圍**來判斷哪個物件應該或不會同步。 這是您在其中進行調整，以符合您的組織的需求。 [範圍] 模組必須決定同步處理規則的範圍應該位於**群組**與**子句**。 **群組**包含一或多個**子句**。 有多個子句和邏輯或多個群組之間的邏輯 AND。

讓我們看看範例︰  
![範圍](./media/active-directory-aadconnectsync-configure-filtering/scope.png)這應為讀取**(部門 = IT) 或者 (部門 = 銷售與 c = 適用於美國)**。

在範例和下列步驟，您使用的使用者物件作為範例，但您可以使用這個所有的物件類型。

在下面的範例中，[優先順序] 的值開頭 500。 此值，可確保這些規則的評估之後現成的規則 （較低的優先順序，較高的數字值）。

#### <a name="negative-filtering-do-not-sync-these"></a>篩選 」 不同步處理這些 」 的負數
在下列範例中，篩選出 （不同步處理） **extensionAttribute15** ，使用 [**非同步**] 的值的所有使用者。

1. 登入伺服器使用的是**ADSyncAdmins**安全性群組的成員帳戶執行 Azure AD Connect 同步處理。
2. 從 [開始] 功能表開始**同步處理規則編輯器**。
3. 請確定已選取 [**輸入**，然後按一下 [**新增規則**]。
4. 為這個規則的描述性名稱，例如 「*中從 AD – 使用者 DoNotSyncFilter*」。 為**MV 物件類型**選取正確的樹系， **CS 物件類型**]，為**使用者**和**人員**。 為**連結類型**，選取 [**加入**和優先順序中輸入目前並未由其他同步處理規則 (例如 500) 的值，然後按一下**下一步**。  
![輸入 1 的描述](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. 在**領域設定篩選器**中，按一下 [**新增群組****加入子句**，，然後按一下屬性中選取 [ **ExtensionAttribute15**。 請確定運算子設為**相同**，並在 [值] 方塊中輸入值**非同步**。 按一下 [**下一步**]。  
![輸入 2 的範圍](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. 保留空白，**加入**規則，然後按一下 [**下一步**。
7. 按一下 [**新增轉換**至**常數** **FlowType** 、 選取目標屬性**cloudFiltered** ，然後選取在 [來源文字] 方塊中，輸入 [ **True**。 按一下 [**新增**] 以儲存規則]。  
![輸入 3 轉換](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. 若要完成設定，[套用並確認變更](#apply-and-verify-changes)。

#### <a name="positive-filtering-only-sync-these"></a>篩選，「 只同步處理這些 」 的正面
表示正數的篩選可能會更容易，因為您必須同時考慮無法同步處理，例如會議室明顯的物件。

正數的篩選選項需要兩個同步處理規則。 一個 （或多個） 與同步處理的物件和秒的正確範圍捕捉所有同步處理規則，篩選出尚未識別為應同步處理的物件的所有物件。

在下列範例中，您僅同步處理使用者物件位置的部門屬性有 [**銷售**] 的值。

1. 登入伺服器執行 Azure AD Connect 同步處理的帳戶**ADSyncAdmins**安全性群組的成員。
2. 從 [開始] 功能表開始**同步處理規則編輯器**。
3. 請確定已選取 [**輸入**，然後按一下 [**新增規則**]。
4. 為這個規則描述性的名稱，例如 「*中 AD – 從使用者銷售同步處理*]。 為**MV 物件類型**選取正確的樹系， **CS 物件類型**]，為**使用者**和**人員**。 為**連結類型**，選取 [**加入**和優先順序中輸入目前並未由其他同步處理規則 (例如 501) 的值，然後按一下**下一步**。  
![輸入 4 的描述](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. 在**領域設定篩選器**中，按一下 [**新增群組****加入子句**，，然後按一下屬性中選取 [**部門**。 請確定運算子設為**相同**，並在 [值] 方塊中輸入 [**銷售**] 的值。 按一下 [**下一步**]。  
![輸入 5 的範圍](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. 保留空白，**加入**規則，然後按一下 [**下一步**。
7. 按一下 [**新增轉換**至**常數** **FlowType** 、 選取目標屬性**cloudFiltered** ，然後選取在 [來源文字] 方塊中，輸入**False**。 按一下 [**新增**] 以儲存規則]。  
![輸入 6 轉換](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
這是選擇性的情況下，設定 cloudFiltered 明確為 False。

    現在，我們有建立捕捉所有同步處理規則。

8. 為這個規則的描述性名稱，例如 「*中從 AD – 使用者捕捉所有篩選*」。 為**MV 物件類型**選取正確的樹系， **CS 物件類型**]，為**使用者**和**人員**。 為**連結類型**，選取 [**加入**並優先順序中輸入目前並未由其他同步處理規則 (例如 600) 的值。 您已選取的優先順序值 （較低優先順序） 高於先前的同步處理規則，但也剩餘的空間，讓我們可以新增多個篩選的同步處理規則稍後當您要開始同步處理其他部門。 按一下 [**下一步**]。  
![輸入 [7 描述](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. 留**領域設定篩選**，然後按一下 [**下一步**]。 空白的篩選器會指出規則應該套用至所有物件。
10. 保留空白，**加入**規則，然後按一下 [**下一步**。
11. 按一下 [**新增轉換**至**常數** **FlowType** 、 選取目標屬性**cloudFiltered** ，然後選取在 [來源文字] 方塊中，輸入 [ **True**。 按一下 [**新增**] 以儲存規則]。  
![輸入 3 轉換](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. 若要完成設定，[套用並確認變更](#apply-and-verify-changes)。

如果您需要您可以建立其他規則的第一個位置我們同步處理中包含多個物件的類型。

### <a name="outbound-filtering"></a>輸出篩選
在某些情況下，則必須執行的篩選，只在物件已加入斷之後。 例如，可能需要查看資源樹系和 userPrincipalName 屬性來決定物件應該進行同步處理帳戶樹系郵件屬性。 在下列情況下，您可以建立輸出規則的篩選。

在此範例中，您可以變更位置郵件和 userPrincipalName 結尾的篩選，只使用者@contoso.com會同步處理︰

1. 登入伺服器執行 Azure AD Connect 同步處理的帳戶**ADSyncAdmins**安全性群組的成員。
2. 從 [開始] 功能表開始**同步處理規則編輯器**。
3. 在 [**規則類型**] 底下，按一下 [**輸出**]。
4. 尋找名為**外出 AAD – 使用者加入 SOAInAD**規則。 按一下 [**編輯**]。
5. 在快顯功能表中，回答**[是]**來建立規則的複本。
6. 在 [**描述**] 頁面中，變更未使用的值，例如 50 的優先順序。
7. 按一下左側導覽**領域設定篩選**。 按一下 [**新增子句**，屬性選取 [**郵件**] 中，選取**ENDSWITH**運算子，及值類型**@contoso.com**。 按一下 [**新增子句**，請選取**userPrincipalName**屬性、 選取**ENDSWITH**運算子，及值類型**@contoso.com**。
8. 按一下 [**儲存**]。
9. 若要完成設定，[套用並確認變更](#apply-and-verify-changes)。

## <a name="apply-and-verify-changes"></a>套用並確認變更
您所做的設定變更之後，這些必須套用至已存在系統中的物件。 也可能是目前不在同步處理引擎的物件應該處理，並同步處理引擎需要讀取來源系統，確認其內容。

如果您使用**網域**或**組織單位**篩選的設定，您需要執行**完整匯入**後面接著**Delta 同步處理**。

若要變更使用**屬性**篩選的設定，您需要執行**完整的同步處理**。

請執行下列步驟︰

1. 從 [開始] 功能表開始**同步處理服務**。
2. 選取**連接器**並在 [**連接器**] 清單中，選取您所做變更先前設定的連接器。 從 [**動作**] 下，選取 [**執行**]。  
![執行的連接器](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. 在**執行設定檔**，請選取上一節中所提及的作業。 如果您需要執行兩個動作，請執行第二個完成的第一個之後 （**狀態**] 欄位是針對所選的連接線**閒置**）。

同步處理之後, 的所有變更分段都安裝匯出。 您實際 Azure AD 中進行變更之前，您會想要確認所有這些變更都正確無誤。

1. 啟動命令提示字元，然後移至`%Program Files%\Microsoft Azure AD Sync\bin`
2. 執行︰`csexport "Name of Connector" %temp%\export.xml /f:x`  
在同步處理服務找的連接器名稱。 有類似 「 contoso.com – AAD 」 的名稱為 Azure AD。
3. 執行︰`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. 現在，您會有 %temp%名為 export.csv，可以在 Microsoft Excel 中檢視中的檔案。 這個檔案包含要匯出的所有變更。
5. 資料或設定進行必要的變更，並執行下列步驟執行一次 （匯入、 同步處理] 和驗證），直到應該要匯出的變更。

當您滿意時，請 Azure ad 匯出所做的變更。

1. 選取**連接器**，然後選取 [**連接器**] 清單中的 [Azure AD 連接器]。 從 [**動作**] 下，選取 [**執行**]。
2. 在**執行設定檔**，請選取 [**匯出**]。
3. 如果您的設定變更刪除多個物件，然後您看到錯誤的匯出時數大於設定閾值 （依預設 500）。 如果您看到此錯誤，您就需要暫時停用功能，[避免意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)。

現在，您可以再次啟用排程器的時間。

1. 從 [開始] 功能表中開始**工作排程器**。
2. 直接在 [**工作排程器程式庫**] 底下，尋找名為 「 **Azure AD Sync 排程器**，以滑鼠右鍵按一下，然後選取 [**啟用**任務。

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
