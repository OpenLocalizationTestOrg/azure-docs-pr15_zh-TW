<properties
    pageTitle="自訂屬性對應 |Microsoft Azure"
    description="瞭解 SaaS 應用程式 Azure Active Directory 中的哪些屬性對應修改即可解決您的業務需求。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="customizing-attribute-mappings"></a>自訂屬性對應


Microsoft Azure AD 提供支援使用者佈建協力廠商 SaaS 應用程式，例如 Salesforce、 Google Apps 和其他人。 如果您有使用者佈建 for 第三方 SaaS 應用程式已啟用，Azure 管理入口網站可控制其屬性的值在表單中的設定，稱為 「 屬性對應]。

有一組預先設定 Azure AD 使用者與每一個 SaaS 應用程式使用者物件間的屬性對應。 有些應用程式中管理其他類型的物件，例如群組或連絡人。 <br> 
根據您的業務需求，您可以自訂的預設屬性對應。 這表示，您可以變更或刪除現有的屬性對應或建立新的屬性對應。

在 Azure AD 入口網站中，您可以存取這項功能，即可 SaaS 應用程式] 工具列中的屬性。

> [AZURE.NOTE] 只使用如果您有使用者佈建啟用 SaaS 應用程式的 [**屬性**] 連結。 


![Salesforce][1] 


當您按一下工具列] 的目前設定 SaaS 應用程式的對應清單屬性。

以下螢幕擷取畫面會顯示這個範例︰



![Salesforce][2]  


在上述範例中，您可以看到受管理的 Salesforce 物件的**名字**屬性都會填入的連結**givenName**值 Azure AD 物件。

如果您想要自訂您的屬性對應或如果您想要還原自訂設定回復到預設的設定，，您可以按一下 [底部的應用程式] 工具列中的 [相關] 按鈕。


![Salesforce][3]  


沒有所需的 SaaS 應用程式，才能正常屬性對應。 在 [屬性] 資料表中相關的屬性對應具有**是****必要**的屬性值。 如果需要屬性對應，您無法將它刪除。 在此情況下，**刪除**功能無法使用。

若要修改現有的屬性對應，只要選取 [對應]，然後按一下**編輯**。 如此會開啟的對話方塊頁面，可讓您修改選取的屬性對應。


![編輯屬性對應][4]  



## <a name="understanding-attribute-mapping-types"></a>了解對應類型的屬性


使用屬性對應，您可以控制如何屬性會顯示在第三合作對象 SaaS 應用程式。 有四種不同的對應類型支援︰

- **直接**– 目標屬性會填入的連結物件的屬性值 Azure AD 中。


- **常數**– 目標屬性會填入您指定的特定字串。


- **運算式**的目標屬性會填入根據類似指令碼的運算式的結果。 如需詳細資訊，請參閱[撰寫的 Azure Active Directory 中的屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)。


- **無**-目標屬性左修改。 不過，如果目標屬性是曾空的它會填入您指定的預設值。



除了以下四種基本的屬性對應類型的自訂屬性對應支援**預設**值工作分派的概念。 預設值作業可確保如果沒有這兩個值中 Azure AD，也沒有目標物件上的目標屬性會填入值。

Microsoft Azure AD 提供非常有效率的同步處理程序實作。 在初始化環境中，需要更新的物件會處理同步循環。 更新屬性對應的同步處理循環效能會影響。 這是因為屬性對應設定的更新需要重新評估所有受管理的物件。 因此，則建議的最佳作法讓您最小的屬性對應的連續變更數目。


##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [自動化使用者佈建/取消提供 SaaS 應用程式](active-directory-saas-app-provisioning.md)
- [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [範圍使用者佈建的篩選](active-directory-saas-scoping-filters.md)
- [若要啟用自動提供 [使用者和群組的 Azure Active Directory 從應用程式使用 SCIM](active-directory-scim-provisioning.md)
- [帳戶佈建通知](active-directory-saas-account-provisioning-notifications.md)
- [教學課程如何整合 SaaS 應用程式的清單](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
