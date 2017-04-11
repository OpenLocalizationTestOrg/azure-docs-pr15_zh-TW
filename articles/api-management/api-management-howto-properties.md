<properties 
    pageTitle="如何使用中 Azure API 管理原則的內容" 
    description="瞭解如何使用中 Azure API 管理原則的內容。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>如何使用中 Azure API 管理原則的內容

API 管理原則會允許變更的行為，透過設定的 api publisher 系統的強大功能。 原則是依序要求上執行的陳述式集合 API 的回應。 您可使用常值文字值、 原則運算式，而且內容建構原則陳述式。 

每個 API 管理服務執行個體有金鑰/值組是全域服務執行個體的 properties 集合。 這些屬性可用於管理所有 API 設定及原則常數字串值。 每個內容有下列屬性。


| 屬性 | 類型            | 描述                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| 名稱      | 字串          | 屬性名稱。 它可能包含只字母、 數字、 週期、 虛線，並底線字元。 |
| 值     | 字串          | 屬性值。 它不可能會是空的或只包含空格。                           |
| 密碼    | 布林值         | 決定值是否為私人，並應該加密。                                |
| 標記      | 字串的陣列 | 可省略標籤時提供可用來篩選 [屬性] 清單。                               |

在 [**屬性**] 索引標籤上的 publisher 入口網站中設定屬性。 在下列範例中，三個屬性的設定。

![屬性][api-management-properties]

屬性值包含常值字串及[原則的運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)。 下表顯示先前的三個範例內容和其屬性。 值的`ExpressionProperty`是原則運算式，傳回包含目前的日期和時間的字串。 屬性`ContosoHeaderValue`標示為 [私人]，所以不會顯示其值。

| 名稱               | 值                      | 密碼 | 標記    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | False  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | 為 true   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | False  |         |

## <a name="to-use-a-property"></a>若要使用的屬性

若要使用屬性的原則，將 [雙對等的括號內的屬性名稱`{{ContosoHeader}}`，如下列範例所示。

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

在此範例中，`ContosoHeader`做為標頭中的名稱`set-header`原則，並`ContosoHeaderValue`做為該標題的值。 在進行要求或回應的 API 管理閘道器中，評估此原則時`{{ContosoHeader}}`和`{{ContosoHeaderValue}}`其個別的屬性值所取代。

內容可為完整屬性或項目值，如下圖所示，在先前範例中，但是可以也會插入或加上的文字運算式，如下列範例所示的一部分︰`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

內容也可以包含原則運算式。 在下列範例中，`ExpressionProperty`使用。

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

評估此原則時，`{{ExpressionProperty}}`會取代的值︰ `@(DateTime.Now.ToString())`。 因為數值是原則運算式，評估的運算式，並原則會繼續執行。

您可以在開發人員入口網站中測試此出呼叫作業屬性與原則的範圍。 在下列範例中，使用兩個的上一個範例呼叫作業`set-header`屬性與原則。 請注意回應包含兩個已設定原則使用屬性的自訂標題。

![開發人員入口網站][api-management-send-results]

如果您查看[API 檢查追蹤](api-management-howto-api-inspector.md)進行通話，其中包含兩個屬性與上一個範例原則時，就可以看見兩個`set-header`原則，以及原則運算式評估之屬性的包含原則運算式所插入的屬性值。

![API 檢查追蹤][api-management-api-inspector-trace]

請注意屬性值可以包含原則運算式，而屬性值不能包含其他屬性。 如果文字包含屬性參照用於屬性值，例如`Property value text {{MyProperty}}`，就不會取代該屬性參照，並會屬性值的一部分。

## <a name="to-create-a-property"></a>若要建立的屬性

若要建立屬性，按一下 [**屬性**] 索引標籤上的 [**新增屬性**]。

![新增屬性][api-management-properties-add-property-menu]

**名稱**和**值**是必要的值。 如果此屬性值是私人，核取**這是 [私人**] 核取方塊。 輸入一或多個選用的標籤，以協助組織您的內容，然後按一下 [**儲存**]。

![新增屬性][api-management-properties-add-property]

儲存新屬性時，[**搜尋] 屬性**] 文字方塊中填入新屬性的名稱，並顯示新的屬性。 若要顯示所有內容，請清除 [**搜尋] 屬性**文字方塊，然後按下 enter。

![屬性][api-management-properties-property-saved]

建立使用 REST API 屬性的資訊，請參閱[建立使用 REST API 的屬性](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put)。

## <a name="to-edit-a-property"></a>若要編輯的屬性

若要編輯的屬性，請按一下 [編輯屬性] 旁的 [**編輯**]。

![編輯屬性][api-management-properties-edit]

進行任何所需的變更，然後按一下 [**儲存**]。 如果您變更屬性名稱時，任何參照該摘要的原則會自動更新以使用新的名稱。

![編輯屬性][api-management-properties-edit-property]

編輯使用 REST API 屬性的詳細資訊，請參閱[編輯使用 REST API 的屬性](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch)。

## <a name="to-delete-a-property"></a>若要刪除的屬性

若要刪除的屬性，按一下要刪除的屬性旁邊的 [**刪除**]。

![刪除屬性][api-management-properties-delete]

按一下 [**是]，將其刪除**以確認。

![確認刪除][api-management-delete-confirm]

>[AZURE.IMPORTANT] 如果有任何原則參照屬性，您無法成功將其刪除，直到您移除所有使用該原則屬性。

刪除使用 REST API 的屬性的詳細資訊，請參閱[刪除使用 REST API 的屬性](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete)。

## <a name="to-search-and-filter-properties"></a>若要搜尋及篩選屬性

[**屬性**] 索引標籤包含搜尋和篩選功能，協助您管理您的內容。 若要依屬性名稱篩選 [屬性] 清單，請在 [**搜尋] 屬性**] 文字方塊中輸入搜尋字詞。 若要顯示所有內容，請清除 [**搜尋] 屬性**文字方塊，然後按下 enter。

![搜尋][api-management-properties-search]

若要標記值篩選 [屬性] 清單，請**依標籤篩選]**文字方塊中輸入一或多個標籤。 若要顯示所有內容，請清除**篩選標籤**文字方塊並按 enter。

![篩選][api-management-properties-filter]

## <a name="next-steps"></a>後續步驟

-   進一步瞭解使用原則
    -   [在 [API 管理原則](api-management-howto-policies.md)
    -   [原則參考](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>觀看視訊的概觀

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

