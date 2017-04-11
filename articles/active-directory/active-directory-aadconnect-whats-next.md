<properties
    pageTitle="Azure AD Connect︰ 後續步驟，以及如何管理 Azure AD Connect |Microsoft Azure"
    description="瞭解如何針對 Azure AD Connect 延伸預設設定與操作工作。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>接下來的步驟，以及如何管理 Azure AD Connect
以下被進階操作讓您自訂 Azure Active Directory 連線符合貴組織的需求及需求的主題。  

## <a name="add-additional-sync-administrators"></a>新增額外的同步處理管理員
依預設只是安裝及本機系統管理員的使用者將能夠管理已安裝的同步處理引擎。 其他人可以存取及管理同步處理引擎，找出群組命名 ADSyncAdmins 本機伺服器上並將其新增至 [此群組。

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Azure AD 進階版和企業版行動使用者指派授權

現在，您的使用者已同步處理至雲端，您必須將他們指派授權，讓他們可以上手使用雲端應用程式，例如 Office 365。

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>若要指派的 Azure AD 進階版或企業版行動性套件授權
--------------------------------------------------------------------------------
1. 登入 Azure 入口網站以系統管理員。
2. 在左側，選取 [ **Active Directory**]。
3. 在 Active Directory 頁面上，按兩下含有您想要啟用使用者的目錄。
4. 在 [目錄] 頁面頂端，選取 [**授權**]。
5. 在 [授權] 頁面中，選取 Active Directory 進階版或企業版行動性套件，然後再按一下**指派**。
6. 在對話方塊中，選取您想要指派授權的使用者，然後按一下 [核取記號] 圖示，以儲存變更。


## <a name="verifying-the-scheduled-synchronization-task"></a>驗證排定的同步處理工作
如果您想要查看您可以藉由 Azure 入口網站中的同步處理狀態。

### <a name="to-verify-the-scheduled-synchronization-task"></a>若要確認排定的同步處理工作
--------------------------------------------------------------------------------
1. 登入 Azure 入口網站以系統管理員。
2. 在左側，選取 [ **Active Directory**]。
3. 在 Active Directory 頁面上，按兩下含有您想要啟用使用者的目錄。
4. 在 [目錄] 頁面的頂端，選取 [**目錄整合**。
5. 在整合與本機 active directory 記事上次同步處理。

<center>![雲端](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>開始排程的同步處理工作
如果您需要執行同步處理工作您可以再次執行 Azure AD Connect 精靈。  您必須提供 Azure AD 認證。  在精靈中，選取**自訂同步處理選項**任務，按一下 [下的一步執行精靈。 結束時，請確定已核取 [**開始同步處理程序完成初始設定為**] 方塊。

<center>![雲端](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

如需詳細資訊，在 Azure AD Connect 同步處理︰ 排程器，請參閱[Azure AD 連線排程器](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Azure AD Connect 中可用的其他工作
您初始安裝後的 Azure AD Connect，您可以隨時重新啟動精靈從 Azure AD Connect 開始頁面或桌面捷徑。  您會發現，通過精靈提供的其他工作表單中某些新選項。  

下表提供每個摘要這些工作與簡短描述。

![加入規則](./media/active-directory-aadconnect-whats-next/addtasks.png)


其他的工作 | 描述
------------- | ------------- |
檢視所選的案例  |可讓您檢視您的目前 Azure AD Connect 解決方案。  這包含一般設定、 同步處理的目錄，同步處理設定等。
自訂同步處理選項 | 可讓您變更目前的設定，包括新增額外的 Active Directory 樹系設定或啟用同步處理選項，例如使用者、 群組、 密碼回寫功能或裝置。
啟動臨時模式 |  這個選項可讓您稍後會同步處理的階段資訊，但不是會匯出至 Azure AD 或 Active Directory。  這個選項可讓您之前發生預覽同步處理。

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
