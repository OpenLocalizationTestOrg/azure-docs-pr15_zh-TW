<properties
   pageTitle="定義及管理狀態 |Microsoft Azure"
   description="如何定義和管理服務布料的轉印圖樣的服務狀態"
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

# <a name="service-state"></a>服務狀態
**服務狀態**指的是服務函數時所需的資料。 其包含的資料結構及服務讀取與寫入工作的變數。

請考慮簡單計算機服務，例如。 這項服務採用兩個數字，並傳回其總和。 這是不含的資料與其相關聯的完全沒有狀態服務。

現在請考慮相同的計算機，但除了計算加總]，也有傳回最後一個總和它具有計算的方法。 這項服務是現在狀態，其中包含部分 （時計算新加總） 會將寫入的狀態，從 （時傳回一個計算加總） 讀取。

Azure 服務布料的轉印圖樣，在第一個服務稱為無狀態的服務。 第二個服務要求狀態的服務。

## <a name="storing-service-state"></a>儲存服務狀態
狀態可以是允或共同位於 [程式碼，它會處理狀態。 省/市的 externalization 通常是使用外部資料庫或存放區。 在計算機範例中，這可能是 SQL 資料庫表格中儲存目前的結果。 計算加總的每個要求執行此列更新。

狀態也可能是放在一起處理此程式碼的程式碼。 使用此模型建立服務布料的轉印圖樣的服務狀態。 服務架構提供基礎結構，以確定高度可使用此狀態和容錯失敗。

## <a name="next-steps"></a>後續步驟

如需有關服務布料的轉印圖樣概念的詳細資訊，請參閱下列各項︰

- [服務布料的轉印圖樣服務的可用性](service-fabric-availability-services.md)

- [服務布料的轉印圖樣服務的延展性](service-fabric-concepts-scalability.md)

- [分割服務布料的轉印圖樣服務](service-fabric-concepts-partitioning.md)
