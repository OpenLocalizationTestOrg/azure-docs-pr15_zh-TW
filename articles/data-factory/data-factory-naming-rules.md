<properties 
    pageTitle="資料工廠-命名規則 |Microsoft Azure" 
    description="說明資料工廠實體命名規則。" 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Azure 資料工廠-命名規則 
下表提供資料工廠成品命名規則。



名稱 | 唯一名稱 | 驗證檢查
:--- | :-------------- | :----------------
資料工廠 | 在 Microsoft Azure 唯一。 名稱不區分大小寫，也就是 MyDF 和 mydf 參照相同的資料工廠。 |<ul><li>每個資料工廠連結至一個 Azure 訂閱。</li><li>物件名稱必須以字母或數字，開頭，而且可以包含字母、 數字和虛線 （-） 字元。</li><li>每個破折號 （–） 字元必須立即前面和後面再加一個字母或數字。 容器名稱中不允許連續連字號。</li><li>名稱可以是 3 63 個字元。</li></ul>
連結的服務/表格/管線 | 使用中唯一資料工廠。 名稱不區分大小寫。 | <ul><li>表格名稱中的字元數上限︰ 260。</li><li>物件名稱必須以字母數字或底線 (_) 開頭。</li><li>下列字元不允許: 」。 「，」 + 」、 「 ? 」，「 / 」，」 < 「，」 > 」、 「 * 」，"%"、"&"，」: 「，」\\」</li></ul>
資源群組 | 在 Microsoft Azure 唯一。 名稱不區分大小寫。 | <ul><li>最大字元數︰ 1000年。</li><li>名稱可以包含字母、 數字和下列字元: 「-「，」 _ 」、 「 」 和 「。 」。</li></ul>