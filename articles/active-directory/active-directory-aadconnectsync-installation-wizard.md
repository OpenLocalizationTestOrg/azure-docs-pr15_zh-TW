<properties
    pageTitle="Azure AD Connect 同步處理︰ 執行安裝精靈的第二個時間 |Microsoft Azure"
    description="說明安裝精靈的第二個的運作方式執行時的時間。"
    keywords="Azure AD Connect 安裝精靈可讓您設定進行的維修作業設定第二次執行時"
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
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect 同步處理︰ 執行安裝精靈的第二個時間
第一次執行 Azure AD Connect 安裝精靈，逐步引導您進行如何設定您的安裝。 如果您再次執行 [安裝] 精靈，它會提供選項，進行的維修作業。

您可以在名為**Azure AD Connect**的 [開始] 功能表中找到安裝精靈。

![開始] 功能表](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

當您啟動安裝精靈時，您會看到的頁面，其中一個選項︰

![與其他工作清單的頁面](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

如果您有安裝 ADFS Azure AD Connect 使用，您會有更多選項。 您有 ADFS 的其他選項會記錄在[ADFS 管理](active-directory-aadconnect-federation-management.md#ad-fs-management)。

選取其中一項任務，然後按 [**下一步**，繼續]。

> [AZURE.IMPORTANT] 如果已開啟的 [安裝] 精靈，會暫停同步處理引擎中的所有作業。 請確定您已完成設定變更為，關閉安裝精靈。

## <a name="view-current-configuration"></a>檢視目前的設定
這個選項可讓您快速檢視您的目前設定選項。

![使用清單的所有選項和其狀態] 頁面](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

按一下 [**前一節**]，回到上一頁。 如果您選取**結束**時，您可以關閉安裝精靈。

## <a name="customize-synchronization-options"></a>自訂同步處理選項
若要變更同步處理設定為使用此選項。 您會看到自訂設定安裝路徑] 選項的子集合。 即使您最初使用快速安裝，您會看到此選項。

- [新增更多的目錄](active-directory-aadconnect-get-started-custom.md#connect-your-directories)。 移除目錄，請參閱[刪除連接器](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete)。
- [變更網域和 OU 篩選](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)。
- 移除篩選] 群組。
- [變更選用的功能](active-directory-aadconnect-get-started-custom.md#optional-features)。

初始安裝的其他選項無法變更，而且無法使用。 這些選項為︰

- 變更要用於 userPrincipalName 和 sourceAnchor 的屬性。
- 從不同的樹系變更物件的聯結方法。
- 啟用 [群組依據篩選。

## <a name="refresh-directory-schema"></a>重新整理目錄結構描述
如果您已變更的結構描述您的內部部署的其中一種使用這個選項，則 AD DS 樹系。 例如，您可能會安裝 Exchange 或升級至 Windows Server 2012 的結構描述與裝置物件。 在此情況下，您必須指示 Azure AD Connect 從 AD DS 再次讀取結構描述和更新其快取。 這個動作也會重新產生同步處理規則。 如果您新增 Exchange 結構描述，做為範例，請設定會新增 exchange 同步處理規則。

當您選取此選項時，會列出您的設定中的所有目錄。 您可以保留預設設定並重新整理所有樹系或取消選取這些部分。

![使用清單的環境中的所有目錄的頁面](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>設定臨時模式
這個選項可讓您啟用及停用在伺服器上執行的模式。 在 [[作業](active-directory-aadconnectsync-operations.md#staging-mode)找執行模式與使用方式的相關詳細資訊。

如果目前啟用或停用臨時，則會顯示選項︰  
![也會顯示目前狀態的暫存模式的選項](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

若要變更狀態，選取此選項，選取或取消選取核取方塊。  
![也會顯示目前狀態的暫存模式的選項](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>變更使用者登入
這個選項可讓您從密碼同步處理變更為同盟或倒。 您無法變更**未設定**。

如需此選項的詳細資訊，請參閱[使用者登入](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method)。

## <a name="next-steps"></a>後續步驟

- 進一步瞭解使用中[的瞭解程度宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)Azure AD Connect 同步處理設定模型。

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
