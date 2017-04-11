<properties
    pageTitle="快速入門轉送混合式連線 |Microsoft Azure"
    description="混合式連線的撰寫節點主控台應用程式的方式"
    services="service-bus"
    documentationCenter="node"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="node"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>快速入門轉送混合式連線

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>什麼就會完成

由於混合式連線需要的用戶端和伺服器元件，我們將在本教學課程中建立兩個主控台應用程式。 步驟如下︰

1. 建立轉送命名空間，使用 [Azure 入口網站。

2. 建立混合式部署連線，使用 [Azure 入口網站。

3. 撰寫伺服器主控台應用程式，將接收訊息。

4. 撰寫用戶端主控台應用程式來傳送郵件。

## <a name="prerequisites"></a>必要條件

1. （這個範例使用節點 7.0） [Node.js](https://nodejs.org/en/) 。

2. Azure 的訂閱。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.建立命名空間使用 Azure 入口網站

如果您已經建立轉送命名空間，請跳至[使用 Azure 入口網站建立混合式連線](#2-create-a-hybrid-connection-using-the-azure-portal)檔案。

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2.建立混合式部署連線，使用 [Azure 入口網站

如果您已經建立的混合式部署連線，請跳至 [[建立伺服器應用程式](#3-create-a-server-application-listener)] 區段。

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.建立伺服器應用程式 （接聽程式）

若要聽取，並從轉送接收訊息，我們將撰寫 Node.js 主控台應用程式。

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.建立用戶端應用程式 （寄件者）

若要傳送的郵件轉送，我們將撰寫 Node.js 主控台應用程式。

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.執行應用程式

1. 執行伺服器應用程式。

2. 執行用戶端應用程式，並輸入一些文字。

3. 請確定 [伺服器] 應用程式主控台輸出的用戶端應用程式中輸入文字。

![執行應用程式](./media/relay-hybrid-connections-node-get-started/running-applications.png)

恭喜您，您建立的端對端混合式連線應用程式。

## <a name="next-steps"></a>後續步驟︰

- [轉送常見問題集](relay-faq.md)
- [建立命名空間](relay-create-namespace-portal.md)
- [開始使用.NET](relay-hybrid-connections-dotnet-get-started.md)
- [快速入門節點](relay-hybrid-connections-node-get-started.md)