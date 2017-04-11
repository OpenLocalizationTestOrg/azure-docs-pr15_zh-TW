<properties
    pageTitle="顯示蝕 java Azure 文件庫封裝 Javadoc 內容"
    description="顯示 Javadoc 內容蝕 Azure 文件庫的方式。"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>顯示蝕 java Azure 文件庫封裝 Javadoc 內容 #

Java Azure 文件庫的 Javadoc 內容可以檢視蝕環境中，建立關聯，以 Javadoc 內容 java Azure 文件庫。 下列步驟將告訴您如何使用內蝕這項功能。

此程序假設您已新增至您建立路徑 java Azure 文件庫。

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>以顯示 java Azure 文件庫中蝕 Javadoc 內容 ##

* 蝕的 [專案總管] 中的專案，**參照文件庫**] 區段中開啟操作功能表 Azure 文件庫的 Java JAR。 例如， **microsoft windowsazure-api 0.1.0.jar** （版本號碼可能不同，取決於您所安裝的版本）。
* 按一下 [**屬性**]。
* 在 [**屬性**] 對話方塊的左窗格中，按一下 [ **Javadoc 位置**]。 [ **Javadoc 位置**] 對話方塊隨即顯示。
* 您可以指定**Javadoc URL**或**Javadoc 在封存中**。
    * 如果您選擇要指定**Javadoc URL**，請使用的 Url，例如**http://dl.windowsazure.com/javadoc**或**http://dl.windowsazure.com/storage/javadoc**。
    * 如果您選擇使用**Javadoc 在封存中**，您可以指定外部的檔案或工作區檔案。
    選擇並瀏覽/驗證視。 下列範例會將 java Azure 文件庫，與對應 Javadoc JAR 的已命名**c:\MyAzureJARs**資料夾本機下載。
    ![][ic553487]
* *可省略*︰ 按一下 [**驗證**。 無法顯示潛在問題 Javadoc JAR 以下所示。
* 按一下**[確定]**。

當文件庫相關聯，Javadoc 內容應該顯示在您的蝕 IDE。 例如，如果`blob`類型定義`CloudBlockBlob`程式碼，以下是當您輸入時，出現 Javadoc 內容的範例`blob.acquireLease`程式碼︰

![][ic553488]

## <a name="see-also"></a>另請參閱 ##

[針對蝕 azure 工具組][]

[建立 Azure 中蝕認識全球應用程式][]

[安裝蝕 Azure 工具組][] 

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png