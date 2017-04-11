<properties
   pageTitle="Azure Active Directory 報告-預覽 |Microsoft Azure"
   description="列出各種可用報表的 Azure Active Directory 預覽"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory 報告-預覽

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-reporting-azure-portal.md)
- [Azure 傳統入口網站](active-directory-reporting-guide.md)

*這份文件是[Azure Active Directory 報告指南](active-directory-reporting-guide.md)的一部分。*

使用報告 Azure Active Directory 預覽中，您可以取得您需要判斷您的環境正在進行的作業的所有資訊。 [什麼是在預覽中？](active-directory-preview-explainer.md)

有兩個主要區域報告︰

- **登入活動**– 受管理的應用程式和使用者登入活動的使用狀況資訊

- **稽核記錄**系統使用者及群組管理、 受管理的應用程式與目錄的活動相關的活動資訊

根據您要尋找的資料範圍，您可以藉由按一下 [ [Azure 入口網站](https://portal.azure.com)中的 [服務] 清單中的 [**使用者和群組**] 或 [**企業應用程式**存取這些報表。

## <a name="sign-in-activities"></a>登入活動

### <a name="user-sign-in-activities"></a>使用者登入活動

報表使用者登入所提供的資訊，您尋找問題的答案執行下列作業︰

- 什麼是使用者的登入模式？
- 多少使用者有使用者登入超過一週？
- 這些登增益集的狀態為何？

此資料至您進入點是使用者登入圖表**使用者和群組**] 下的 [**概觀**] 區段中。

 ![報告](./media/active-directory-reporting-azure-portal/05.png "報告")

使用者登入圖表會顯示每週的彙總的登集給定的時間週期中的所有使用者。 期間的預設值為 30 天。

![報告](./media/active-directory-reporting-azure-portal/02.png "報告")

當您按一下登入圖表] 中的某一天時，您會收到的登入活動的詳細的清單。

![報告](./media/active-directory-reporting-azure-portal/03.png "報告")

在 [登入活動] 清單中的每個資料列可讓您選取登入的相關詳細的資訊，例如︰

- 誰具有登入？

- 相關的 UPN 是什麼？

- 哪些應用程式的登入的目標？

- 登入的 IP 位址是什麼？

- 登入的狀態為何？

### <a name="usage-of-managed-applications"></a>管理應用程式的使用方式

您登入的資料以應用程式檢視中，您可以使用回答問題例如︰

- 誰會使用我的應用程式？

- 什麼是您組織中的前 3 應用程式？

- 我最近有導入應用程式。 如何將其做？


此資料至您進入點是您組織內 [**概觀**] 區段底下**企業應用程式**] 中的最後一個 30 天報表中的前 3 應用程式。

 ![報告](./media/active-directory-reporting-azure-portal/06.png "報告")


應用程式使用圖形每週的彙總登給定的時間週期中的前 3 應用程式集。 期間的預設值為 30 天。

![報告](./media/active-directory-reporting-azure-portal/78.png "報告")

如果您想要您可以將焦點放在特定的應用程式。

![報告](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "報告")


當您按一下應用程式的使用狀況圖形中的某一天時，您會收到的登入活動的詳細的清單。


![報告](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "報告")



[**簽署增益集**] 選項可讓您所有的登入事件的完整概觀應用程式。

![報告](./media/active-directory-reporting-azure-portal/85.png "報告")

藉由使用 [欄位選擇]，您可以選取您想要顯示的資料欄位。

![報告](./media/active-directory-reporting-azure-portal/column_chooser.png "報告")



### <a name="filtering-sign-ins"></a>篩選符號增益集

您可以篩選登增益集，以限制顯示的資料量的時間間隔。

![報告](./media/active-directory-reporting-azure-portal/927.png "報告")


若要篩選的項目之登入活動的另一個方法是要搜尋特定的項目。
搜尋方法可讓您設定範圍您簽署增益集周圍特定**使用者**、**群組**或**應用程式**。


![報告](./media/active-directory-reporting-azure-portal/84.png "報告")

## <a name="audit-logs"></a>稽核記錄

Azure Active Directory 中的稽核記錄檔提供的相容性系統活動的記錄。

有三個主要的類別的稽核 Azure 入口網站中的相關的活動︰

- 使用者和群組   

- 應用程式

- 目錄   


稽核報告活動的完整清單，請參閱[稽核報告的事件清單](active-directory-reporting-audit-events.md#list-of-audit-report-events)。


您進入點到所有的稽核資料是在 [**活動**] 區段的**Azure Active Directory**中的**稽核記錄**。


![稽核](./media/active-directory-reporting-azure-portal/61.png "稽核")


稽核記錄具有清單檢視顯示的動作項目 （使用者） （內容） 的活動和目標。


![稽核](./media/active-directory-reporting-azure-portal/345.png "稽核")


按一下 [清單] 檢視中的項目，您可以取得更多詳細資料。

![稽核](./media/active-directory-reporting-azure-portal/873.png "稽核")




### <a name="users-and-groups-audit-logs"></a>使用者和群組稽核記錄


使用者及群組為基礎的稽核報告，您可以取得例如問題的答案︰

- 內容類型的更新已套用使用者？

- 變更多少使用者？

- 多少的密碼已變更？

- 具有管理員做什麼的目錄中？

- 什麼是已新增群組？

- 是否有群組的成員資格變更？

- 已變更] 群組的擁有者嗎？

- 授權已指派給群組或使用者？


如果您只是要檢閱使用者和群組相關的稽核資料，您可以找到篩選的檢視**稽核記錄**在 [**使用者**和群組的 [**活動**] 區段中。


![稽核](./media/active-directory-reporting-azure-portal/93.png "稽核")


### <a name="application-audit-logs"></a>應用程式稽核記錄

使用應用程式為基礎的稽核報告，您可以取得例如問題的答案︰

- 什麼是新增或更新的應用程式？

- 什麼是已移除的應用程式？

- 已變更的應用程式的服務原則

- 已變更的應用程式名稱？

- 誰可以提供同意應用程式？


如果您只是要檢閱稽核資料的相關應用程式，您可以找到篩選的檢視**稽核記錄**下的 [**企業應用程式**的 [**活動**] 區段中。


![稽核](./media/active-directory-reporting-azure-portal/134.png "稽核")


### <a name="filtering-audit-logs"></a>篩選的稽核記錄

您可以篩選稽核報告的時間間隔，以限制顯示的資料量。

![稽核](./media/active-directory-reporting-azure-portal/324.png "稽核")

若要篩選的稽核記錄的項目另一種方法可搜尋特定的項目。

![稽核](./media/active-directory-reporting-azure-portal/237.png "稽核")

## <a name="next-steps"></a>後續步驟

請參閱[報表指南的 Azure Active Directory](active-directory-reporting-guide.md)。
