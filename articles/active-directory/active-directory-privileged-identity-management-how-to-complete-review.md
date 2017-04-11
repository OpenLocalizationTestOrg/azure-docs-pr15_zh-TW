<properties
   pageTitle="如何完成存取檢閱 |Microsoft Azure"
   description="您開始存取檢閱 Azure AD 有權限的身分識別管理之後，瞭解如何完成它，並檢視結果"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>如何完成 Azure AD 有權限的身分識別管理存取檢閱


有權限的角色系統管理員可以檢視權限的存取一次[啟動安全性檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。 Azure AD 權限的身分識別管理 (PIM) 會自動傳送電子郵件提示若要檢閱其存取權的使用者。 如果使用者未取得電子郵件，您可以傳送這些指示[如何執行安全性檢閱](active-directory-privileged-identity-management-how-to-perform-security-review.md)。

安全性檢閱款，或其自動檢閱完成所有使用者之後，請遵循此文件，以管理檢閱及看到的結果中的步驟進行。

## <a name="manage-security-reviews"></a>管理安全性檢閱

1. 移至 [ [Azure 入口網站](https://portal.azure.com/)，然後選取您的儀表板上的**Azure AD 有權限的身分識別管理**應用程式。
2. 選取儀表板的**Access 檢閱**一節。
3. 選取您想要管理存取檢閱。

在 access 檢閱的詳細資料刀有管理該檢閱數字的選項。

![PIM access 檢閱按鈕的螢幕擷取畫面][1]

### <a name="remind"></a>提醒

如果 access 檢閱設定，以便使用者檢視本身，[**提醒**] 按鈕會送出通知。 

### <a name="stop"></a>停駐點

所有的 access 檢閱結束日期，但是您可以使用 [**停止**] 按鈕來將其最早完成時間。 任何使用者沒有已檢閱者這次，他們無法後停止評論。 已停止之後，您無法重新啟動檢閱。

### <a name="apply"></a>套用

Access 檢閱完畢之後，可能是因為您達到結束日期，或以手動方式，停止 [**套用**] 按鈕實作檢閱的結果。 在檢閱拒絕使用者的存取，如果這是將會移除其角色指派的步驟。  

### <a name="export"></a>匯出

如果您想要手動套用安全性檢閱的結果，您可以匯出評論。 [**匯出**] 按鈕，就會開始下載 CSV 檔案。 您可以管理 Excel 或 CSV 檔案的開啟其他程式中的結果。

### <a name="delete"></a>刪除

如果您不是感興趣的任何進一步的檢閱，請將其刪除。 [**刪除**] 按鈕會移除 PIM 應用程式中的檢閱。

> [AZURE.IMPORTANT] 您不會收到警告，刪除前，因此請確定您想要刪除該檢閱。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
