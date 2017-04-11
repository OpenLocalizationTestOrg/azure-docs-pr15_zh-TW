<properties
    pageTitle="使用者佈建管理企業應用程式中的 Azure Active Directory 預覽 |Microsoft Azure"
    description="瞭解如何管理使用者帳戶佈建企業應用程式使用 Azure Active Directory 預覽"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>預覽︰ 管理使用者帳戶提供的新的 Azure 入口網站的企業應用程式

本文將說明如何使用[Azure 入口網站](https://portal.azure.com)管理自動的使用者帳戶佈建和不支援的應用程式的支援，特別是一種是已新增的[Azure Active Directory 應用程式組件庫](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)的 「 主要 」 類別。 公用預覽，目前位於此新 Azure 入口網站中的管理體驗，本文將說明的新功能，以及在預覽期間位置中的幾個暫時限制。 [什麼是在預覽中？](active-directory-preview-explainer.md)

若要瞭解自動的使用者帳戶佈建和運作方式的詳細資訊，請參閱[自動化使用者佈建和 Deprovisioning 與 Azure Active Directory SaaS 應用程式](active-directory-saas-app-provisioning.md)。

##<a name="finding-your-apps-in-the-new-portal"></a>在新的入口網站尋找您的應用程式

迄年 9 月 2016年已設定的單一的所有應用程式登入在目錄中，使用[Azure Active Directory 應用程式庫](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)內[Azure 傳統入口網站](https://manage.windowsazure.com)目錄系統管理員，可以現在檢視和管理新的 Azure 入口網站。

可以在新 Azure 入口網站，可以透過在左側的導覽區域中的 [**更多服務**] 功能表的 [**企業應用程式**] 區段中找到這些應用程式。 企業應用程式的應用程式的部署，並使用您的組織內部的使用者。

![企業應用程式刀][0]

選取左側的 [**所有應用程式**] 連結會顯示所有已設定，包括已從圖庫新增的應用程式的應用程式的清單。 選取應用程式會載入資源刀，該應用程式，在該應用程式，可以檢視報表，且可管理各種不同的設定。

選取**提供**的左側，可以管理佈建設定的使用者帳戶。

![應用程式資源刀][1]


##<a name="provisioning-modes"></a>佈建模式

**提供**刀**模式**] 功能表中顯示的企業應用程式支援哪些佈建模式，請從開始，並讓其進行設定。 可用的選項包括︰

* **自動**為此選項會出現 Azure AD 支援佈建自動 API 型及/或不支援的使用者帳戶，以便此應用程式。 選取此模式下會顯示引導系統管理員設定連線到應用程式的使用者管理 API Azure AD、 建立帳戶對應和定義之間 Azure AD 的使用者帳戶資料應該如何流程的工作流程進行介面和應用程式，和管理 Azure AD 佈建服務。

* 如果 Azure AD 不支援此應用程式的使用者帳戶的自動佈建顯示**手動**這個選項。 這個選項，就表示您必須使用外部的程序，根據該應用程式 （可包含 SAML Just-In-Time 佈建） 所提供的使用者管理與佈建功能來管理使用者帳戶記錄儲存在應用程式。


##<a name="configuring-automatic-user-account-provisioning"></a>設定自動的使用者帳戶佈建

選取 [**自動**] 選項會顯示四個區段中的除數畫面︰

###<a name="admin-credentials"></a>管理員認證
這是認證所需的 Azure AD 連線至應用程式的使用者管理 API 所輸入的位置。 輸入所需視應用程式而異。 若要瞭解的認證類型與特定的應用程式的需求，請參閱[設定教學課程中的特定應用程式](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning)。

選取 [**測試連接**] 按鈕可讓您測試認證由 Azure AD 嘗試連線到應用程式的佈建應用程式使用所提供的認證。

###<a name="mappings"></a>對應
這是系統管理員可以檢視及編輯哪些使用者屬性間流動 Azure AD 的位置和使用者帳戶正在佈建後，或更新的目標應用程式。

有一組預先設定 Azure AD 使用者物件與每一個 SaaS 應用程式使用者物件之間的對應。 有些應用程式中管理其他類型的物件，例如群組或連絡人。 選取其中一個這些對應表格會顯示在右側，以便能檢視和自訂對應編輯器。

![應用程式資源刀][2]

支援的自訂作業期間的第一個預覽包括︰

* 啟用及停用特定的物件，例如 SaaS 應用程式的使用者物件 Azure AD 使用者物件的對應。

* 編輯哪些屬性的應用程式使用者物件流程從 Azure AD 使用者物件。 如需屬性對應的詳細資訊，請參閱[瞭解屬性對應類型](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types)。

* 篩選 Azure AD 應該執行目標應用程式，也就是 Azure 入口網站中的新功能佈建動作。 而不是由 Azure AD 完整的同步處理物件，您可以限制執行的動作。 例如，只選取 [**更新**，Azure AD 只更新現有的使用者帳戶在應用程式，並不會建立新的]。 只選取 [**建立**]，Azure，只會建立新的使用者帳戶，但並不會更新現有的。 此功能可讓系統管理員 」 建立不同的帳戶建立的對應及更新工作流程。 若要建立多個對應每個應用程式的完整功能被規劃稍後的預覽期間。

###<a name="settings"></a>設定
此區段中，可讓啟動和停止 Azure AD 佈建服務所選取的應用程式，以及您也可以清除佈建的快取並重新啟動服務的系統管理員。

如果要啟用佈建應用程式的第一次，開啟服務**佈建狀態**變更為**上**。 這會使 Azure AD 佈建服務進行初始同步處理，它會讀取指定 [**使用者和群組**] 區段中的使用者、 查詢的目標應用程式，然後執行 [佈建 Azure AD**對應**區段中定義的動作。 在此程序，提供的服務會儲存它由管理，哪些使用者帳戶的相關的快取的資料，因此非受管理的帳戶，永遠不會在工作分派的範圍中的目標應用程式內不會受到不支援的作業。 初始同步處理後，提供的服務會自動同步處理使用者和群組物件上 10 分鐘的時間間隔。

**佈建狀態**變更為 [**關閉**時，只會暫停佈建的服務。 在這個狀態，Azure 不建立、 更新或移除應用程式中的任何使用者或群組物件。 回到 [開啟變更狀態會使服務，選擇 [停止的地方。

選取 [**清除目前狀態，並重新啟動同步處理**] 核取方塊，然後儲存停止佈建的服務，傾印 Azure AD 哪些帳戶的相關的快取的資料管理、 重新啟動服務及執行初始同步處理一次。 這個選項可讓系統管理員重新開始提供部署程序。

###<a name="synchronization-details"></a>同步處理的詳細資料
本節提供加法佈建服務，包括佈建服務已針對應用程式，並多少使用者和群組物件的受管理的第一個和最後一個時間的作業的詳細資料。

連結會提供**提供活動報表**，其中提供記錄的所有使用者群組建立、 更新，及移除之間 Azure AD 及目標應用程式，並**提供錯誤報告**提供更詳細的錯誤訊息的使用者和群組物件無法讀取，建立，更新或移除。 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
