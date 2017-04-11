<properties
    pageTitle="限制存取至您的 Azure CDN 內容依國家/地區 |Microsoft Azure"
    description="瞭解如何限制存取至您的 Azure CDN 內容使用地理篩選功能。"
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="akucer"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="Lichard"/>

#<a name="restrict-access-to-your-content-by-country---akamai"></a>限制存取至您的內容，依國家/地區-Akamai

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai 標準](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>概觀

當使用者要求您的內容，根據預設時，內容被 served 無論使用者進行這項要求從的位置。 在某些情況下，您可能會想要限制存取至您的內容，依國家/地區。 本主題說明如何使用 [**地理篩選**功能才能將服務設定為允許或封鎖依國家/地區的存取權。

> [AZURE.IMPORTANT] Verizon 與 Akamai 產品提供相同的地理篩選功能，但不同的使用者介面。 這份文件的**Azure CDN 標準從 Akamai**描述的介面。 地理以篩選**Azure CDN 標準/進階版從 Verizon**，請參閱[限制依國家/地區-Verizon 您內容的存取權](cdn-restrict-access-by-country.md)。

設定此類型的限制的考量事項的相關資訊，請參閱主題的結尾的[考量](cdn-restrict-access-by-country.md#considerations)一節。  

![國家/地區篩選](./media/cdn-filtering/cdn-country-filtering-akamai.png)

##<a name="step-1-define-the-directory-path"></a>步驟 1︰ 定義的路徑

選取您入口網站中的端點，找到 [地理篩選] 索引標籤上找到這項功能的左導覽。

在設定國家/地區篩選時，您必須指定的使用者會被允許或拒絕存取的位置的相對路徑。 您可以套用地理篩選的所有檔案] / [或選取指定目錄路徑 」 / 圖片 / 」 的資料夾。 您也可以套用地理篩選單一檔案藉由指定檔案，並查看斜線離開 「 / pictures/city.png 」。

範例目錄路徑篩選器︰

    /                                 
    /Photos/
    /Photos/Strasbourg/
    /Photos/Strasbourg/city.png

##<a name="step-2-define-the-action-block-or-allow"></a>步驟 2︰ 定義的動作︰ 封鎖或允許

**區塊︰**從指定的國家/地區的使用者將無法存取要求的遞迴路徑的資產。 如果已不設定任何其他國家/地區篩選選項的該位置，然後所有其他允許使用者存取。

**允許︰**僅從指定的國家/地區允許使用者存取要求的遞迴路徑的資產。

##<a name="step-3-define-the-countries"></a>步驟 3︰ 定義國家/地區

選取您想要封鎖或允許路徑的國家/地區。 如需詳細資訊，請參閱[Azure CDN 來自 Akamai 國家/地區碼](https://msdn.microsoft.com/library/mt761717.aspx)。

例如，封鎖 /Photos/Strasbourg/的規則會篩選檔案，包括︰

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>國家/地區碼

[**地理篩選**功能會使用國家/地區碼來定義國家/地區的要求要允許或封鎖受到保護的目錄。 [Azure CDN 來自 Akamai 國家/地區碼](https://msdn.microsoft.com/library/mt761717.aspx)中，您會發現的國家/地區碼。 

##<a id="considerations"></a>考量

- 將其最多可能需要幾分鐘，讓您的國家/地區篩選設定所做的變更才能生效。
- 這項功能不支援萬用字元 (例如，「 * 」)。
- 相對路徑相關聯的地理篩選設定將會遞迴套用至該路徑。
- 只有一個規則套用至相同的相對路徑 （您無法建立指向相同的相對路徑的多個國家/地區篩選。 不過，資料夾可能會有多個國家/地區的篩選。 這是因為遞迴性質的國家/地區篩選。 換句話說，先前設定的資料夾的子資料夾，可以指定不同的國家/地區篩選。

