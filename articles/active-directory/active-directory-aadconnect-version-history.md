<properties
   pageTitle="Azure AD Connect︰ 版本版本歷程記錄 |Microsoft Azure"
   description="本主題列出所有新版 Azure AD Connect 和 Azure AD Sync"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect︰ 版本版本歷程記錄

Azure Active Directory 小組定期更新 Azure AD Connect 新功能。 並非所有新增都適用於所有對象。

本文是設計用來協助您追蹤的已發行版本，並瞭解您是否要更新為最新版本，或不。

這是相關主題的清單︰

主題 |  
--------- | --------- |
若要從 Azure AD Connect 升級的步驟 | [升級至最新版的舊版](active-directory-aadconnect-upgrade-previous-version.md)Azure AD 連線發行的不同方法。
必要的權限 | 若要將更新套用所需的權限，請參閱[帳戶及權限](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
下載| [下載 Azure AD 連線](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
發行︰ 2016 年 8 月

**解決的問題︰**

- 下次同步處理循環完成之後，變更同步處理間隔才會直到的位置。
- Azure AD Connect 精靈不接受 Azure AD 帳戶的使用者名稱的開頭底線 (\_)。
- Azure AD Connect 精靈無法驗證 Azure AD 帳戶所提供的帳戶密碼包含太多的特殊字元。 錯誤訊息 「 無法驗證憑證。 意外的錯誤發生。 」 會傳回。
- 解除安裝臨時伺服器停用 Azure AD 租用戶中的密碼同步處理，並會導致失敗作用中的伺服器使用密碼同步處理。
- 沒有儲存在使用者的密碼雜湊時常見的情況下會失敗密碼同步處理。
- 當暫存模式啟用 Azure AD Connect 伺服器時，密碼回寫未暫時停用。
- Azure AD Connect 精靈不會顯示 [實際密碼同步處理] 和 [密碼回寫組態伺服器中執行模式時。 永遠顯示這些已停用。
- 當伺服器位於臨時模式時，Azure AD Connect 精靈不會保存密碼同步處理及密碼回寫的設定變更。

**改良功能︰**

- 指出是否可以順利啟動新的同步處理循環圖，或不更新的開始 ADSyncSyncCycle 指令程式。
- 若要同步處理循環圖及作業目前正在進行中的新增停駐點 ADSyncSyncCycle 指令程式。
- 若要同步處理循環圖及作業目前正在進行中的更新停駐點 ADSyncScheduler 指令程式。
- 在設定[目錄副檔名](active-directory-aadconnectsync-feature-directory-extensions.md)Azure AD Connect 精靈] 中，現在可選取 「 Teletex string 」 類型的 AD 屬性。

## <a name="111890"></a>1.1.189.0
發行︰ 2016 年 6 月

**解決的問題及改良功能︰**

- Azure AD Connect 現在可以 FIPS 相容伺服器上安裝。
    - 密碼同步處理，請參閱[密碼同步處理及 FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- 修正問題位置 NetBIOS 名稱無法解析到 Active Directory 連接器中的 FQDN。

## <a name="111800"></a>1.1.180.0
發行︰ 2016 可能

**新的功能︰**

- 警告，協助您驗證網域，如果您未執行 Azure AD Connect 之前，先進行此動作。
- [Microsoft Cloud 德國](active-directory-aadconnect-instances.md#microsoft-cloud-germany)加入的支援。
- 加入新的 URL 需求最新的[Microsoft Azure 政府雲端](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud)基礎結構支援。

**解決的問題及改良功能︰**

- 新增篩選同步處理規則編輯器，讓您輕鬆找到同步處理規則。
- 刪除連接器空間時改善的效能。
- 相同的物件已刪除的郵件和新增同一執行 （稱為刪除/新增） 時，請修正問題。
- 停用的同步處理規則就不會再重新啟用包含物件和屬性升級或目錄的結構描述在重新整理。

## <a name="111300"></a>1.1.130.0
發行︰ 2016 年 4 月

**新的功能︰**

- 加入的支援多重值屬性[目錄副檔名](active-directory-aadconnectsync-feature-directory-extensions.md)。
- 加入的支援[自動升級](active-directory-aadconnect-feature-automatic-upgrade.md)考量適合升級的更多設定變化。
- 新增[自訂](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler)的排程器的一些指令程式。

## <a name="111190"></a>1.1.119.0
發行︰ 2016 年 3 月

**解決的問題︰**

- 進行確保 Express 安裝無法在 Windows Server 2008 (前 R2) 使用密碼同步處理自不支援此作業系統。
- 以自訂篩選設定升級 DirSync 並未無法如預期運作。
- 升級至新的版本時不沒有設定任何變更，不應該排定完整的匯入/同步處理。

## <a name="111100"></a>1.1.110.0
發行︰ 2016 年 2 月

**解決的問題︰**

- 從舊版升級無法運作如果安裝不是預設**C:\Program Files**資料夾。
- 如果您安裝並取消選取 [**開始同步處理程序...** 在 [安裝] 精靈的最後，重複執行安裝精靈，不會啟動排程器。
- 排程器將無法如預期般在其中的日期/時間格式不是美國短的伺服器上。 將會封鎖`Get-ADSyncScheduler`以傳回正確的時間。
- 如果您使用 ADFS 安裝舊版 Azure AD Connect 為登入選項和升級時，您無法重新執行安裝精靈。

## <a name="111050"></a>1.1.105.0
發行︰ 2016 年 2 月

**新的功能︰**

- [自動升級](active-directory-aadconnect-feature-automatic-upgrade.md)的快速設定客戶的功能。
- 使用 [安裝精靈中的 [MFA 和 PIM 全域管理員的支援。
    - 您必須允許您的 proxy 也讓 https://secure.aadcdn.microsoftonline-p.com 流量，如果您是使用 MFA。
    - 您需要新增 https://secure.aadcdn.microsoftonline-p.com 正常運作的 MFA 您信任的網站清單。
- 允許初始安裝後變更使用者的登入方法。
- 允許在安裝精靈中的 [[網域和 OU 篩選](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)]。 這也可讓連線到樹系並非所有網域都可的位置。
- [排程器](active-directory-aadconnectsync-feature-scheduler.md)是內建同步處理引擎。

**從 preview 升級到 GA 的功能︰**

- [裝置回寫](active-directory-aadconnect-feature-device-writeback.md)。
- [目錄副檔名](active-directory-aadconnectsync-feature-directory-extensions.md)。

**新的預覽功能︰**

- 新的預設同步處理循環圖間隔為 30 分鐘。 用來的所有先前的版本類似的 3 個小時。 新增支援，以變更[排程器](active-directory-aadconnectsync-feature-scheduler.md)行為。

**解決的問題︰**

- DNS 驗證網域] 頁面，不一定能辨識網域。
- 設定 ADFS 時，網域系統管理員認證的提示。
- 內部部署 AD 帳戶的無法識別安裝精靈如果位於不同的 DNS 樹狀目錄比根網域的網域。

## <a name="1091310"></a>1.0.9131.0
發行︰ 2015 年 12 月

**解決的問題︰**

- 當您設定密碼時，變更密碼 AD DS 中，但運作時，可能會無法使用密碼同步處理。
- Azure AD 驗證當您的 proxy 伺服器時，可能無法在安裝或解除升級設定] 頁面上。
- 從舊版 Azure AD Connect 使用完整的更新如果您不是以 SQL 索會失敗 SQL Server。
- 更新與遠端 Azure AD Connect 舊版本的 SQL Server 會顯示 「 無法存取 ADSync SQL 資料庫 」 的錯誤。

## <a name="1091250"></a>1.0.9125.0
發行︰ 2015 年 11 月

**新的功能︰**

- 可以重新設定 Azure AD 信任 ADFS。
- 可以重新整理的 Active Directory 結構描述，然後重新產生同步處理規則。
- 可以停用同步處理規則。
- 可以定義為同步處理規則中的新常值的 「 AuthoritativeNull 」。

**新的預覽功能︰**

- [Azure AD 連線狀況的同步處理](active-directory-aadconnect-health-sync.md)。
- [Azure AD 網域服務](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)密碼同步處理支援。

**新支援的情況︰**

- 支援多個內部部署 Exchange 組織。 如需詳細資訊，請參閱[使用多個 Active Directory 樹系的混合式部署](https://technet.microsoft.com/library/jj873754.aspx)。

**解決的問題︰**

- 密碼同步處理的問題︰
    - 從出的範圍移到範圍內的物件不會有同步處理其密碼。 此 incudes OU 和屬性篩選。
    - 選取要包含在同步處理新的 OU 不需要完整密碼同步處理。
    - 啟用已停用的使用者，則不會不會同步密碼。
    - 密碼重試佇列是無限，並已移除 5000 已停用的物件的前一個限制。
    - [改善疑難排解](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)。
- 無法連線到 Active Directory 與 Windows Server 2016 樹系功能等級。
- 無法變更群組篩選初始安裝後所用的群組。
- Azure AD Connect 伺服器每位使用者的密碼回寫啟用執行變更密碼不再會建立新的使用者設定檔。
- 無法使用 [長整數值同步規則的範圍。
- 如果無法存取網域控制站的 「 裝置回寫] 核取方塊會保持停用。

## <a name="1086670"></a>1.0.8667.0
發行︰ 2015 年 8 月

**新的功能︰**

- Azure AD Connect 安裝精靈會立即當地語系化用於所有 Windows Server 的語言。
- 新增的帳戶支援解除鎖定時使用 Azure AD 密碼管理。

**解決的問題︰**

- 如果其他使用者仍然安裝，而不是第一次開始安裝的人員，會當機 azure AD Connect 安裝精靈。
- 如果完全解除安裝 Azure AD Connect 同步處理的 Azure AD Connect 失敗之前解除安裝，無法重新安裝。
- 無法安裝 Azure AD Connect 使用 Express 安裝，如果使用者不是樹系根網域，或使用 Active Directory 的非英文版本。
- 如果無法解決 Active Directory 使用者帳戶的 FQDN，會顯示誤導的錯誤訊息 「 失敗認可結構描述 」。
- 如果在 Active Directory 連接器所用的帳戶會變更精靈之外，精靈會失敗後續執行。
- Azure AD Connect 有時候無法安裝網域控制站。
- 無法啟用及停用 「 臨時模式 」，如果已新增擴充屬性。
- Active Directory 連接器不正確密碼，因為某些設定就會失敗密碼回寫。
- 如果在 dn 使用屬性篩選，就無法升級目錄同步。
- 過度 CPU 使用率時使用密碼重設。

**移除的預覽功能︰**

- 暫時已移除[使用者回寫](active-directory-aadconnect-feature-preview.md#user-writeback)的 [預覽] 功能會根據預覽客戶的意見反應。 重新將稍後當我們有收件者所提供的意見反應。

## <a name="1086410"></a>1.0.8641.0
發行︰ 2015 年 6 月

**Azure AD Connect 初始發行版本。**

變更要 Azure AD Connect 從 Azure AD Sync 名稱。

**新的功能︰**

- [快速設定](./connect/active-directory-aadconnect-get-started-express.md)安裝
- 可以[設定 ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- 可以[從 DirSync 升級](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [避免意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- 介紹[臨時模式](active-directory-aadconnectsync-operations.md#staging-mode)

**新的預覽功能︰**

- [使用者回寫](active-directory-aadconnect-feature-preview.md#user-writeback)
- [群組回寫](active-directory-aadconnect-feature-preview.md#group-writeback)
- [裝置回寫](active-directory-aadconnect-feature-device-writeback.md)
- [目錄副檔名](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
發行︰ 2015 年

**新的需求︰**

- Azure AD Sync 現在需要安裝.Net framework 版本 4.5.1。

**解決的問題︰**

- 從 Azure AD 密碼回寫失敗 servicebus 連線錯誤。

## <a name="104910413"></a>1.0.491.0413
發行︰ 2015 年 4 月

**解決的問題及改良功能︰**

- Active Directory 連接器不處理刪除正確如果啟用資源回收筒，以及樹系中有多個網域。
- 匯入作業的效能已經過改良的 Azure Active Directory 連接器。
- 當群組超過的成員資格限制 （依預設，限制設定為 50 k 物件），Azure Active Directory 中已刪除的群組。 新的行為是仍會維持該群組，會擲回錯誤，並在沒有新的成員資格變更將會匯出。
- 如果連接器空間中已使用相同的 DN 分段的刪除，無法提供新物件。
- 有些物件會標示為進行同步處理期間 delta 同步處理，雖然不沒有分段物件上任何變更。
- 強制密碼同步處理時，也會移除慣用的 DC 清單。
- CSExportAnalyzer 有一些物件狀態的問題。

**新的功能︰**

- 聯結現在可以連線至 「 ANY 」 MV 中的物件類型。

## <a name="104850222"></a>1.0.485.0222
發行︰ 2015 年 2 月

**改良功能︰**

- 改良的匯入效能。

**解決的問題︰**

- 密碼同步處理接受使用屬性篩選 cloudFiltered 屬性。 篩選的物件中的密碼同步處理的範圍，就不會出現。
- 在位置拓撲有很多個網域控制站少見的情況下，無法使用密碼同步處理。
- Azure AD/Intune 已啟用 「 已停止-伺服器 」 之後裝置管理匯入從 Azure AD 連接器時。
- 加入來自多個網域，在同一個樹系外部索引的安全性原則 (FSPs) 會產生不明確聯結錯誤。

## <a name="104751202"></a>1.0.475.1202
發行︰ 2014 年 12 月

**新的功能︰**

- 現在支援執行密碼同步處理篩選依據的屬性。 如需詳細資訊，請參閱[篩選密碼同步處理](active-directory-aadconnectsync-configure-filtering.md)。
- AD 屬性 msDS ExternalDirectoryObjectID 會重新寫入。 這加入使用 OAuth2 線上存取兩個 Office 365 應用程式的支援，內部部署 Exchange 混合式部署的信箱。

**固定的升級問題︰**

- 使用伺服器上登入小幫手有較新版本。
- 自訂安裝路徑用來安裝 Azure AD Sync。
- 無效的自訂加入準則封鎖升級。

**其他修正方式︰**

- 修正 Office 專業增強版的範本]。
- 固定的安裝問題所造成的使用者名稱的開頭虛線。
- 固定執行安裝精靈的第二個時間時遺失 sourceAnchor 設定。
- 固定的 ETW 追蹤的密碼同步處理

## <a name="104701023"></a>1.0.470.1023
發行︰ 2014 年 10 月

**新的功能︰**

- 從多個密碼同步處理內部部署 AD Azure ad。
- 所有的 Windows Server 語言的本地化的安裝 ui。

**從 AADSync 1.0 GA 升級**

如果您已安裝的 Azure AD Sync，有一個額外的步驟，您可採取在變更您有任何一個現成的同步處理規則。 您已升級至 1.0.470.1023 後放開，同步處理重複的您修改過的規則。 針對每一個已修改的同步處理規則執行下列動作︰

- 找出您修改過，並記下的 [變更同步處理規則。
- 刪除同步處理的規則。
- 尋找新的同步處理規則建立 Azure AD sync，並重新套用變更。

**AD 帳戶權限**

AD 帳戶必須授與其他的權限，才能從 AD 讀取密碼雜湊。 若要授與的權限的命名 」 複寫目錄變更 」 和 「 複寫目錄變更所有 」。 讀取密碼雜湊所需的兩個權限

## <a name="104190911"></a>1.0.419.0911
發行︰ 2014 年 9 月

**Azure AD Sync 初始發行版本。**

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
