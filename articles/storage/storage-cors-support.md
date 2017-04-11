<properties
    pageTitle="十字形，資源共用 (CORS) 支援 |Microsoft Azure"
    description="瞭解如何針對 Microsoft Azure 儲存服務啟用 CORS 支援。"
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>十字形，資源共用 (CORS) 支援 Azure 儲存服務

開始使用版本 2013年-08-15，Azure 儲存服務的支援十字形，共用資源 (CORS) Blob、 表格、 佇列中，以及檔案的服務。 CORS 是 HTTP 功能可讓 web 應用程式執行下一個網域，存取另一個網域中的資源。 網頁瀏覽器執行稱為[相同來源的原則](http://www.w3.org/Security/wiki/Same_Origin_Policy)，可避免在不同的網域; 呼叫 Api 網頁安全性限制CORS 提供安全的方法，若要允許撥打 Api 另一個網域中的一個網域 （原點的網域）。 [CORS 規格](http://www.w3.org/TR/cors/)如需詳細資訊，請參閱 CORS。

您可以設定 CORS 規則個別的儲存空間服務，每個，則可電話[設定 Blob 服務屬性](https://msdn.microsoft.com/library/hh452235.aspx)、[設定佇列服務屬性](https://msdn.microsoft.com/library/hh452232.aspx)和[設定資料表服務屬性](https://msdn.microsoft.com/library/hh452240.aspx)。 當您設定服務的 CORS 規則時，將會以判斷是否允許根據您指定的規則評估正確通過驗證的要求，對服務，從不同的網域。

>[AZURE.NOTE] 請注意，CORS 無法驗證機制。 啟用 CORS 時對儲存資源任何要求必須有適當的驗證簽章或必須對公用資源。

## <a name="understanding-cors-requests"></a>了解 CORS 要求

從來源網域 CORS 要求可能包含兩個不同的要求︰

- 預先要求，會查詢服務的 CORS 限制。 除非要求方法是[簡單的方法](http://www.w3.org/TR/cors/)，這表示取得、 不對或文章需要預先要求。

- 實際要求，對所要的資源。

### <a name="preflight-request"></a>預先要求

預先要求查詢所建立的儲存空間服務帳戶擁有者 CORS 限制。 網頁瀏覽器 （或其他使用者代理程式） 傳送選項要求，包括要求標頭、 方法及來源的網域。 儲存服務評估預期根據一組預先設定的指定指定哪些原點網域、 要求方法和要求標頭針對儲存資源的實際要求 CORS 規則的作業。

如果 CORS 已啟用的服務，且沒有符合預先要求的 CORS 規則服務回應狀態碼 200 （確定），並在回應中包括必要的存取控制標題。

如果 CORS 不會啟用的服務，或沒有 CORS 規則符合預先要求，請將回應服務狀態碼 403 （禁止）。

如果選項要求不包含必要的 CORS 標題 （[Origin] 和 [存取控制要求-方法標題），將回應服務狀態碼 400 （錯誤的要求）。

請注意，預先要求評估服務 （Blob 與佇列中，表格），但不檢查要求的資源。 帳戶擁有者必須有啟用 CORS 成功邀請的順序帳戶服務內容的一部分。

### <a name="actual-request"></a>實際的邀請

接受預先要求並回應會傳回之後，在瀏覽器會分派針對儲存資源的實際要求。 在瀏覽器將拒絕實際如果預先就會遭到拒絕立即要求。

實際要求被視為服務所儲存的一般要求。 原點標題的目前狀態會指出要求是 CORS 邀請，並比對 CORS 規則檢查服務。 如果找到相符項目，存取控制標題新增回應或傳送回用戶端。 如果找不到相符項目，不會傳回 CORS 存取控制標題。

## <a name="enabling-cors-for-the-azure-storage-services"></a>啟用 CORS Azure 儲存體服務

在服務等級，設定 CORS 規則，因此您需要啟用或停用的每個服務 （Blob、 佇列和表格） 的 CORS 分別。 根據預設，CORS 會為每個服務停用。 若要啟用 CORS，您必須設定使用版 2013年-08-15 的適當的服務屬性或更新版本，並將 CORS 規則新增至服務內容。 如需有關如何啟用或停用 CORS 服務，以及如何設定 CORS 規則的詳細資訊，請參閱[設定 Blob 服務屬性](https://msdn.microsoft.com/library/hh452235.aspx)、[設定佇列服務屬性](https://msdn.microsoft.com/library/hh452232.aspx)和[設定資料表服務屬性](https://msdn.microsoft.com/library/hh452240.aspx)。

以下是單一 CORS 規則，透過設定服務內容作業指定的範例︰

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

包含在 CORS 規則中每個項目說明如下︰

- **AllowedOrigins**︰ 原始網域，允許針對透過 CORS 儲存服務要求。 原點網域是要求的來源的網域。 請注意原點必須完全區分大小寫符合的使用者年齡會傳送到服務的來源。 您也可以使用萬用字元 ' *' 為允許透過 CORS 要求所有原始網域。 在上述範例中，網域[http://www.contoso.com](http://www.contoso.com) [http://www.fabrikam.com](http://www.fabrikam.com)可以讓對使用 CORS 的服務要求。

- **AllowedMethods**︰ 原點網域可能會使用 CORS 邀請的方法 （HTTP 要求動作）。 在上述範例中，只放入和 GET 要求允許。

- **AllowedHeaders**: CORS 要求指定原點網域，可能會要求標頭。 在上述範例中，會允許所有 x-ms-中繼資料，x-ms-中繼-目標，並 x ms-中繼 abc 開始著手的中繼資料行首。 請注意，萬用字元 ' *' 表示是否允許指定的前置詞與任何頁首開頭。

- **ExposedHeaders**︰ 回應標頭的可能會傳送回應 CORS 要求和瀏覽器，要求發行者公開。 在上述範例中，在瀏覽器指示公開 x-ms-中繼任何有標題的開頭。

- **MaxAgeInSeconds**︰ 在瀏覽器應該快取預先選項的最大時間要求。

Azure 儲存服務支援指定前置的頁首的 [ **AllowedHeaders** ] 和 [ **ExposedHeaders**元素。 若要允許的類別目錄的標題，您可以指定該類別的常見前置詞。 例如，指定*x-ms-中繼** 為前置的頁首建立規則符合開頭 x-ms-中繼的所有標題。

下列限制適用於 CORS 規則︰

- 您可以指定最多五個 CORS 規則，每個儲存服務 （Blob、 表格和佇列中）。
- 所有 CORS 規則設定要求時，不包括 XML 標籤的大小上限不可超過 2 篇知識庫文章。
- 允許的頁首]、 [公開的頁首或 [允許的原點的長度不超過 256 個字元。
- 允許的標題和公開的標頭可能也會︰
  - 標題文字，位置要提供的確切的標頭名稱，例如**x-ms-中繼-處理**。 要求指定 64 常值的標題的最大值。
  - 加上的標題，其中要提供標題的前置字元，例如**x ms-中繼資料***。 指定前置詞，以這種方式可讓或公開任何頁首，指定前置詞的開頭。 要求指定兩個前置標題的最大值。
- 支援 Azure 儲存服務 Api 的方法必須符合**AllowedMethods**項目中指定的方法 （或 HTTP 動作）。 支援的方法是刪除、 取得、 不對、 合併、 文章、 選項及放入。

## <a name="understanding-cors-rule-evaluation-logic"></a>了解 CORS 規則評估邏輯

儲存服務收到預先或實際要求時，它會評估根據 CORS 規則，您建立的適當的設定服務內容作業透過服務的要求。 CORS 規則的評估已設定設定服務內容作業的邀請內文中的順序。

CORS 規則的評估，如下所示︰

1. 首先，要求的原點網域已針對**AllowedOrigins**項目的列出的網域。 如果來源網域包含在清單中，或使用萬用字元允許所有的網域 」 * 」，然後規則評估進行。 如果不包含原始網域，要求失敗。

2. 接下來，要求的方法 （或 HTTP 動詞） 針對檢查**AllowedMethods**項目中所列的方法。 如果這個方法包含在清單中，規則評估會繼續進行。如果沒有出現，然後邀請失敗。

3. 如果要求符合規則在其原始網域和它的方法，要求並沒有進一步的規則會進行評估的程序會選取該規則。 要求可以成功之前，不過，在邀請中指定的任何標頭會針對**AllowedHeaders**項目中所列的標頭檢查。 如果傳送的標頭不相符的可容許的標頭，要求將會失敗。

因為其會出現在邀請內文中的順序處理規則，最佳作法建議您指定最為嚴格的規則與來源第一次在清單中，讓這些會先評估。 指定規則的限制-較少，例如規則，允許所有來源 – 結尾的清單。

### <a name="example--cors-rules-evaluation"></a>範例 – CORS 規則評估

下列範例會顯示部分的要求主體若要設定的儲存空間服務的 CORS 規則作業。 製作要求的詳細資訊，請參閱[設定 Blob 服務屬性](https://msdn.microsoft.com/library/hh452235.aspx)、[設定佇列服務屬性](https://msdn.microsoft.com/library/hh452232.aspx)和[設定資料表服務屬性](https://msdn.microsoft.com/library/hh452240.aspx)。

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

接下來，請考慮下列 CORS 要求︰

要求||| 回應||
---|---|---|---|---
**方法** |**原點** |**要求標頭** |**規則相符項目** |**結果**
**保留** | http://www.contoso.com |x-ms-blob 的內容類型 | 第一個規則 |成功
**取得** | http://www.contoso.com| x-ms-blob 的內容類型 | 第二個規則 |成功
**取得** | http://www.contoso.com| x-ms-blob 的內容類型 | 第二個規則 | 失敗

第一次要求符合的第一個規則 – 原點網域比對允許的來源，方法比對允許的方法，並頁首符合所允許的標頭 –，因此成功。

第二個要求不符合的第一個規則，因為方法不相符的可容許的方法。 不過，不符第二個規則，讓它成功。

第三個要求比對其原始網域和方法，第二個規則，讓任何進一步的規則會進行不評估。 不過， *x-ms-用戶端-要求-識別碼標頭*不是第二個規則，讓要求失敗時，儘管第三個規則的語意就有允許成功。

>[AZURE.NOTE] 雖然此範例顯示之前更嚴格的限制較少的規則，在 [一般] 的最佳作法是第一次列出最為嚴格的規則。

## <a name="understanding-how-the-vary-header-is-set"></a>瞭解如何設定的分標頭

*依*標題是由邀請通知的處理要求伺服器所選取準則的相關的瀏覽器或使用者代理程式的標題欄位的一組標準 HTTP/1.1 標頭。 *依*標題主要用於 proxy、 瀏覽器，並使用它來決定如何回應應該快取的 Cdn 快取。 如需詳細資訊，請參閱[依標題](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)的規格。

當瀏覽器或另一個使用者代理程式快取 CORS 要求的回應時，快取原點網域為允許的起點。 快取作用中時，第二個網域問題同一個要求資源的儲存空間，當使用者代理程式中擷取快取的原點網域。 第二個網域不符合快取的網域，因此，要求失敗時，可以成功。 在某些情況下，Azure 儲存體設定為授權該分頁首**原點**的指示使用者代理程式服務傳送後續 CORS 要求，當要求的網域與快取的來源。

Azure 儲存設定在下列情況中實際的取得/不對要求的**原點***分*標題︰

- 當要求原點完全符合允許的原點 CORS 規則所定義。 若要完全符合，CORS 規則可能不會包含萬用字元 ' * 」 字元。

- 沒有符合要求原點的規則，但 CORS 已啟用來儲存服務。

取得/不對要求符合 CORS 規則，讓所有的來源位置的大小寫，在回應表示允許所有來源、 快取作用中時，使用者代理程式快取可讓任何來源網域的後續要求。

請注意，使用方法取得/不對以外的要求，儲存服務會未設定分標頭中，因為這些方法的回應不會快取使用者代理程式。

下表會指出如何 Azure 儲存空間將回應取得/不對要求根據先前所述的情況下︰

要求|帳戶設定和規則評估的結果|||回應|||
---|---|---|---|---|---|---|---|---
**原點頁首上要求** | **指定此服務 CORS 規則** | **有符合規則，讓所有的來源 (*)** | **有確切的原點相符項目相符的規則** | **回應包含設為原始的分標頭** | **回應包含 Access 控制項-允許的來源: 「*」** | **回應包含 Access 控制項-公開-標頭**
無|無|無|無|無|無|無
無|[是]|無|無|[是]|無|無
無|[是]|[是]|無|無|[是]|[是]
[是]|無|無|無|無|無|無
[是]|[是]|無|[是]|[是]|無|[是]
[是]|[是]|無|無|[是]|無|無
[是]|[是]|[是]|無|無|[是]|[是]


## <a name="billing-for-cors-requests"></a>CORS 要求的帳單

如果您已啟用您的帳戶的儲存空間服務的任何 CORS （藉由呼叫[設定 Blob 服務內容](https://msdn.microsoft.com/library/hh452235.aspx)、[設定佇列服務屬性](https://msdn.microsoft.com/library/hh452232.aspx)，或是[設定表格服務](https://msdn.microsoft.com/library/hh452240.aspx)），則是向收費成功預先要求。 最小化費用，請考慮設定**MaxAgeInSeconds**元素 CORS 規則以較大的值，好讓使用者代理程式快取要求。

不計費失敗預先要求。

## <a name="next-steps"></a>後續步驟

[設定 Blob 服務屬性](https://msdn.microsoft.com/library/hh452235.aspx)

[設定佇列服務屬性](https://msdn.microsoft.com/library/hh452232.aspx)

[設定資料表服務屬性](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C 十字形，資源共用規格](http://www.w3.org/TR/cors/)
