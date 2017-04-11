<properties
    pageTitle="覆寫預設 HTTP 行為中使用的規則引擎 Azure CDN |Microsoft Azure"
    description="規則引擎可讓您自訂 HTTP 要求的處理方式以 Azure CDN，例如封鎖的特定類型的內容傳遞、 定義快取的原則，以及修改 HTTP 標頭。"
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

# <a name="override-default-http-behavior-using-the-rules-engine"></a>覆寫預設 HTTP 行為使用規則引擎

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概觀

規則引擎可讓您自訂 HTTP 要求的處理方式，例如封鎖的特定類型的內容傳遞、 定義快取的原則，以及修改 HTTP 標頭。  本教學課程中會示範建立規則，將會變更 CDN 資產的快取行為。  還有視訊內容可在 「[請參閱](#see-also)「] 區段中。

## <a name="tutorial"></a>教學課程

1. 從 CDN 的設定檔刀中，按一下 [**管理**] 按鈕。

    ![Cdn 到底設定檔刀管理] 按鈕](./media/cdn-rules-engine/cdn-manage-btn.png)

    隨即會開啟 [CDN 管理入口網站。

2. 按一下在 [ **HTTP 大型**的索引標籤，後面接著**規則引擎**。

    會顯示新規則的選項。

    ![Cdn 到底新規則選項](./media/cdn-rules-engine/cdn-new-rule.png)

    >[AZURE.IMPORTANT] 多個規則列出的順序會影響的處理方式。 後續的規則，可能會覆寫先前的規則所指定的動作。
    
3. 輸入的名稱**名稱 / 描述**文字方塊。

4. 找出的邀請會套用規則的類型。  根據預設，已選取 [**永遠**符合條件。  您會使用**永遠**在本教學課程，便保持選取。

    ![Cdn 到底符合條件](./media/cdn-rules-engine/cdn-request-type.png)

    >[AZURE.TIP] 有多種類型的相符項目使用下拉式清單中的條件。  按一下藍色的資訊圖示，以符合條件的左邊，將說明目前選取的條件的詳細資料。
    >
    >符合條件的詳細資料的完整清單，請參閱[規則引擎符合條件和功能的詳細資料](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0)。

5.  按一下 [**+**按鈕，可新增的新功能的**功能**。  在左側下拉式清單中，選取 [**強制內部 Max 年齡**]。  在出現的文字方塊中，輸入**300**。  保留其餘的預設值。

    ![Cdn 到底功能](./media/cdn-rules-engine/cdn-new-feature.png)

    >[AZURE.NOTE] 為具有符合條件，按一下左側的新功能的藍色資訊圖示會顯示此功能的詳細資料。  若是**強制內部 Max 年齡**，我們會覆寫控制資產的**快取控制項**和**到期日**標頭時 CDN 邊緣節點將會重新整理從原點資產。  我們的範例 300 秒表示 CDN 邊緣節點將快取資產 5 分鐘左右，再重新整理從原始資產。
    >
    >功能的詳細資料的完整清單，請參閱[規則引擎符合條件和功能的詳細資料](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1)。

6.  按一下 [**新增**] 按鈕，若要儲存新的規則。  現在等候新規則。 經過核准之後，將會從**擱置的 XML**為**作用中的 XML**變更狀態。

    >[AZURE.IMPORTANT] 規則變更可能需要 90 分鐘的時間，才會傳播到 cdn 到底。

## <a name="see-also"></a>另請參閱
* [Azure 星期五︰ Azure CDN 強大的新進階功能](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/)（影片）
* [規則引擎符合條件和功能的詳細資料](https://msdn.microsoft.com/library/mt757336.aspx)
