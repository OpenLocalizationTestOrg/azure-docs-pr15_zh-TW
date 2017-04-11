<properties 
    pageTitle="Azure API 管理原則 |Microsoft Azure" 
    description="瞭解如何建立、 編輯及設定 API 管理原則。" 
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


#<a name="policies-in-azure-api-management"></a>Azure API 管理原則

在 Azure API 管理原則會是允許變更的行為，透過設定的 api publisher 系統的強大功能。 原則是依序要求上執行的陳述式集合 API 的回應。 常用的陳述式包含 JSON 從 XML 格式轉換，然後呼叫限制限制的來電開發人員的工資率。 更多原則，可從] 方塊。

請參閱[原則參照][]的原則陳述式和其設定的完整清單。

位於 API 消費者和受管理的 API 之間的閘道內套用原則。 閘道器會收到所有要求，並通常不變，基礎 api 轉寄。 不過原則可將變更套用到傳入的要求和輸出的回應。

除非原則指定，否則，就可以為屬性值或文字值中任何 API 管理原則，使用原則運算式。 某些原則，例如[控制項流程][]和[變數設定][]原則根據原則運算式。 如需詳細資訊，請參閱[進階原則][]和[原則運算式][]。

## <a name="scopes"></a>如何設定原則
全域或[產品][]、 [API][]或[作業][]的範圍，您可以設定原則。 若要設定原則，瀏覽至 publisher 入口網站中的 [原則編輯器。

![原則] 功能表][policies-menu]

原則編輯器包含三個主要章節︰ 原則範圍 （頁首）、 位置 （左） 編輯原則和陳述式 （右）] 清單中的原則定義︰

![原則編輯器][policies-editor]

若要開始設定您必須先選取的範圍的原則應該套用哪些原則。 螢幕擷取畫面下方**Starter**中已選取 [產品。 原則名稱旁的方塊符號會指出此層級，已經套用原則的附註。

![範圍][policies-scope]

由於已經套用原則，設定會顯示在 [定義] 檢視中。

![設定][policies-configure]

原則會顯示唯讀的第一個。 若要編輯定義，請按一下 [**設定原則**] 動作。

![編輯][policies-edit]

原則定義是簡單的 XML 文件說明傳入和傳出陳述式的順序。 XML 可以直接在 [定義] 視窗中編輯。 陳述式的清單右側並啟用陳述式適用於目前範圍而反白顯示。**限制呼叫工資率**陳述式中上述的螢幕擷取畫面所示。

按一下 [啟用的陳述式會新增適當的 XML 游標放在 [定義] 檢視的位置。 

>[AZURE.NOTE] 如果您想要新增的原則不會啟用，請確定您是在正確的範圍，該原則。 每個原則陳述式專為使用中某些範圍及原則區段。 若要檢閱的原則節和原則的範圍，請核取該原則[原則參照][]中的 [**使用情況**] 區段。

原則陳述式的完整清單及他們的設定可供使用[原則參照][]中。

例如，若要新增新的陳述式，指定的 IP 位址限制傳入的邀請，將游標置於內的內容直接`inbound`XML 項目，按一下 [**限制來電者 IPs**陳述式。

![限制原則][policies-restrict]

這會新增至 XML 程式碼片段`inbound`指引如何設定陳述式的項目。

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

若要限制輸入的要求並接受只有從 IP 位址的 1.2.3.4 修改的 XML，如下所示︰

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![儲存][policies-save]

完成時設定的陳述式的原則，按一下 [**儲存]** ，然後變更將會傳送到的 API 管理閘道器立即。

##<a name="sections"></a>瞭解原則設定

原則是一系列的邀請及回應的順序執行的陳述式。 設定分成適當`inbound`， `backend`， `outbound`，及`on-error`sections 下列設定中所示。

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

如果發生錯誤時要求的處理期間，任何剩餘的步驟`inbound`， `backend`，或`outbound`略過的節，並執行跳至中的陳述式`on-error`一節。 放置在原則陳述式來`on-error`一節，您可以使用檢閱錯誤`context.LastError`屬性，檢查及自訂錯誤回應使用`set-body`原則，並設定發生錯誤時，會發生什麼情況。 有錯誤碼內建的步驟，並處理原則陳述式時可能會發生的錯誤。 如需詳細資訊，請參閱[錯誤處理 API 管理原則](https://msdn.microsoft.com/library/azure/mt629506.aspx)。

因為原則可以指定在不同層級 （全域、 產品、 api 及作業） 設定讓您指定的原則定義陳述式執行與上層原則的順序。 

原則範圍會以下列順序進行評估。

1. 全域範圍
2. 產品範圍
3. API 範圍
4. 作業範圍

內進行的陳述式的評估根據的放置`base`項目，如果有的話。

例如，如果您有全域層級，API 設定原則的原則，然後使用該特定的 API 時這兩個原則套用。 API 管理可讓確定排序合併的原則陳述式，透過基底的項目。 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

在上述範例原則定義`cross-domain`陳述式會執行任何較高的原則，可在中開啟，後面再`find-and-replace`原則。

如果相同的原則出現原則陳述式中，按兩次，就會套用最近評估過的原則。 您可以使用此選項會覆寫較高的範圍定義的原則。 若要查看的原則，在目前的範圍原則編輯器] 中，按一下 [**重新計算的選取範圍的有效原則**]。

請注意，全域原則有任何上層原則，並使用`<base>`中的項目有任何效果。 

## <a name="next-steps"></a>後續步驟

請查看下列原則運算式上的視訊。

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[原則參考]: api-management-policy-reference.md
[產品]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[作業]: api-management-howto-add-operations.md

[進階的原則]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[控制流程]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[設定變數]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[原則運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
