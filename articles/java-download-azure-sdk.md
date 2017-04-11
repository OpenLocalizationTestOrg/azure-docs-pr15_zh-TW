<properties 
    pageTitle="下載 java Azure SDK" 
    description="瞭解如何下載 Azure SDK java，提供 Maven 專案及基本安裝步驟的蝕 Azure Tookit 範例程式碼。" 
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

# <a name="download-the-azure-sdk-for-java"></a>下載 java Azure SDK

本文中的指示下載並安裝 java Azure 文件庫。

**附註︰**Java Azure 文件庫] 下[Apache 授權、 版本 2.0]分散[license]。

## <a name="azure-libraries-for-java---manual-download"></a>Java-手動下載 azure 文件庫

若要手動安裝 java Azure 文件庫，按一下 [ <http://go.microsoft.com/fwlink/?LinkId=690320>下載 ZIP 檔案包含所有的文件庫及所有的相依性]。

在您的 zip 檔案下載到您的電腦，擷取的內容，並使用下列選項的其中一個 JAR 檔案新增至專案︰

* JAR 檔案匯入蝕 Java 專案。

* **建立路徑**Java 專案在中設定蝕包含 JAR 檔案的路徑。

建立路徑蝕中設定的詳細資訊，請參閱[Java 建立路徑]文件蝕網站。

**附註︰**請參閱 license.txt 和 ThirdPartyNotices.txt 檔案的 ZIP 內授權及其他資訊。

## <a name="azure-libraries-for-java---building-with-maven"></a>Azure 文件庫 java-建築 Maven

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>步驟 1-建立使用 Maven 設定您的專案

若要建立 Maven 專案中蝕 java，使用 Azure 的文件庫中的[快速入門 Azure Java 的管理文件庫]的指示執行[maven-getting-started]文章。 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>步驟 2-您 Maven 設定與必要的相依性

用於建立 Maven 設定您的專案之後, 您可以新增必要的相依性，使用語法 pom.xml 檔案，例如下列範例。 請注意，您不需要在下列範例中，新增每個列的相依性您只需要新增的特定的相依性會要求您的專案。

> [AZURE.NOTE] 在每個`<version>`項目在下列範例中，以有效的版本號碼，您可以從[Azure Maven 上的文件庫存放庫]取得取代在此範例中的 「 n.n.n 」 版面配置區。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>安裝蝕 Azure 工具組

本節包含基本指示安裝的蝕; Azure 工具組如需詳細指示，請參閱[安裝的蝕 Azure 工具組]。

### <a name="prerequisites"></a>必要條件

1. Windows operting 系統[的蝕 Azure 工具組中的新]文件中所列。
1. Macintosh 或 Linux operting 系統，列出[的蝕 Azure 工具組中的新]文件中。
1. Java 卻開發人員，（IDE 蝕色) Indigo 或更新版本。 這樣就可以從<http://www.eclipse.org/downloads/>下載。

### <a name="basic-installation-steps"></a>安裝的基本步驟

1. 在蝕，從**[說明**] 功能表中，選取 [**安裝新的軟體**。
1. 輸入網站位置<http://dl.microsoft.com/eclipse> ，然後按**Enter**。
1. 選取要安裝，然後按一下 [**完成**的項目。

Azure 工具組的蝕使用最新版 Azure SDK。 這可以使用 Web 平台安裝程式 (WebPI) <http://go.microsoft.com/fwlink/?LinkID=252838>下載。 不過，如果您沒有安裝，當您建立第一個 Azure 部署專案的蝕 Azure 工具組會自動安裝適當的 Azure SDK 版本。

## <a name="see-also"></a>另請參閱

[針對蝕 azure 工具組]

[安裝蝕 Azure 工具組] 

[建立 Azure 中蝕認識全球應用程式]

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[在 Maven azure 文件庫存放庫]: http://go.microsoft.com/fwlink/?LinkID=286274
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java 建立路徑]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Azure 工具組蝕的新增功能]: http://go.microsoft.com/fwlink/?LinkId=690333
