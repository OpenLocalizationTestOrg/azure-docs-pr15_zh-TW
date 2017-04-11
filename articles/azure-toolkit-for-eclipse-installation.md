<properties
    pageTitle="安裝蝕 Azure 工具組 |Microsoft Azure"
    description="瞭解如何安裝的蝕 Azure 工具組。"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>安裝蝕 Azure 工具組

Azure 工具組的蝕提供範本和功能，可讓您輕鬆建立、 開發、 測試和部署 Azure 使用蝕的開發環境的應用程式。 Azure 工具組蝕的是在開啟的來源的專案，其來源程式碼是從專案網站上，將下列 URL GitHub MIT 授權] 下的可用︰

<https://github.com/microsoft/azure-tools-for-java>

下列步驟將告訴您如何安裝的蝕 Azure 工具組。

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>若要安裝的蝕 Azure 工具組

1. 啟動蝕。

1. 蝕開啟時，按一下 [**說明**] 功能表，然後按一下**安裝新的軟體**，如下圖所示。

    ![安裝蝕 Azure 工具組][01]

1. 在 [**可用的軟體**] 對話方塊中**使用**[文字] 方塊中，輸入**http://dl.microsoft.com/eclipse**再按下**Enter**鍵。

1. 在 [**名稱**] 窗格中，核取**的蝕 Azure 工具組**，並取消核取 [**找到所需的軟體，安裝所有更新網站期間的連絡人**。 畫面看起來應該類似下列動作︰

    ![安裝蝕 Azure 工具組][02]

1. 如果您展開**的蝕 Azure 工具組**，您會看到下列項目︰

    * **Java 應用程式的深入見解外掛程式**︰ 這個元件，可讓您使用 Azure 的遙測記錄及分析您的應用程式與服務伺服器執行個體。
    * **Azure 存取控制服務篩選器**︰ 這個元件提供支援驗證應用程式使用者 Azure ACS、 啟用單一登入案例和 externalizing 身分識別邏輯的應用程式。
    * **Azure 常見外掛程式**︰ 這個元件提供其他工具組元件所需的一般功能。
    * **Azure Explorer 蝕**︰ 這個元件提供其他工具組元件所需的一般功能。
    * **Java 蝕 azure 外掛程式**︰ 這個元件提供的支援開發協助建立、 測試和部署 Java 應用程式的 Microsoft Azure 雲端中蝕及透過命令列的專案。
    * **Azure java Web 應用程式增益集**︰ 這個元件部署到 Microsoft Azure Web App 容器 Java web 應用程式提供支援。
    * **SQL server Microsoft JDBC 驅動程式 4.2**︰ 這個元件提供 JDBC API SQL Server 和 Microsoft Azure SQL 資料庫 Java 平台 Enterprise Edition 8。
    * **封裝 Apache Qpid 用戶端文件庫的 JMS**︰ 這個元件提供 Apache Qpid 專案可讓您的應用程式使用 AMQP 訊息 Microsoft Azure 中從 JMS 用戶端元件。
    * **Microsoft Azure java 的文件庫的套件**︰ 這個元件提供 Api 的存取 Microsoft Azure 服務，例如儲存空間、 服務匯流排、 服務執行階段等等。

1. 按一下 [**下一步**]。 （如果您遇到異常的延遲，安裝該工具組時，請確定**所有更新網站期間的連絡人安裝找到所需的軟體**取消核取。）

1. 在 [**安裝的詳細資料**] 對話方塊中，按一下 [**下一步**]。

    ![檢閱安裝的詳細資訊][03]

1. 在 [**檢閱授權**] 對話方塊中，檢閱授權合約的條款。 如果您接受授權合約的條款，按一下 [**我接受授權合約的條款**，然後按一下 [**完成]**。 （其餘的步驟假設您接受授權合約的條款。 如果您不接受授權合約的條款，結束安裝程序。）

    ![檢閱授權][04]

    蝕會下載並安裝必要的套件。

    ![安裝進度][05]

1. 如果提示您重新啟動蝕以完成安裝，請按一下 [**是**]。

    ![重新啟動提示][06]

## <a name="see-also"></a>另請參閱

Java Ide 的 Azure 工具套件的相關詳細資訊，請參閱下列連結︰

- [針對蝕 azure 工具組]
  - *安裝蝕 （本文） Azure 工具組*
  - [建立 Azure 中蝕認識全球 Web 應用程式]
  - [Azure 工具組蝕的新增功能]
- [針對 IntelliJ azure 工具組]
  - [安裝 IntelliJ Azure 工具組]
  - [建立在 IntelliJ Azure 認識全球 Web 應用程式]
  - [Azure 工具組 IntelliJ 的新增功能]

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心]。

<!-- URL List -->

[針對蝕 azure 工具組]: ./azure-toolkit-for-eclipse.md
[針對 IntelliJ azure 工具組]: ./azure-toolkit-for-intellij.md
[建立 Azure 中蝕認識全球 Web 應用程式]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[建立在 IntelliJ Azure 認識全球 Web 應用程式]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[安裝 IntelliJ Azure 工具組]: ./azure-toolkit-for-intellij-installation.md
[Azure 工具組蝕的新增功能]: ./azure-toolkit-for-eclipse-whats-new.md
[Azure 工具組 IntelliJ 的新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

