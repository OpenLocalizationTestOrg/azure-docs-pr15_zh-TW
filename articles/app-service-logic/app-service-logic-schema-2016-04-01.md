<properties 
    pageTitle="新的結構描述版本 2016年-06-01 |Microsoft Azure" 
    description="瞭解如何撰寫邏輯應用程式的最新版本的 JSON 定義" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>新的結構描述版本 2016年-06-01

新的結構描述和 API 邏輯應用程式的版本有哪些改善可靠性改良功能的數字和輕鬆使用邏輯應用程式。 有 3 的主要差異︰

1. 額外的範圍，也就是包含各種動作的動作。
1. 條件和迴圈是第一類的動作
1. 執行順序更多詳細資訊，透過`runAfter`屬性 (哪些取代`dependsOn`)

如需的資訊從 2015年-08-01-預覽結構描述中的邏輯應用程式，2016年-06-01 結構描述，[取出升級下方的章節。](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1.範圍

此結構描述中最大的變更是加入的範圍以及巢狀結構中彼此的動作。  當群組的一組動作，或需要巢狀結構中 （例如條件可以包含另一個條件） 彼此的動作，這是很有幫助。  可以是範圍語法的更多詳細資料] 下方，則您可以找到找到[以下](app-service-logic-loops-and-scopes.md)，但簡單範圍範例︰


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2.條件與迴圈變更

在舊版的結構描述中，條件和迴圈已與單一動作相關聯的參數。  此結構描述中已提高這項限制，現在條件和迴圈顯示為一種動作。  可以[在本文中](app-service-logic-loops-and-scopes.md)，找到詳細資訊，並條件動作的簡單範例如下所示︰

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3.RunAfter 屬性

新`runAfter`屬性會取代`dependsOn`協助允許更精確的執行順序。  `dependsOn`不等於 」 動作會執行，並已順利完成，「 您要執行的動作，如果上一個動作是成功不過次數，請失敗，或略過。  `runAfter`可讓您的彈性。  它會指定所有之後，會執行的動作名稱的物件，並定義狀態的觸發程序從可接受的陣列。  範例，如果您想要執行的步驟成功 A 和 B 之後已成功或失敗，您可以建立下列`runAfter`屬性︰

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>升級至 2016年-06-01 結構描述

升級至新的 2016年-06-01 結構描述只需要幾個步驟。  [本文中](app-service-logic-schema-2016-04-01.md)找的結構描述變更的詳細資訊。  升級程序包含執行的升級的指令碼，將其儲存為新的邏輯應用程式，並可能會覆寫舊版邏輯應用程式如有需要。

1. 開啟您目前的邏輯應用程式。
1. 按一下工具列中的 [**更新結構描述**] 按鈕
   
    ![][1]
   
    會傳回升級後的定義。  您可以複製並貼上此資源定義，如果您需要但我們**強烈建議**您使用 [**另存新檔**] 按鈕，以確保所有連線參照是有效的升級的邏輯應用程式中。
1. 按一下 [升級刀的工具列中的 [**另存新檔**] 按鈕。
1. 填寫 [名稱與邏輯應用程式狀態，然後按一下 [**建立**部署升級邏輯應用程式。
1. 確認您已升級的邏輯應用程式如預期般運作。

    >[AZURE.NOTE] 如果您使用手冊或要求觸發程序，新的邏輯應用程式會變更回撥 URL。  使用新的 URL，以確認其運作方式的端對端，您可以在您現有的邏輯應用程式，以保留上一個 Url 複製。

1. *選擇性*使用 （相鄰**更新結構描述**中的圖示上方的圖片） 工具列中的 [**複製**] 按鈕，以新的結構描述版本覆寫先前邏輯應用程式。  這是您想要保持相同的資源識別碼，或要求邏輯應用程式的觸發程序 URL 時，才需要。

### <a name="upgrade-tool-notes"></a>升級工具備忘稿

#### <a name="condition-mapping"></a>條件對應

工具會使群組一起在升級後的定義中的範圍內的 true 和 false 分支動作的最佳效果。  特別的設計工具模式`@equals(actions('a').status, 'Skipped')`應該顯示為`else`動作。  但是如果工具偵測到模式，無法辨識的內容，可能會建立個別的條件，true 和 false 分支兩者均適用。  動作可重新對應張貼升級，如有需要。

#### <a name="foreach-with-condition"></a>使用條件 ForEach
  
在新的結構描述，以篩選動作，就能複製 foreach 迴圈每個項目條件的前一個模式。  這應該會自動在升級。  條件篩選器動作前 foreach 迴圈 （若要傳回只陣列符合條件的項目），並且陣列傳入 foreach 動作。  您可以檢視此[本文中](app-service-logic-loops-and-scopes.md)的範例

#### <a name="resource-tags"></a>資源標籤

資源標籤會在升級，您將需要升級後的工作流程重新設定。

## <a name="other-changes"></a>其他變更

### <a name="manual-trigger-renamed-to-request-trigger"></a>手動重新命名為 [要求觸發程序的觸發程序

類型`manual`已遭取代，重新命名為`request`與類型的`http`。  這是模式的更一致的觸發程序會用來建立類型。

### <a name="new-filter-action"></a>新增 「 篩選器 」 動作

如果您正在使用的大型陣列和需要篩選下一組較小的項目，您可以使用新的 「 篩選器 」 類型。  將其接受陣列及條件會計算每個項目的情況，並傳回陣列的符合該條件的項目。

### <a name="foreach-and-until-action-restrictions"></a>ForEach 和 [到] 動作限制

Foreach 和循環播放直到限制為單一動作。

### <a name="trackedproperties-on-actions"></a>TrackedProperties 動作

動作現在可以有其他屬性 (同層級`runAfter`和`type`) 稱為`trackedProperties`。  它會指定特定動作輸入或輸出做為工作流程的一部分，就會發出 Azure 診斷遙測中所包含的物件。  例如︰

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
- [使用邏輯應用程式的工作流程定義](app-service-logic-author-definitions.md)
- [建立邏輯應用程式部署範本](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png
