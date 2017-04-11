<properties 
    pageTitle="做針對中斷和損毀的服務匯流排應用程式 |Microsoft Azure"
    description="說明您可以使用來保護可能的服務匯流排中斷的應用程式的技巧。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>做應用程式與服務匯流排中斷和損毀的最佳作法

關鍵任務的應用程式必須持續，操作，即使有非預期的中斷或損毀。 本主題說明的技巧來保護對可能的服務中斷或損毀的服務匯流排應用程式，您可以使用。

中斷的定義的 Azure 服務匯流排暫時無法使用。 中斷可能會影響服務匯流排的部分元件，例如訊息 store] 或甚至整個資料中心。 已修正問題之後，服務匯流排有空一次。 一般而言，中斷並不會影響的郵件或其他資料。 元件失敗的範例是無法使用的特定訊息存放區。 整個資料中心的範例是資料的電源中斷的資料中心或的區域資料中心網路切換。 中斷的最後可以從幾分鐘至幾天。

損毀的定義服務匯流排刻度] 單位或資料中心的永久遺失。 資料中心可能，或可能無法使用一次。 通常損毀會遺失部分或全部的郵件或其他資料。 損毀的範例是 fire、 癱瘓，或地震。

## <a name="current-architecture"></a>目前的結構

服務匯流排會使用多個訊息存放區來儲存佇列] 或 [主題寄送的郵件。 非分割佇列或主題會被指派到一個訊息存放區。 如果無法使用此郵件儲存，則會失敗佇列或主題上的所有作業。

所有服務匯流排訊息實體佇列、 主題 （轉送） 都位於服務命名空間，會影響的資料中心。 服務匯流排不會啟用自動地理-複製資料，也無法讓橫跨多個資料中心的命名空間。

## <a name="protecting-against-acs-outages"></a>防止 ACS 中斷

如果您使用的 ACS 認證 ACS 無法使用時，用戶端可以不再取得權杖。 有權杖 ACS 停機時間的用戶端可以繼續使用服務匯流排，直到權杖過期。 預設權杖存留是 3 小時。

若要防止 ACS 中斷，使用 [共用 Access 簽章 (SA) 權杖。 在此案例中，用戶端驗證直接與服務匯流排登私密金鑰的自我 minted 的權杖。 已不再需要的來電至 ACS。 如需有關 SA 權杖的詳細資訊，請參閱[服務匯流排驗證][]。

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>保護佇列 」 和 「 主題針對訊息市集失敗

非分割佇列或主題會被指派到一個訊息存放區。 如果無法使用此郵件儲存，則會失敗佇列或主題上的所有作業。 分割的佇列中，手動，包含多個片段。 每個片段會儲存在不同的訊息存放區。 當郵件傳送至分割佇列中或主題時，服務匯流排會指派到其中一個完整的郵件。 如果無法使用的相對應的訊息存放區，服務匯流排所撰寫的郵件到不同的片段，如果可以的話。 如需有關分割的實體的詳細資訊，請參閱[分割訊息的項目][]。

## <a name="protecting-against-datacenter-outages-or-disasters"></a>保護資料中心中斷或損毀

若要允許的兩個資料中心之間的容錯移轉，您可以在每個資料中心建立服務匯流排服務命名空間。 例如 [服務匯流排服務命名空間**contosoPrimary.servicebus.windows.net**可能位於美國北美/Central 區域，和**contosoSecondary.servicebus.windows.net**可能位於美國南部/Central 區域。 如果訊息實體服務匯流排必須仍可存取資料中心中斷時，您可以在兩個命名空間中建立的實體。

如需詳細資訊，請參閱[非同步訊息的圖樣與可用性][]」 中 Azure 資料中心的服務匯流排失敗 」 一節。

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>針對資料中心中斷或損毀的保護轉送端點

地理複寫轉送結束點 」 可讓公開轉送端點可服務匯流排中斷時的服務。 若要進行地理複寫服務必須在不同的命名空間中建立兩個轉送端點。 命名空間必須位於不同的資料中心，兩個端點必須有不同的名稱。 例如主要的端點連接**contosoPrimary.servicebus.windows.net/myPrimaryService**下, 時次要涵蓋可以連絡**contosoSecondary.servicebus.windows.net/mySecondaryService**底下。

兩個端點，然後接聽服務和用戶端可以叫用的服務，透過 [結束點。 用戶端應用程式隨機做為主要的端點，挑選其中一個轉送，並將其邀請傳送至作用中的端點。 如果與錯誤碼失敗，此錯誤指出轉送端點無法使用。 開啟要備份的結束點的頻道應用程式，並會重新發出邀請。 使用中文和備份的結束點點切換角色︰ 用戶端應用程式會考慮舊作用中的結束點，可以在新的備份端點，且舊備份的結束點，為新作用中的端點。 如果同時傳送作業失敗，接下來的角色維持不變，會傳回錯誤。

[地理複寫服務匯流排轉送訊息][]範例會示範如何複寫轉送。

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>保護佇列 」 和 「 主題針對資料中心中斷或損毀

若要恢復資料中心中斷時達到使用代理訊息，服務匯流排支援兩種方法︰*使用中*」 以及*被動式*複寫。 每一種方法，如果指定的佇列或主題必須保持存取有資料中心中斷，您可以建立在兩個命名空間。 這兩個實體可以有相同的名稱。 例如主要佇列中可以存取**contosoPrimary.servicebus.windows.net/myQueue**下, 時次要涵蓋可以連絡**contosoSecondary.servicebus.windows.net/myQueue**底下。

如果應用程式時，不需要永久寄件者至收件者通訊，該應用程式可以實作長期的用戶端佇列若要避免遺失訊息，並讓寄件者的所有暫時性服務匯流排錯誤。

## <a name="active-replication"></a>作用中的複寫

作用中的複寫會使用實體兩個命名空間中的每一項作業。 任何用戶端傳送一封郵件，傳送兩份相同的郵件。 第一份會傳送至主要的實體 (例如， **contosoPrimary.servicebus.windows.net/sales**)，及第二個郵件的副本會傳送至次要實體 (例如， **contosoSecondary.servicebus.windows.net/sales**)。

用戶端收到來自兩個佇列的郵件。 收件者處理第一份郵件，並會隱藏 [第二份複本。 若要隱藏重複的訊息，寄件者必須標記的唯一識別碼與每一封郵件。 郵件的兩個複本必須使用相同的識別碼來標記。 若要標記的訊息，您可以使用[BrokeredMessage.MessageId][]或[BrokeredMessage.Label][]屬性或自訂屬性。 收件者必須保留已接收郵件的清單。

[地理複寫服務匯流排代理訊息][]範例會示範作用中複製的郵件項目。

> [AZURE.NOTE] 作用中的複寫方法加倍運算的數目，因此此方式可能會導致較高的成本。

## <a name="passive-replication"></a>被動式複寫

故障免費的大小寫，在被動式複寫會使用其中一個兩個訊息的實體。 用戶端的作用中的實體傳送的訊息。 如果在作用中的實體上作業失敗與錯誤碼，表示可能無法使用主控作用中的實體資料中心，用戶端會傳送郵件的複本的備份的實體。 使用中文和的備份的實體點切換角色︰ 傳送的用戶端會考慮要新增的備份實體舊的作用中實體和舊的備份實體是新作用中的項目。 如果同時傳送作業失敗，接下來的角色維持不變，會傳回錯誤。

用戶端收到來自兩個佇列的郵件。 因為有機會收件者會收到兩份相同的郵件，收件者必須隱藏重複的訊息。 作用中的複製所述，您可以隱藏重複項目以相同的方式。

一般而言，被動式複寫是經濟型比作用中的複寫，因為在大部分情況下會執行只有一項作業。 延遲處理量，與成本完全相同的非複寫案例。

在使用被動式複寫時，在下列情況可以會遺失或接收郵件按兩次︰

-   **郵件延遲或遺失**︰ 假設寄件者已成功傳送訊息 m1 主要佇列中，然後佇列中會變成無法使用收件者收到 m1 之前。 寄件者會傳送至第二個佇列後續訊息 m2。 如果主要佇列中暫時無法使用，收件者會收到 m1 之後佇列中會再次可用。 若損毀，收件者可能不會收到 m1。

-   **複製接待**︰ 假設寄件者傳送郵件 m，主要佇列。 服務匯流排成功處理 m，但無法傳送回應。 傳送作業逾時，寄件者就會傳送至第二個佇列 m 中相同的複本。 如果收件者可以接收 m 第一份之前主要佇列中會變成無法使用，收件者會收到兩份 m 大約相同的時間。 如果收件者不能接收 m 第一份之前主要佇列中會變成無法使用，收件者一開始會收到 m，第二個複製，但主要佇列中有空時再收到 m 一份複本。

[地理複寫服務匯流排代理訊息][]範例會示範訊息實體被動式的複寫。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解損毀修復，請參閱下列文章︰

- [Azure SQL 資料庫業務連續性][]
- [Azure 恢復技術指南][]

  [服務匯流排驗證]: service-bus-authentication-and-authorization.md
  [分割訊息的項目]: service-bus-partitioning.md
  [非同步訊息的圖樣與可用性]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [使用服務匯流排地理複寫轉送郵件]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [使用服務匯流排地理複寫代理的訊息]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Azure SQL 資料庫業務連續性]: ../sql-database/sql-database-business-continuity.md
  [Azure 恢復技術指南]: ../resiliency/resiliency-technical-guidance.md
