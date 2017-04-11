<properties
    pageTitle="Azure Active Directory 報表保留原則 |Microsoft Azure"
    description="在您的 Azure Active Directory 中的報表資料的保留原則"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 報表保留原則

*這份文件是[Azure Active Directory 報告指南](active-directory-reporting-guide.md)的一部分。*

Azure Active Directory (Azure AD) 報表會保留資料的天數，在這裡指定數目。

報表                                                  | 描述
------------------------------------------------------- | -----------
簽署增益集不明來源                           | 30 天
多個失敗之後登增益集。                        | 30 天
從多個地區登增益集。                      | 30 天
從 [IP 位址可疑的活動登增益集。     | 30 天
從可能受感染的裝置登增益集。                 | 30 天
不規則的登入活動                              | 30 天
含異常登入活動的使用者                   | 30 天
遺漏認證的使用者                           | 30 天
稽核報告                                            | 180 天
重設密碼活動 (Azure AD)                      | 30 天
重設密碼活動 （身分識別管理員）              | 30 天
密碼重設登錄活動 (Azure AD)         | 30 天
密碼重設登錄活動 （身分識別管理員） | 30 天
自我服務群組活動 (Azure AD)                 | 30 天
自我服務群組活動 （身分識別管理員）         | 30 天
應用程式的使用方式                                       | 30 天
帳戶佈建活動                           | 30 天
密碼變換狀態                                | 30 天
帳戶佈建錯誤                             | 30 天
RMS 的使用方式                                               | 30 天
最作用中的 RMS 使用者                                   | 30 天
RMS 裝置使用方式                                        | 30 天
啟用 RMS 的應用程式的使用方式                           | 30 天
