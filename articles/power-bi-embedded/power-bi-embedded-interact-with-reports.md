<properties
   pageTitle="與使用 JavaScript API 報表互動 |Microsoft Azure"
   description="Power BI 內嵌、 互動使用 JavaScript API 報表"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>互動使用 JavaScript API 的 Power BI 報表

Power BI JavaScript API 可讓您輕鬆地將 Power BI 報表內嵌至您的應用程式。 Api，您的應用程式以程式設計方式可與不同的報表頁面等篩選的項目互動。 此互動功能可讓 Power BI 報表更多整合式的應用程式的一部分。

您的應用程式組件為使用主控的 iframe 內嵌 Power BI 報表應用程式中。 下圖中，您可以看到 iframe 作為您的應用程式與報表之間的邊界。 

![Power BI 內嵌 iframe 不 Javascript API](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

Iframe，會使內嵌的程序很簡單，但不 JavaScript API 報表] 和 [應用程式無法彼此互動。 這項的互動，可讓您覺得報表不是真正的應用程式組件。 在報表和應用程式真的需要通訊來回，如圖所示。

![Power BI 內嵌 iframe 和 Javascript API](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

Power BI JavaScript API 可讓您撰寫可安全地通過 iframe 邊界的程式碼。 這可讓應用程式以程式設計方式在報表中，執行的動作，並在接聽事件從報表中的使用者進行的動作。

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>您可以使用 Power BI JavaScript API 做什麼？
JavaScript api，可以管理報表、 瀏覽至報表中的頁面、 篩選報表中，並處理內嵌事件。 下圖顯示 API 的結構。

![Power BI JavaScript API 圖表](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>管理報表
Javascript API 可讓您管理報表] 和 [頁面層級的行為︰

- 安全地內嵌在您的應用程式中的特定的 Power BI 報表-嘗試[內嵌示範應用程式](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - 設定存取權杖
- 設定報表
  - 啟用及停用 [篩選器窗格] 和 [頁面導覽-嘗試[更新設定示範應用程式](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - 設定頁面和篩選的預設值-嘗試[設定預設值的示範](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- 輸入，然後結束全螢幕模式

[深入瞭解內嵌報表](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>瀏覽至報表中的頁面
JavaScript API enbales 您探索報表中的所有頁面，並設定目前的頁面。 請嘗試[導覽示範應用程式](http://azure-samples.github.io/powerbi-angular-client/#/scenario3)。

[進一步瞭解頁面導覽](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>篩選報表
JavaScript API 提供基本和進階篩選內嵌的報表及報表頁面的功能。 請嘗試[篩選示範應用程式](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)，並檢閱一些簡介的程式碼。  


#### <a name="basic-filters"></a>基本篩選
基本篩選器會放在欄或階層層級，並包含的值，來包含或排除清單。

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>進階的篩選
進階的篩選使用邏輯運算子和或 OR，並接受一或兩個條件，兩者都有自己的運算子和值。 支援的條件是︰

- 無
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- 包含
- DoesNotContain
- StartsWith
- DoesNotStartWith
- 為
- IsNot
- IsBlank
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[深入瞭解篩選](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>處理事件
除了傳送資訊至 iframe，您的應用程式也會收到來自 iframe 事件的詳細資訊︰

- 內嵌
  - 載入
  - 錯誤
- 報表
  - pageChanged
  - dataSelected （即將推出）

[深入瞭解處理事件](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>後續步驟
如需有關 Power BI JavaScript API 的詳細資訊，請查看下列連結︰

- [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [物件模型參照](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- 範例
  - [角度](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [即時示範](https://microsoft.github.io/PowerBI-JavaScript/demo/)
