<properties
    pageTitle="自動化佈建中 Azure AD SaaS 應用程式使用者 |Microsoft Azure"
    description="簡介如何使用 Azure AD 自動佈建，就能取消佈建，並持續更新跨多個協力廠商 SaaS 應用程式的 [使用者帳戶。"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>自動化佈建和取消與 Azure Active Directory SaaS 應用程式的使用者

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>什麼是自動化使用者佈建 SaaS 應用程式？

Azure Active Directory (Azure AD) 可讓您自動建立、 維護和移除的 Dropbox、 Salesforce、 ServiceNow，等雲端 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 應用程式中的使用者身分識別。

**以下是什麼這項功能可讓您執行的一些範例︰**

- 時自動建立新的帳戶正確 SaaS 應用程式中的人員他們加入您的小組。
- 當人員難免離開小組會自動停用 SaaS 應用程式的帳戶。
- 確認您 SaaS 應用程式中的識別會保持最新狀態根據目錄中的變更。
- 佈建非使用者物件，例如 [群組]，以支援這些 SaaS 應用程式。

**自動化的使用者佈建也包含下列功能︰**

- 以符合現有的身分識別 Azure AD 之間的功能及 SaaS 應用程式。
- 說明 Azure AD 的自訂選項符合您的組織目前所用的 SaaS 應用程式的目前設定。
- 針對佈建錯誤的選用的電子郵件通知。
- 報告及活動記錄，以協助監控與疑難排解。

##<a name="why-use-automated-provisioning"></a>為什麼要使用自動化佈建？

使用此功能的一些常見的升級包括︰

- 若要避免成本、 效率，並手動佈建程序與相關聯的人力錯誤。
- 若要立即移除使用者的身分識別從金鑰 SaaS 應用程式，離開組織保護您的組織。
- 若要將特定 SaaS 應用程式，輕鬆匯入大量使用者數目。
- 若要將很享受個佈建方案執行相同的應用程式存取原則您定義的 Azure AD 單一登入的人員。

##<a name="frequently-asked-questions"></a>常見問題集

**頻率會 Azure AD 撰寫目錄變更 SaaS 應用程式？**

Azure AD 檢查有變更每個 5 到 10 分鐘。 如果傳回 SaaS 應用程式 （例如無效的管理員認證的大小寫） 的數個錯誤，Azure AD 會逐漸減慢其頻率，以最每日修正錯誤之前的一次。

**將會多久佈建使用者？**

累加變更幾乎立即發生，但如果您嘗試佈建大部分的目錄，然後依存的使用者及群組，您有數。 小型目錄只需要幾個幾分鐘、 中型目錄可能需要幾分鐘，及很大的目錄可能需要幾個小時。

**我要如何追蹤目前佈建工作的進度？**

您可以檢閱您的目錄中的 [報表] 區段下的帳戶佈建報告。 另一個選項是造訪 SaaS 應用程式的您在提供，並查看頁面底部附近的 「 整合狀態] 區段下的 [儀表板] 索引標籤。

**如何知道使用者無法取得佈建後正確？**

結尾的佈建設定精靈有是其中一個選項以訂閱的佈建失敗電子郵件通知。 您也可以查看佈建錯誤報告，若要查看哪些使用者無法佈建和原因。

**可以 Azure AD 回覆變更從 SaaS 應用程式目錄嗎？**

大部分的 SaaS 應用程式，佈建只輸出，這表示使用者從目錄寫入到應用程式，並從應用程式的變更無法重新寫入的目錄。 [工作日](https://msdn.microsoft.com/library/azure/dn762434.aspx)，不過，佈建只輸入，這表示的使用者匯入 Workday，從目錄及同樣地，在目錄中的變更執行寫入回 Workday 到的。

**如何送出工程小組的意見反應？**

請與我們連絡透過[Azure Active Directory 意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/)。

##<a name="how-does-automated-provisioning-work"></a>自動化佈建的運作方式？

Azure AD 佈建 SaaS 應用程式使用者份連接到佈建每個應用程式的廠商所提供的結束點。 這些端點允許 Azure AD 以程式設計方式建立、 更新及移除使用者]。 以下是不同的步驟自動化佈建 Azure AD 所需的簡介。

1. 當您啟用佈建第一次應用程式時，請執行下列動作︰
 - Azure AD 會嘗試符合 SaaS 應用程式中使用其對應的身分識別，在目錄中任何現有使用者。 當使用者相符時，它們會*不*會自動啟用單一登入。 若要讓使用者可存取應用程式，必須明確指派的應用程式中 Azure AD，直接或透過群組成員資格。
 - 如果您已經指定哪些使用者應指派給應用程式，而且 Azure AD 無法找到這些使用者現有的帳戶，Azure AD 會佈建新帳戶的應用程式中。
2. 一旦完成初始同步處理後上所述，Azure AD 會檢查每 10 分鐘的下列變更︰
 - 如果已指派的應用程式的新使用者 （直接或透過群組成員），就能佈建後 SaaS 應用程式中的新帳戶。
 - 如果已移除使用者的存取權，然後將標記其帳戶 SaaS 應用程式中的，為停用 （刪除的使用者會永遠不完整，這可以讓您避免資料遺失發生錯誤時）。
 - 如果最近指派給應用程式使用者，他們 SaaS 應用程式中已經有帳戶，該帳戶會標示為已啟用，與特定使用者屬性可能會更新，如果他們是過時比較目錄。
 - 如果已在目錄中變更使用者的資訊 （例如電話號碼、 辦公室位置等），然後該資訊會也會更新 SaaS 應用程式中。

如需有關如何之間 Azure AD 對應屬性和 SaaS 應用程式，請參閱在[自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md)的文件。

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>支援自動化的使用者提供的應用程式清單

按一下 [應用程式，請參閱瞭解如何設定，自動化佈建教學課程︰

- [方塊](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [商務用 Dropbox](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google 應用程式](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Salesforce 沙箱](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [workday](http://go.microsoft.com/fwlink/?LinkId=690250)（輸入佈建）

應用程式支援自動化的使用者提供的順序，它必須先提供必要的結束點，以允許外部的程式，便可自動化建立、 維護和移除的使用者。 因此，並非所有 SaaS 應用程式都是相容具有此功能。 應用程式支援此 Azure AD 工程團隊然後會建置那些應用程式，佈建的連接器，這項工作目前和未來的客戶的需求來選擇優先將。

若要連絡 Azure AD 工程團隊要求其他應用程式提供支援，請提交的郵件，透過[Azure Active Directory 意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/)。

##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [自訂使用者提供的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
- [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [範圍使用者佈建的篩選](active-directory-saas-scoping-filters.md)
- [若要啟用自動提供 [使用者和群組的 Azure Active Directory 從應用程式使用 SCIM](active-directory-scim-provisioning.md)
- [帳戶佈建通知](active-directory-saas-account-provisioning-notifications.md)
- [教學課程如何整合 SaaS 應用程式的清單](active-directory-saas-tutorial-list.md)
