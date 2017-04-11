<properties
    pageTitle="啟用工作階段相關性蝕使用 Azure 工具組"
    description="瞭解如何啟用工作階段相關性蝕使用 Azure 工具組。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>啟用工作階段的相似性 #

內的蝕 Azure 工具組，您可以啟用 HTTP 工作階段相關性或 「 自黏工作階段]，為您的角色。 下圖顯示的**負載平衡**的屬性] 對話方塊用來啟動工作階段相關性功能︰

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>若要啟用您的角色的工作階段相似性 ##

1. 以滑鼠右鍵按一下該角色蝕的專案總管] 中，按一下**Azure**，再按一下 [**負載平衡**。
1. 在 [ **WorkerRole1 負載平衡屬性**] 對話方塊中︰
    1. 檢查**此角色的啟用 HTTP 工作階段相關性 （自黏工作階段）。**
    1. **若要使用的輸入結束點**，請選取 [輸入的端點，例如，使用**http (公用︰ 80，私人︰ 8080)**]。 您的應用程式必須使用這個端點為其 HTTP 端點。 您可以將多個端點啟用您的角色，但您可以選取其中一個支援自黏工作階段。
    1. 重建應用程式。

一旦啟用，如果您有一個以上的角色執行個體，來自特定的用戶端的 HTTP 要求會繼續處理相同的角色執行個體。

蝕工具可讓此安裝特殊 IIS 模組稱為應用程式要求路由 (ARR) 到每一個角色執行個體。 ARR 連接至適當的角色執行個體的 HTTP 要求。 這樣的內送的 HTTP 流量第一次路由 ARR 軟體，此工具組自動重新設定選取的端點。 此工具組，也會建立新的內部結束點的您 Java 伺服器設定為聆聽。 這是使用 ARR 重設的 HTTP 流量到適當的角色執行個體的結束點。 如此一來，您在多個執行個體部署中的每一個角色執行個體當做反向 proxy 所有其他執行個體，啟用自黏工作階段。

## <a name="notes-about-session-affinity"></a>工作階段相關性的相關附註 ##

* 工作階段相關性計算模擬器中無法運作。 設定可計算模擬器中套用不會干擾您建立程序] 或 [計算模擬器執行，但計算模擬器中無法運作本身的功能。
* 啟用工作階段相關性會增加為其他軟體會下載並安裝至您的角色執行個體，Azure 雲端啟動您的服務時所佔的部署 Azure 中的空間量。
* 若要初始化每一個角色的時間會更長的時間。
* 內部端點，作為流量 rerouter 上述，將會 added.ss

如需如何啟用工作階段相關性時維護工作階段資料的範例，瞭解[如何使用工作階段相關性維護工作階段資料][]。

## <a name="see-also"></a>另請參閱 ##

[針對蝕 azure 工具組][]

[建立 Azure 中蝕認識全球應用程式][]

[安裝蝕 Azure 工具組][] 

[若要維持工作階段與工作階段相關性的資料的方式][]

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[若要維持工作階段與工作階段相關性的資料的方式]: http://go.microsoft.com/fwlink/?LinkID=699539
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
