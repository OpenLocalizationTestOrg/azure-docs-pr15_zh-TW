<properties 
    pageTitle="Azure RemoteApp 網路頻寬的一般指導方針 |Microsoft Azure"
    description="瞭解基本的網路頻寬方針 Azure RemoteApp 集合和應用程式。"
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
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp 網路頻寬-（如果您不能測試自己） 的一般指導方針

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

如果您沒有時間或 Azure RemoteApp 中執行[的網路頻寬測試](remoteapp-bandwidthtests.md)的功能，以下是一些相當一般指導方針可協助您估計的網路頻寬每個使用者。

如果您有這些案例的組合，我們不建議任何項目小於 （或等於） 10 MB/s 為現代化的連線到網際網路的應用程式，在遠端環境中的最小值網路頻寬。 (雖然所述，這會保證獲得更佳比平均的使用者體驗。)

## <a name="complex-powerpoint-simple-powerpoint"></a>複雜的 PowerPoint，簡單的 PowerPoint

Azure RemoteApp 會最適合在 100 MB 區域網路。 在 10 MB/s 網路設定檔，抖動 120 ms 上方時超過 5%，使用者會看到平均的體驗。 在 1 MB/s 不同 glaring-例如，在投影片放映中，使用者可能無法看到動畫的轉場效果完全因為會略過的畫面。

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet explorer 是指混合 PDF、 PDF 文字

10 MB/s 網路設定檔是靠近 LAN 在大部分層面。 1 MB/s 會提供確定的體驗，雖然可能會有一些抖動當使用者捲動時就是在螢幕上的影像。

## <a name="flash-video-youtube"></a>Flash 視訊 (YouTube)

100 MB/s LAN 會提供最佳體驗，10 MB/s 時可接受 （表示我們的圖文框工資率，但抖動增加）。 1 MB/s，抖動是非常大，而且醒目的通知。

## <a name="word-typing-word-remote-input"></a>Word 中輸入 （Word 遠端輸入）
這是低頻寬使用量的案例。 256 KB/s，我們會提供好的區域網路經驗。

## <a name="learn-more"></a>深入瞭解
- [估計 Azure RemoteApp 網路頻寬使用量](remoteapp-bandwidth.md)

- [Azure RemoteApp-如何網路頻寬和品質體驗的工作放在一起？](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp-tseting 一些常見的案例與您的網路頻寬使用量](remoteapp-bandwidthtests.md)