<properties 
    pageTitle="如何設定 Azure API 管理通知和電子郵件範本" 
    description="瞭解如何設定通知，及電子郵件 Azure API 管理中的範本。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>如何設定 Azure API 管理通知和電子郵件範本

API 管理提供的功能設定通知，針對特定事件，以及設定進行通訊的系統管理員和 API 管理執行個體的開發人員所使用的電子郵件範本。 本主題說明如何設定通知，可用的事件，並提供的設定使用這些事件的電子郵件範本概觀。

## <a name="publisher-notifications"></a>設定 publisher 通知

若要設定通知，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 會帶您到 API 管理 publisher 入口網站。

![Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

從**API 管理**左側功能表上，檢視可用的通知中按一下 [**通知**]。

![Publisher 通知][api-management-publisher-notifications]

下列清單的事件可以設定通知]。

-   **訂閱要求 （需要核准）** -指定的電子郵件收件者和使用者會收到關於訂閱要求 API 產品需要核准的電子郵件通知。
-   **新的訂閱**的使用者與指定的電子郵件收件者會收到關於新的 API 產品訂閱電子郵件通知。
-   **應用程式組件庫要求**-指定的電子郵件收件者和使用者提出新的應用程式至應用程式庫時，會收到電子郵件通知。
-   **密件副本**-指定的電子郵件收件者和使用者會收到電子郵件密件副本傳送至 [開發人員的所有電子郵件。
-   **新的問題或註解**-指定的電子郵件收件者和使用者會收到電子郵件通知時的新的問題或註解會送出，在開發人員入口網站。
-   **關閉帳戶的訊息**-指定的電子郵件收件者和使用者帳戶已關閉時，會收到電子郵件通知。
-   **Approaching 訂閱配額限制**-下列電子郵件收件者和使用者訂閱的使用方式取得靠近使用量配額時，會收到電子郵件通知。

為每個事件，您可以指定電子郵件收件者使用電子郵件地址] 文字方塊，或從清單中選取使用者。

若要指定的電子郵件地址時收到通知，請輸入其在電子郵件地址] 文字方塊中。 如果您有多個電子郵件地址，分隔使用逗號。

![通知收件者][api-management-email-addresses]

若要指定時收到通知使用者，按一下 [**新增收件者**、 核取時收到通知，使用者旁邊的方塊，然後按一下**[確定]**。

>請注意，只有管理員才會顯示在清單中。

設定後的通知收件者，請按一下 [**儲存**] 以套用更新的通知收件者。

>如果您瀏覽離開 [ **Publisher 通知**] 索引標籤的 publisher 入口網站會通知您如果那里未儲存的變更。

## <a name="email-templates"></a>設定電子郵件範本

API 管理提供電子郵件傳送的訊息會在 [管理和使用服務的電子郵件範本。 提供下列電子郵件範本。

-   應用程式庫送出核准
-   開發人員辭別字母
-   開發人員配額限制接近通知
-   邀請使用者
-   新增註解新增至有問題
-   收到新的問題
-   啟動新的訂閱
-   更新的訂閱確認
-   訂閱邀請參加
-   接收到的訂閱要求

您可以修改這些範本，視需要。

若要檢視並設定您的 API 管理執行個體的電子郵件範本，按一下 [**通知**從**API 管理**左側功能表上，然後選取 [**電子郵件範本**] 索引標籤。

![電子郵件範本][api-management-email-templates]

若要檢視或修改特定的範本，請從 [**範本**] 下拉式清單中選取它。

![電子郵件範本清單][api-management-email-templates-list]

每個電子郵件範本具有以純文字主旨和本文定義以 HTML 格式。 您可以自訂每個項目，視需要。

![電子郵件範本編輯器][api-management-email-template]

[**參數**] 清單包含的參數，清單時插入主旨或內文中，將會傳送電子郵件時取代指定的值。 若要插入的參數，將游標放在您想要移，請的參數，然後按一下參數名稱左邊的箭號。

按一下 [**預覽**] 或 [**傳送測試**若要查看如何電子郵件會尋找或測試電子郵件傳送]。

>請注意預覽或傳送測試時的實際值不會取代參數。
>
>若要將變更儲存至電子郵件範本，請按一下 [**儲存**]，或取消所做的變更按一下 [**取消**]。



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[開始使用 Azure API 管理]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance