<properties
    pageTitle=" Azure 入口網站中建立 Azure 媒體服務帳戶 |Microsoft Azure"
    description="本教學課程中會引導您建立 Azure 媒體服務帳戶 Azure 入口網站的步驟。"
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>建立使用 Azure 入口網站 Azure 媒體服務帳戶

> [AZURE.SELECTOR]
- [入口網站](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [其餘](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 

Azure 入口網站提供快速建立 Azure 媒體服務 （反飛彈系統） 帳戶的方式。 若要存取可讓您可以儲存、 加密、 編碼、 管理及資料流中的 Azure 媒體服務，您可以使用您的帳戶。 在您建立的媒體服務帳戶時，您也建立相關聯的儲存帳戶 （或使用現有） 中的媒體服務帳戶的相同地理區域。

本文說明一些常見的概念，並說明如何建立媒體服務帳戶 Azure 入口網站。

## <a name="concepts"></a>概念

存取媒體服務需要兩個相關聯的帳戶︰

- 媒體服務帳戶。 您的帳戶可讓您存取雲端媒體服務所提供的 Azure 一組。 媒體服務帳戶並不會儲存實際的媒體內容。 請改為媒體內容和媒體處理工作相關的中繼資料儲存在您的帳戶。 在建立帳戶時，您可以選取可用的媒體服務區域。 您選取的地區是儲存您的帳戶的中繼資料記錄的資料中心。

    可用的媒體服務 （反飛彈系統） 區域包括下列︰ 北美歐洲、 西歐、 西美國、 東亞美國、 東南亞、 中式地址，西日本日本東亞。 媒體服務並不會使用相關性群組。
    
    反飛彈系統現在已也可使用下列資料中心︰ 巴西南部、 印度西、 印度南部和印度中央。 您現在可以使用 [Azure 入口網站，以建立媒體服務帳戶，並執行各種此處所述的工作。 不過，即時編碼中未啟用這些資料中心。 此外，並非所有類型的編碼保留單位都中, 找到這些資料中心。
    
    - 巴西南部︰ 僅可使用標準及基本編碼保留單位。
    - 西印度印度南部︰ 

- Azure 儲存體帳戶。 儲存帳戶必須位於相同的地理區域為媒體服務帳戶。 當您建立的媒體服務帳戶時，您可以在同一個區域中，選擇現有的儲存空間帳戶或您可以在同一個區域中建立新的儲存空間帳戶。 如果您刪除媒體服務帳戶時，不會刪除相關的儲存空間帳戶中 blob。

## <a name="create-an-ams-account"></a>建立反飛彈系統帳戶

本節中的步驟會顯示如何建立反飛彈系統帳戶。

1. 在[Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下**[+ 新增** > **Web + 行動** > **媒體服務**。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. 在**建立媒體服務帳戶**輸入必要的值。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. 在 [**帳戶名稱**] 中，輸入新的反飛彈系統帳戶的名稱。 媒體服務帳戶名稱是全部小寫的數字或字母不加空格，，3 至 24 個字元的長度。
    2. 在訂閱中，選取 [之間的不同的 Azure 訂閱，您有權存取]。
    
    2. 在 [**資源] 群組**中，選取新的或現有的資源。  資源群組是共用生命週期、 權限及原則的資源的集合。 深入瞭解[以下](azure-resource-manager/resource-group-overview.md#resource-groups)。
    3. 在 [**位置**] 中，選取會用來儲存您的媒體服務帳戶的媒體及中繼資料記錄的地理區域。 此區域會用來處理和串流媒體。 使用媒體服務區域會出現在下拉式清單方塊中。 
    
    3. 在**儲存帳戶**，選取儲存的帳戶提供媒體內容媒體服務帳戶的 blob 儲存體。 您可以選取現有的儲存空間帳戶中相同的地理區域，為您的媒體服務帳戶，或者您也可以建立儲存帳戶。 在同一個區域中建立新的儲存空間帳戶。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。

        進一步瞭解儲存空間[以下](storage-introduction.md)。

    4. 選取 [**固定至儀表板**，若要查看帳戶部署的進度。
    
7. 按一下 [**建立**底部的表單]。

    帳戶已成功建立之後，狀態會變更為**執行**。 

    ![媒體服務設定](./media/media-services-portal-vod-get-started/media-services-settings.png)

    若要管理您的反飛彈系統帳戶 （例如上, 傳視訊編碼資產、 監控工作進度） 使用 [**設定**] 視窗。

## <a name="manage-keys"></a>管理索引鍵

您需要客戶名稱] 和 [以程式控制方式存取媒體服務帳戶主索引鍵的資訊。

1. 在 Azure 入口網站中，選取您的帳戶。 

    [**設定**] 視窗會顯示在右側。 

2. 在 [**設定**] 視窗中，選取 [**索引鍵**。 

    **管理金鑰**windows 顯示的帳戶名稱，並顯示主要和次要鍵。 
3. 按下的值，複製 [複製] 按鈕。
    
    ![媒體服務索引鍵](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>後續步驟

您現在可以將您的反飛彈系統帳戶上傳檔案。 如需詳細資訊，請參閱[上傳檔案](media-services-portal-upload-files.md)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


