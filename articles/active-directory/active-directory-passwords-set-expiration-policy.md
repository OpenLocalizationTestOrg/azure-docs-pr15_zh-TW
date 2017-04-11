<properties
    pageTitle="Azure Active Directory 中設定密碼過期原則 |Microsoft Azure"
    description="瞭解如何檢查到期原則和變更單一或大量的 Azure Active directory 密碼的使用者密碼過期"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Azure Active Directory 中設定密碼過期原則

> [AZURE.IMPORTANT] **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。

為 Microsoft 雲端服務的全域管理員，您可以使用 Microsoft Azure Active Directory 模組的 Windows PowerShell 的過期，並且不設定使用者密碼。 您也可以使用 Windows PowerShell cmdlet，若要移除的永久設定，或若要查看哪些使用者的密碼已設定不到過期。 本文提供雲端服務，例如 Microsoft Intune 與 Office 365 依賴 Microsoft Azure Active Directory 的身分識別與目錄服務說明。

  > [AZURE.NOTE] 僅透過目錄同步處理未同步處理的使用者帳戶的密碼可以設定為過期。 如需有關目錄同步處理的詳細資訊，請參閱[目錄同步處理藍圖](https://msdn.microsoft.com/library/azure/hh967642.aspx)中主題的清單。

若要使用 Windows PowerShell cmdlet，您首先必須安裝這些。

## <a name="what-do-you-want-to-do"></a>您想要做什麼？

- [如何檢查密碼到期原則](#how-to-check-expiration-policy-for-a-password)

- [設定為過期的密碼](#set-a-password-to-expire)

- [將密碼設定，讓它不會過期](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>如何檢查密碼到期原則

1.  使用您公司的系統管理員認證的 Windows powershell 連線。

2.  請執行下列其中一項︰

    - 若要查看單一使用者的密碼是否設定為永不過期，請執行下列 cmdlet 使用使用者主要名稱 (UPN) (例如，aprilr@contoso.onmicrosoft.com)或您想要檢查之使用者的使用者識別碼︰`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - 若要查看所有使用者的 「 密碼永久有效 」 設定，請執行下列 cmdlet:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>設定為過期的密碼

1.  使用您公司的系統管理員認證的 Windows powershell 連線。

2.  請執行下列其中一項︰

    - 若要設定一位使用者的密碼，如此便會到期密碼，請執行下列 cmdlet 使用使用者主要名稱 (UPN) 或使用者的使用者識別碼︰`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

    - 若要讓他們便會到期，請在組織中設定的所有使用者的密碼，請使用下列 cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>設定為永不過期的密碼

1. 使用您公司的系統管理員認證的 Windows powershell 連線。

2.  請執行下列其中一項︰

    - 若要設定為永不過期的一位使用者的密碼，請執行下列 cmdlet 使用使用者主要名稱 (UPN) 或使用者的使用者識別碼︰`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

    - 若要設定為永不過期組織中的所有使用者的密碼，執行下列 cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>後續步驟

* **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。
