<properties
    pageTitle="屬性型的應用程式佈建與設定的範圍篩選 |Microsoft Azure"
    description="瞭解如何使用範圍篩選防止支援自動化的使用者提供的實際佈建如果物件無法滿足您業務需求的應用程式中的物件。"
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


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>屬性型的應用程式佈建與設定的範圍篩選

本節的目標是以說明如何使用範圍的篩選來定義屬性為基礎的規則決定哪些使用者會佈建後的應用程式。





## <a name="clauses-and-scope-groups"></a>子句和範圍群組


![設定篩選的範圍][1] 




一或多個**範圍群組**，每個按住一或多個**子句**定義範圍篩選。 若要查看特定範圍群組子句，請按一下群組名稱左邊的箭號來展開。

一個**子句**會決定哪些使用者可以透過 [範圍] 篩選傳遞計算每個使用者的屬性。 例如，您可能會要求使用者的 「 狀態 」 屬性等於紐約 」，這表示只有您紐約的使用者，會佈建在應用程式中的一個子句。

![範圍的群組名稱][2] 



每個**範圍] 群組**開頭一個強制**子句**的螢幕擷取畫面上方所示。 此子句只會指出，必須先為使用者指派至應用程式您範圍的篩選評估之前。 無法刪除或修改此子句。

您可以按下適當的按鈕來新增新子句或新範圍群組。 您可以藉由編輯其**範圍群組名稱**屬性給每個範圍] 群組的名稱。





## <a name="how-scoping-filters-are-evaluated"></a>如何設定的範圍篩選的評估

期間佈建，我們要測試的每位指派的使用者針對您的範圍篩選來決定該使用者是否值得存取應用程式。 您可以將每個子句所必須傳遞的使用者，以避免快速篩選出的測試。 

如果您有多個定義的範圍群組時，每位使用者必須至少一個傳遞才能存取應用程式。 在每個範圍] 群組中，不過，使用者必須通過每個單一子句以便傳遞的特定範圍] 群組。 

換句話說，您可以將範圍群組所要放在一起，或您認為子句中所與會放在一起。例如，請考慮下列範圍的篩選︰


![範圍的群組名稱][2]  


根據此範圍的篩選，使用者必須符合下列條件，才能佈建︰

1. 他們必須被指派到應用程式。

2. 他們必須在工程部門工作

3. 必須工作舊金山和加拿大。


##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [自動化佈建和取消 SaaS 應用程式的使用者](active-directory-saas-app-provisioning.md)
- [自訂使用者提供的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
- [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [帳戶佈建通知](active-directory-saas-account-provisioning-notifications.md)
- [若要啟用自動提供 [使用者和群組的 Azure Active Directory 從應用程式使用 SCIM](active-directory-scim-provisioning.md)
- [教學課程如何整合 SaaS 應用程式的清單](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png
