<properties
    pageTitle="如何時 Azure 服務會影響 Azure 金鑰保存庫中斷 |Microsoft Azure"
    description="瞭解如何影響 Azure 金鑰保存庫 Azure 服務中斷事件。"
    services="key-vault"
    documentationCenter=""
    authors="adamglick"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="key-vault"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="sumedhb;aglick"/>


# <a name="azure-key-vault-availability-and-redundancy"></a>Azure 鍵保存庫可用性和重複

Azure 鍵保存庫功能，請確定，您的索引鍵和密碼都會持續為可用應用程式即使服務中的個別元件失敗重複性的多個圖層。

您的主要保存庫的內容複寫區域中，並至少 150 英哩的次要區域辦公室，但在相同的地理位置。 這會維持高持續性的索引鍵和密碼。

如果金鑰保存庫服務中的個別元件失敗，在區域內的替代元件中的步驟來處理您的要求，請確定沒有任何降低的功能。 您不需要採取任何動作來觸發此。 它會自動，並會對您而言。

在整個 Azure 區域，就無法使用少見的事件，Azure 金鑰保存庫，在該區域的要求都是自動路由 （*移轉*） 的第二個區域。 要求的主要區域再次可用時，都會傳送上一步] （*返回失敗*） 的主要區域。 同樣地，您不需要採取任何動作，因為這會自動。

您有要注意的一些注意事項︰

* 當地區故障，可能需要幾分鐘的服務。 在此期間內所做的要求可能會失敗，直到容錯移轉完成。
* 容錯移轉完成後，您的主要保存庫是以唯讀模式。 在此模式中支援的要求是︰
 * 清單金鑰保存庫
 * 取得金鑰保存庫的屬性
 * 清單機密資料
 * 取得機密資料
 * 清單索引鍵
 * 取得 （的屬性） 鍵
 * 加密
 * 解密
 * 自動換行
 * 列名
 * 驗證
 * 符號
 * 備份
* 回失敗 」 錯誤後移轉之後，所有要求 （包括已讀取*和*寫入的要求） 有類型。
