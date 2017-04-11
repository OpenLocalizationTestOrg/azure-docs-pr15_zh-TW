<properties
   pageTitle="擴充的服務布料的轉印圖樣服務 |Microsoft Azure"
   description="說明如何不按比例縮放服務布料的轉印圖樣服務"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="scaling-service-fabric-applications"></a>縮放比例服務布料的轉印圖樣應用程式
Azure 服務布料的轉印圖樣可以輕鬆建立可調整的應用程式的負載平衡服務、 磁碟分割及複本上叢集內的所有節點。 這可讓最大資源使用狀況。

兩種方法可以達成高的小數位數的服務布料的轉印圖樣應用程式︰

1. 縮放比例分割層級

2. 縮放服務名稱層級

## <a name="scaling-at-the-partition-level"></a>縮放比例分割層級
服務布料的轉印圖樣支援分割成多個較小的磁碟分割區的 [個別的服務。 [分割概觀](service-fabric-concepts-partitioning.md)提供的支援的分割配置類型資訊。 每個磁碟分割的複本會分散叢集節點。 請考慮使用遠距分割配置 0 低鍵、 99，高鍵與四個磁碟分割的服務。 三個節點叢集，服務可能會配置四個的複本的共用資源的每個節點，如下所示︰

![含三個節點的磁碟分割版面配置](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

增加的節點數目，可讓服務利用上新的節點的資源將複本部分移至空白的節點布料的轉印圖樣。 藉由增加為 4 的節點數目，服務現在有三個每個節點 （不同的磁碟分割），以便更好的資源使用狀況和效能上執行的複本。

![有四個節點的磁碟分割版面配置](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>縮放服務名稱層級
服務執行個體是特定的執行個體的應用程式名稱和服務類型名稱 （請參閱[服務布料的轉印圖樣應用程式生命週期](service-fabric-application-lifecycle.md)）。 在建立期間的服務，您可以指定磁碟分割配置 （請參閱[分割服務布料的轉印圖樣服務](service-fabric-concepts-partitioning.md)） 使用。

服務名稱是按比例縮放的第一層級。 您可以使用不同層級的分割，為您較舊的服務執行個體會變成 [忙碌建立新的執行個體的服務。 這個選項可讓新的服務消費者使用較不忙碌的服務執行個體，而非忙碌的文件。

建立新的服務執行個體，新的分割配置可提高生產力，以及增加或降低磁碟分割計數的其中一個選項。 這會將複雜，但為任何耗用的用戶端必須知道何時及如何使用不同名稱的服務。

### <a name="example-scenario-embedded-dates"></a>範例案例︰ 內嵌日期
一個可能的情況是，為服務名稱的一部分使用日期資訊。 例如，您可以使用服務執行個體以特定的 2013年中加入的所有客戶名稱] 和 [客戶在 2014 年加入另一個名稱。 這個命名配置可讓以程式設計方式增加根據日期的名稱 （2014年方法為服務建立執行個體 2014 可以視需要）。

不過，這種方法根據使用的服務布料的轉印圖樣知識庫範圍以外的特定應用程式的命名資訊的用戶端。

- *使用的命名慣例*︰ 在 2013 中，當您的應用程式上市後，您建立服務要求布料的轉印圖樣: / 應用程式/service2013。 附近的 2013年第二季，您可以建立其他服務，一部分布料的轉印圖樣: / 應用程式/service2014。 這兩種服務是相同的服務類型。 在這種方式，您的用戶端會需要採用邏輯，以建構年適當的服務名稱。

- *使用查閱服務*︰ 另一種模式是提供的次要查閱服務可提供服務所需的索引鍵的名稱。 查閱服務就可以建立新的服務執行個體。 查閱服務本身無法保留任何應用程式的資料，只需服務名稱，它會建立的資料。 因此，年型上述範例中，用戶端會先連絡查閱服務，找出特定的年份，處理資料服務名稱，然後再使用 [執行的實際作業的該服務名稱。 第一個查詢的結果可快取。

## <a name="next-steps"></a>後續步驟

如需有關服務布料的轉印圖樣概念的詳細資訊，請參閱下列各項︰

- [服務布料的轉印圖樣服務的可用性](service-fabric-availability-services.md)

- [分割服務布料的轉印圖樣服務](service-fabric-concepts-partitioning.md)

- [定義及管理狀態](service-fabric-concepts-state.md)
