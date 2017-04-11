<properties
   pageTitle="如何設定的安全性警告 |Microsoft Azure"
   description="瞭解如何設定 Azure 權限的身分識別管理擴充的安全性警告。"
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
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>如何設定 Azure AD 有權限的身分識別管理的安全性警告

## <a name="security-alerts"></a>安全性警告
您的環境中可疑或不安全的活動時，azure 權限的身分識別管理 (PIM) 會產生提醒。 觸發通知時，會出現在 [PIM 儀表板。 選取您要檢視會列出使用者或角色觸發提醒通知。

![PIM 儀表板安全性警訊螢幕擷取畫面][1]



| 提醒 | 觸發程序 | 建議 |
| ----- | ------- | -------------- |
| **外部 PIM 指派角色** | 系統管理員已永久指派給角色，外部 PIM 介面。 | 檢閱新的角色指派。 因為其他服務只可以指派永久性的系統管理員，請視需要變更其符合資格的工作分派。 |
| **角色會被啟用太過頻繁** | 在設定所允許的時間內沒有太多重新角色相同。 | 連絡使用者請參閱為什麼他們已經啟動角色有這麼多的時間。 也許時間限制為太的簡稱即可完成其工作，或可能他們使用指令碼自動啟動角色。 |
| **角色不需要啟動的多重因素驗證** | 有不 MFA 設定中啟用的角色。 | 我們需要 MFA 的最高特殊權限的角色，但強烈建議您允許的各種角色啟動 MFA。 |
| **管理員不使用其權限的角色** | 有資格最近尚未啟動其角色的系統管理員。 | 啟動 access 檢閱，來判斷不再需要存取權的使用者。 |
| **有太多的全域管理員** | 還有更多全域管理員超過建議。 | 如果您有大量的全域管理員，則可能使用者會取得比所需的權限。 將使用者移至較低權限的角色，或者讓將其中一些有資格申請而不是該角色的永久指派。 |

## <a name="configure-security-alert-settings"></a>設定通知的安全性設定

您可以自訂部分中使用您的環境及安全性目標 PIM 的安全性警告。 遵循達到設定刀下列步驟︰

1. 登入[Azure 入口網站](https://portal.azure.com/)，然後選擇 [ **Azure AD 有權限的身分識別管理**從儀表板。
2. 選取 [**受管理的權限的角色** > **設定** > **通知設定**。

    ![瀏覽至安全性通知設定][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>「 角色會被啟用太過頻繁 」 通知

如果使用者啟動的相同的權限的角色多次在指定期間內，觸發程序此通知。 您可以設定的時間期間和啟動數目。

- **啟動更新時間範圍**︰ 指定在天、 小時、 分鐘和第二個您要用來追蹤可疑續訂的期間。

- **啟動續訂數目**︰ 指定啟動，從 2 到 100，考慮的提醒中，在您選擇的時間範圍內的數目。 您可以變更此設定移動滑桿，或在 [文字] 方塊中輸入數字。


### <a name="there-are-too-many-global-administrators-alert"></a>「 有太多的全域管理員 」 通知

如果符合兩個不同的準則，且您可以在其中設定 PIM 觸發程序此通知。 首先，您必須達到特定臨界值的全域管理員。 第二，您的總角色指派的特定百分比必須是全域管理員。 如果您只符合這些度量單位，其中一項不會出現警示。  

- **最小值數字的全域管理員**︰ 指定的全域管理員、 從 2 到 100，請考慮不安全的量。

- **全域管理員的百分比**︰ 指定全域系統管理員的系統管理員的百分比，0%到 100%，這是您的環境中不安全。

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>「 系統管理員不使用其權限的角色 」 通知

如果使用者在特定時間長度，但不啟動角色，觸發程序此通知。

- **天數**︰ 指定的天數，從 0 到 100 的使用者可以移但不啟動角色。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
