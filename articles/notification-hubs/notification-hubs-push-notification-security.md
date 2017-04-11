<properties
    pageTitle="通知集線器的安全性"
    description="本主題說明 Azure 通知集線器的安全性。"
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>安全性

##<a name="overview"></a>概觀

本主題說明 Azure 通知集線器的安全性模型。 因為通知集線器服務匯流排實體，他們會實作服務匯流排為相同的安全性模型。 如需詳細資訊，請參閱[服務匯流排驗證](https://msdn.microsoft.com/library/azure/dn155925.aspx)主題。

##<a name="shared-access-signature-security-sas"></a>共用的 Access 簽章安全性 (SA) 

通知集線器實作實體層級安全性配置稱為 SA （共用 Access 簽章）。 此配置可讓訊息的項目，將最多 12 其描述中授與權限的實體上的授權規則。

每個規則包含名稱、 關鍵值 （共用密碼） 及一組權限，「 安全性宣告。 」 的一節所述 在建立通知中心時，會自動建立兩個規則︰ 一個以接聽權限 （的用戶端應用程式使用），另一個使用所有的權限 （也就是使用應用程式後端）。

從用戶端應用程式中，執行註冊管理，透過傳送的資訊時通知不會區分 （例如，天氣更新），存取通知中樞的常見方法是提供的值的規則只聆聽 access 用戶端應用程式，並提供應用程式後端的規則的完整存取權的索引鍵值。

不建議您在 Windows 市集用戶端應用程式中內嵌的值。 若要避免內嵌的值的方式是已擷取在啟動應用程式後端的用戶端應用程式。

請務必瞭解接聽存取鍵可讓任何標籤所註冊的用戶端應用程式。 如果您的應用程式必須限制為特定的用戶端 （例如，標記代表使用者識別碼） 的特定標籤進行之註冊，您的應用程式後端必須執行註冊。 如需詳細資訊，請參閱註冊管理。 請注意，在這種方式，用戶端應用程式不會直接存取通知集線器。

##<a name="security-claims"></a>安全性宣告

與其他實體，通知中心作業所允許的三個安全性宣告︰ 接聽、 傳送及管理。

| 宣告 | 描述 | 允許的作業 |
|-------|-------------|--------------------|
| 聆聽 | 更新建立、 讀取及刪除單一註冊 | 建立/更新註冊<br><br>閱讀註冊<br><br>讀取所有登錄的控點<br><br>刪除註冊 |
| 傳送 | 傳送郵件給 [通知] 中心 | 傳送郵件 |
| 管理 | CRUDs 通知集線器 （包括更新 PNS 認證與安全性索引鍵），以及了解的登錄根據標記 | 建立/更新/已讀取/刪除通知集線器<br><br>閱讀登錄標記 |


通知集線器接受授與 Microsoft Azure 存取控制權杖，並直接在 [通知] 中心上設定共用的金鑰以產生的簽章權杖宣告。