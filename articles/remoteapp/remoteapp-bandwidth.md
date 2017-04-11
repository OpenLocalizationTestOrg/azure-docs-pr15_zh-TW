
<properties 
    pageTitle="估計 Azure RemoteApp 網路頻寬使用量 |Microsoft Azure"
    description="瞭解您的 Azure RemoteApp 集合和應用程式的網路頻寬需求。"
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>估計 Azure RemoteApp 網路頻寬使用量 

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 使用遠端桌面通訊協定 (RDP) 之間執行 Azure 雲端和您的使用者的應用程式進行通訊。 本文提供可用來估計的網路使用，可能會評估每個 Azure RemoteApp 使用者的網路頻寬使用量的一些基本原則。

評估頻寬使用量每位使用者很複雜，且需要執行多個應用程式同時在多工案例中可能會影響應用程式的位置彼此的效能根據他們的網路頻寬需求。 不同的頻寬結果可能會導致甚至遠端桌面用戶端 （例如 HTML5 用戶端與 Mac 用戶端） 的類型。 若要可協助您執行這些複雜，我們會中斷的使用案例將數個複寫真實案例常見的類別。 （其中現實情況是，當然，混合的類別，並會由使用者而異。）

我們進一步-之前請注意，我們假設 RDP 提供最佳體驗的好大部分的使用案例在網路上延遲 120 ms 與頻寬下方超過 5 Mb-這根據 RDP 的功能，以動態方式調整使用可用的網路頻寬和估計的應用程式頻寬需求。 本文超出那些 」 大部分的使用案例 」 若要查看案例的開始回溯和位置降低開始的使用者體驗的邊緣。

比較基準建議和什麼我們未包含在估計立即查看下列文章，如需詳細資訊，包括，需要考慮的因素。

- [如何網路頻寬和品質體驗的工作放在一起？](remoteapp-bandwidthexperience.md)
- [測試您的一些常見的案例的網路頻寬使用量](remoteapp-bandwidthtests.md)
- [如果您沒有時間] 或 [測試能夠快速的指導方針](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>我們不包括什麼？

當您檢視的建議的測試和我們整體 （和的確一般） 的建議時，請注意，有幾個不是由我們考慮的因素。 例如上, 傳與非對稱式性質所提供的使用者體驗複雜下載頻寬。 非對稱式性質大部分 Wi-fi 網路時，此外會影響效能和使用者體驗認知。 互動式的情況下可能低於上游，這可能會增加遺失的視訊或音訊圖文框的數字，因此影響串流經驗的使用者認知優先順序下游流量。 您可以執行您自己的實驗，以查看建議您的特定的使用案例和網路。

雖然我們討論裝置重新導向，我們已不考慮所造成的儲存空間、 印表機、 掃描器、 網路攝影機及其他 USB 裝置等的附加裝置的網路流量的頻寬影響。 這些裝置的效果通常暫時尖峰頻寬需求，然後消失時任務已完成。 但如果經常，完成頻寬需求可能太醒目的通知。

我們也不討論如何一位使用者可能會影響其他相同的網路中的使用者。 例如，使用 4 K 視訊 100 MB/s 網路上的一位使用者大幅可能會影響嘗試執行相同的工作相同的網路上的其他使用者。 很抱歉，它會變得難以判斷並行使用提供的一般] 或 [完全包含的建議，瞭解如何系統會在彙總的影響。 所有說，為基礎的通訊協定技術會使您可用的網路頻寬的最佳使用，但它有其限制。