<properties
    pageTitle="建立使用 Azure 入口網站的服務匯流排命名空間 |Microsoft Azure"
    description="若要開始使用服務匯流排，您將需要命名空間。 以下說明如何建立使用 Azure 入口網站。"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>建立使用 Azure 入口網站的服務匯流排命名空間

命名空間是常見容器的所有郵件的元件。 多個佇列與主題可以存放在單一命名空間，並命名空間通常作為應用程式容器。 目前有兩種不同方式建立服務匯流排命名空間。

1.  Azure 入口網站 （本文）

2.  [資源管理員範本][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Azure 入口網站中建立命名空間

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

恭喜您 ！ 現在您已建立的服務匯流排訊息命名空間。

## <a name="next-steps"></a>後續步驟

請查看我們[GitHub 範例][github-samples]用來顯示更多進階功能的 Azure 服務匯流排訊息。

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples