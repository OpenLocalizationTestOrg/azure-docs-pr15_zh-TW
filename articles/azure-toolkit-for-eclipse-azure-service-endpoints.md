<properties
    pageTitle="Azure 服務端點"
    description="說明 Azure 服務端點中設定的蝕 Azure 工具組。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Azure 服務端點 #

Azure 服務端點決定是否應用程式部署到，由全域 Azure 平台管理 Azure 21Vianet 所營運 china （中國），或私人 Azure 平台。 [**服務結束點**] 對話方塊可讓您指定您想要使用哪些服務端點。 若要開啟 [**服務結束點**] 對話方塊中蝕、 按一下 [**視窗**]、 按一下 [**喜好設定**、 展開**Azure**，然後按一下 [**服務端點**。 設定**使用中設定**欄位]，以決定哪些 Azure 服務端點將用於您目前的工作區中的 Azure 專案。

以下顯示**服務結束點**] 對話方塊。

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>若要設定服務端點 ##

在 [**服務結束點**] 對話方塊中，執行其中一個下列動作︰

* 如果您想要使用全域的 Azure 平台，請從**作用中的設定**] 下拉式清單中，選取**windowsazure.com** ，然後按一下**[確定]**。
* 如果您想要使用 Azure 由中國 21vianet 從**作用中的設定**] 下拉式清單中，選取**windowsazure.cn （中國）** ，然後按一下**[確定]**。
* 如果您想要使用的私人 Azure 平台︰
    1. 按一下 [**編輯**]。
    2. 對話方塊隨即開啟，通知您，將會關閉 [**服務結束點**] 對話方塊，並開啟喜好設定設定檔。 按一下**[確定]**。
    3. 在 preferencesets.xml 檔案，建立新的`preferenceset`項目。 為這個新的項目，建立`name`， `blob`， `management`，`portalURL`和`publishsettings`屬性，與您私人的 Azure 平台上的對應加入值。 您可以使用所提供的現有的值`preferenceset`為範本的項目。 **注意**︰ 所使用的值`blob`屬性必須包含 「 blob 」 URL 中的文字。
    4. 儲存並關閉 preferencesets.xml。
    5. 重新開啟**服務結束點**] 對話方塊。
    6. 從**作用中的設定**] 下拉式清單中，選取作用中設定您建立的然後按一下**[確定**]。
    7. 建立您私人的 Azure 平台後`preferenceset`項目，您可以變更**服務結束點**] 對話方塊中的 [**編輯**] 按鈕，即可為其指派的值。 您也可以建立多個私人 Azure 平台`preferenceset`元素，如果您想要。

## <a name="see-also"></a>另請參閱 ##

[針對蝕 azure 工具組][]

[安裝蝕 Azure 工具組][] 

[建立 Azure 中蝕認識全球應用程式][]

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
