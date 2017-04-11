<properties
   pageTitle="報告延遲的 azure Active Directory |Microsoft Azure"
   description="所需的時間報告的事件顯示在您的 Azure Active Directory 中的時間量"
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

# <a name="azure-active-directory-report-latencies"></a>Azure Active Directory 報表延遲

*這份文件是[Azure Active Directory 報告指南](active-directory-reporting-guide.md)的一部分。*

報表                                                  | 最小值  | 平均值    | 最大值
------------------------------------------------------- | -------- | ---------- | ----------
**安全性報告**                                    |          |            |
不規則的登入活動                              | 2 的時數  | 4 小時    | 8 小時
簽署增益集不明來源                           | 2 的時數  | 4 小時    | 8 小時
多個失敗之後登增益集。                        | 2 的時數  | 4 小時    | 8 小時
從多個地區登增益集。                      | 2 的時數  | 4 小時    | 8 小時
從 [IP 位址可疑的活動登增益集。     | 2 的時數  | 4 小時    | 8 小時
從可能受感染的裝置登增益集。                 | 2 的時數  | 4 小時    | 8 小時
含異常登入活動的使用者                   | 2 的時數  | 4 小時    | 8 小時
遺漏認證的使用者                           | 2 的時數  | 4 小時    | 8 小時
**應用程式的報表**                                 |          |            |
帳戶佈建活動                           | 2 的時數  | 4 小時    | 8 小時
帳戶佈建錯誤                             | 2 的時數  | 4 小時    | 8 小時
應用程式的使用方式                                       | 2 的時數  | 4 小時    | 8 小時
密碼變換狀態                                | 2 的時數  | 4 小時    | 8 小時
**稽核與活動報表**                            |          |            |
稽核報告                                            | 1 分鐘 | 15 分鐘 | 30 分鐘
重設密碼活動 (Azure AD)                      | 2 的時數  | 4 小時    | 8 小時
重設密碼活動 （身分識別管理員）              | 2 的時數  | 4 小時    | 8 小時
密碼重設登錄活動 (Azure AD)         | 2 的時數  | 4 小時    | 8 小時
密碼重設登錄活動 （身分識別管理員） | 2 的時數  | 4 小時    | 8 小時
自我服務群組活動 (Azure AD)                 | 2 的時數  | 4 小時    | 8 小時
自我服務群組活動 （身分識別管理員）         | 2 的時數  | 4 小時    | 8 小時
**RMS 報表**                                         |          |            |
最作用中的 RMS 使用者                                   | 2 的時數  | 4 小時    | 8 小時
RMS 的使用方式                                               | 2 的時數  | 4 小時    | 8 小時
RMS 裝置使用方式                                        | 2 的時數  | 4 小時    | 8 小時
啟用 RMS 的應用程式的使用方式                           | 2 的時數  | 4 小時    | 8 小時
**私人預覽報表**                             |          |            |
所有的使用者登入活動                               | 2 的時數  | 4 小時    | 8 小時
