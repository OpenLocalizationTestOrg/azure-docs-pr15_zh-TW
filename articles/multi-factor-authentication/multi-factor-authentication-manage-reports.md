<properties
    pageTitle="Azure 多重因素驗證報表"
    description="以下說明如何使用 Azure 多重因素驗證功能-報表。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="reports-in-azure-multi-factor-authentication"></a>Azure 多重因素驗證中的報表

Azure 多重因素驗證提供可讓您與您組織的許多報表。 透過多重因素驗證管理網站，您必須具備 Azure MFA 提供者，或 Azure MFA、 Azure AD 進階版或企業版行動性套件的授權可以存取這些報表。 以下是可用的報表的清單。

您可以透過 Azure 管理網站存取報表。

名稱| 描述
:------------- | :------------- |
使用方式 | 使用狀況上整體的使用方式與使用者摘要使用者詳細資料的報表顯示資訊。
伺服器狀態|此報表顯示多重因素驗證伺服器與您的帳戶相關聯的狀態。
封鎖的使用者歷程記錄|這些報表顯示封鎖或解除封鎖使用者要求的歷程記錄。
略過的使用者歷程記錄|若要略過多因素驗證，使用者的電話號碼邀請的歷程記錄] 會顯示。
網路詐騙提醒|顯示歷程記錄的網路詐騙通知提交期間所指定的日期範圍。
佇列中|清單報表佇列處理和他們的狀態。 完成報表時提供下載或檢視的報表的連結。

## <a name="to-view-reports"></a>若要檢視的報表

1.  登入 http://azure.microsoft.com
2.  在左側，選取 [Active Directory]。
3.  選取下列選項之一︰
    - **選項 1**︰ 按一下 [多重因素驗證提供者] 索引標籤。 選取您的 MFA 提供者，然後按一下 [底部的 [管理] 按鈕。
    - **選項 2**︰ 選取您的目錄，然後按一下 [設定] 索引標籤。 在 [多重因素驗證] 區段中，選取 [管理服務設定]。 在 [MFA 服務設定] 頁面底部，按一下 [移至入口網站的連結。
4.  在 Azure 多重因素驗證管理入口網站，您會看到 [檢視報表] 區段，在左側導覽畫面中。 從這裡開始，您可以選取上面所述的報表。

<center>![雲端](./media/multi-factor-authentication-manage-reports/report.png)</center>


**其他資源**

* [使用者](./end-user/multi-factor-authentication-end-user.md)
* [Azure MSDN 上的多重因素驗證](https://msdn.microsoft.com/library/azure/dn249471.aspx)
