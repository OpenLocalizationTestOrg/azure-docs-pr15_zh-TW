<properties
   pageTitle="Azure AD Connect 同步處理︰ 避免意外刪除 |Microsoft Azure"
   description="本主題說明 Azure AD Connect 的防止不小心刪除 （避免意外刪除） 功能。"
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
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect 同步處理︰ 避免意外刪除
本主題說明 Azure AD Connect 的防止意外刪除 （避免意外刪除） 功能。

當安裝 Azure AD Connect，避免意外是預設為啟用刪除，並將其設為不允許使用多個 500 會匯出。 此功能被設計來保護您免於意外的設定變更，並變更您的內部部署目錄會影響許多使用者及其他物件。

當您看到包含許多刪除常見的案例︰

- 若要[篩選](active-directory-aadconnectsync-configure-filtering.md)的整個[OU](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering)或[網域](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering)哪裡未選取的變更。
- 刪除 OU 中的所有物件。
- OU 會重新命名，以便在筆記本內所有的物件會被視為不同步處理的範圍內。

可以變更 500 個物件的預設值，使用 PowerShell 使用`Enable-ADSyncExportDeletionThreshold`。 您應該設定此值，以符合貴組織的大小。 同步處理排程器執行每 30 分鐘，因為值是在 30 分鐘內所看到的刪除數目。

如果有太多刪除分段匯出至 Azure AD，然後匯出停駐點，而且您收到一封電子郵件，像這樣︰

![避免意外刪除電子郵件](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *認識 （技術連絡人）。在 （時間） 的身分識別同步處理服務偵測到的刪除數超出 （組織的名稱） 的設定的刪除臨界值。執行此身分識別同步刪除送給總 (number) 的物件。這符合或超出 (number) 物件的設定的刪除臨界值。我們需要您提供確認我們會繼續進行之前，應該處理這些刪除。請參閱禁止意外刪除，如需此電子郵件訊息中列出的錯誤。*

您也可以查看狀態`stopped-deletion-threshold-exceeded`當您檢視**同步處理服務管理員**使用者介面中匯出設定檔。
![避免意外刪除同步處理服務管理員使用者介面](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

如果這是預期，然後調查並進行修正。 若要查看即將遭到刪除的物件，請執行下列動作︰

1. 從 [開始] 功能表開始**同步處理服務**。
2. 移至**連接線**。
3. 選取連接器與**Azure Active Directory**類型。
4. 按一下右邊的**動作**] 底下選取 [**搜尋連接器空間**]。
5. 在**範圍**底下的快顯視窗，選取 [**中斷連線後，**然後選擇 [過去的時間。 按一下 [**搜尋**]。 本頁面提供即將遭到刪除的所有物件的檢視。 按一下每個項目，您可以取得其他物件的相關資訊。 您也可以按一下以新增顯示格線中的其他屬性**設定欄**。

![搜尋連接器空間](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

如果需要全部刪除，然後執行下列動作︰

1. 若要暫時停用此保護，讓這些刪除瀏覽、 PowerShell 指令程式︰ `Disable-ADSyncExportDeletionThreshold`。 提供 Azure AD 全域管理員帳戶及密碼。
![認證](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. 與 Azure Active Directory 連接器仍處於選取狀態，請選取 [**執行**的動作，然後選取 [**匯出**。
3. 若要重新啟用保護，請執行 [PowerShell 指令程式︰ `Enable-ADSyncExportDeletionThreshold`。

## <a name="next-steps"></a>後續步驟

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
