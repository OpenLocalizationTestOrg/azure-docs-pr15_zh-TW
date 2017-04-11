<properties
    pageTitle="清除 Azure CDN 端點 |Microsoft Azure"
    description="瞭解如何從 CDN 端點清除快取的所有內容。"
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="purge-an-azure-cdn-endpoint"></a>清除 Azure CDN 端點

## <a name="overview"></a>概觀

Azure CDN 邊緣節點將快取資產，直到資產的存留時間 (TTL) 到期。  資產的 TTL 到期、 用戶端要求資產從邊緣節點之後，邊緣節點將會擷取新的更新來處理用戶端要求資產複本，然後市集重新整理快取。

有時您可能要清除快取的內容從所有邊緣節點並強制所有擷取新的更新的資產。  這可能是因為更新 web 應用程式，或快速更新資訊不正確的資產。

> [AZURE.TIP] 請注意，只清除清除快取的內容在 CDN edge server。  任何下游的快取，例如 proxy 伺服器與本機瀏覽器快取，可能仍會保留快取檔案的複本。  請務必記住這一點，當您設定檔的存留時間。  您可以強制下游的用戶端，提供一個唯一的名稱每次您更新，或利用[查詢字串快取](cdn-query-string.md)要求最新版的檔案。  

本教學課程中會引導您所有的邊緣節點的端點清除資產。

## <a name="walkthrough"></a>逐步解說

1. 在[Azure 入口網站](https://portal.azure.com)中，瀏覽至含有您想要清除的端點的 CDN 設定檔。

2. 從 CDN 的設定檔刀中，按一下 [清除] 按鈕。

    ![Cdn 到底設定檔刀](./media/cdn-purge-endpoint/cdn-profile-blade.png)

    清除刀隨即會開啟。

    ![Cdn 到底清除刀](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. 在清除刀中，選取您想要清除從 URL] 下拉式清單的服務地址。

    ![清除表單](./media/cdn-purge-endpoint/cdn-purge-form.png)

    > [AZURE.NOTE] 您也可以前往清除刀，即可 CDN 端點刀上的 [**清除**] 按鈕。  在此情況下，[ **URL** ] 欄位會預先填入的特定的結束點的服務地址。

4. 選取您想要清除的邊緣節點的資產。  如果您想要清除所有的資產，按一下 [**清除所有**核取方塊。  否則，請輸入您想要清除每個資產的完整路徑 (例如`/pictures/kitten.png`)**路徑**] 文字方塊中。

    > [AZURE.TIP] 輸入文字以讓您建立多個資產清單後，會顯示更多的**路徑**文字方塊。  您可以從清單中刪除資產，按一下 [省略符號 （...）] 按鈕。
    >
    > 路徑必須符合下列[規則運算式](https://msdn.microsoft.com/library/az24scfc.aspx)的 URL: `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)*\*$";`。  **從 Verizon Azure CDN** （標準和進階版） 的星號 (\*) 可能會使用萬用字元 (例如`/music/*`)。  萬用字元，然後**清除所有**不允許與**Akamai 從 Azure cdn 到底**。
    
5. 按一下 [**清除**] 按鈕。

    ![清除] 按鈕](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] 清除要求大約需要 2-3 分鐘**Verizon 從 Azure CDN** （標準及進階版） 與 7 分鐘與**Akamai 從 Azure CDN**處理。  Azure CDN 有 50 同時在任何時候清除要求的限制。 

## <a name="see-also"></a>另請參閱
- [Azure CDN 端點上預先載入資產](cdn-preload-endpoint.md)
- [Azure CDN REST API 參照-清除或預先載入端點](https://msdn.microsoft.com/library/mt634451.aspx)
