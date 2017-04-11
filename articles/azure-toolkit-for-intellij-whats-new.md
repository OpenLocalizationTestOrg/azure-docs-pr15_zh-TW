<properties
    pageTitle="Azure IntelliJ 的工具組中的新功能 |Microsoft Azure"
    description="瞭解有關 Azure 工具組中的最新功能的 IntelliJ。"
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Azure 工具組 IntelliJ 的新增功能

## <a name="azure-toolkit-for-intellij-releases"></a>Azure 工具組 IntelliJ 版本

本文包含 IntelliJ 在不同版本和 Azure 工具組最新的更新資訊。

> [AZURE.NOTE] 此外，還有蝕 ide Azure 工具組。 如需詳細資訊，請參閱[針對蝕 Azure 工具組]。

### <a name="august-26-2016"></a>2016 年 8 月 26

Azure 工具組 IntelliJ-2016 年 8 月發行的包含下列增強功能︰

* **自訂 JDK 散佈**。 Azure 工具組的 IntelliJ 現在支援指定及部署 Azure WebApp 容器的任意 JDK 版本︰
  - 除了提供 Azure JDKs，您也可以選擇從寬祖魯文 OpenJDK 版本可供 Azure Azul 系統的選項。
  - 如果您上傳其為 ZIP 檔案至您儲存的帳戶，您也可以指定您自己的 JDK 通訊群組。
* **Azure 總管檢視的增強功能**︰
  - 使用新資源管理員模型的 Azure 虛擬機器管理支援︰ 您可以清單、 建立及刪除資源管理員為基礎的虛擬機器，而不需離開 IDE。
  - 儲存帳戶 blob 管理使用 Azure 的資源管理員補充管理 「 傳統 」 儲存帳戶既有的功能支援。
* **Microsoft JDBC 驅動程式 6.0 for SQL Server**。 此更新包括 Microsoft SQL Server (6.0)，這是最新的 JDBC 驅動程式現在包含為您可以輕鬆地新增 Java 專案文件庫，藉此取代較舊的版本。

### <a name="june-29-2016"></a>2016 年 6 月 29日日

Azure 工具組 IntelliJ-2016 年 6 月發行的包含下列增強功能︰

* **Java 8 需求**。 Azure 工具組的 IntelliJ 現在需要 Java 8，雖然這項要求僅適用於工具組-您的應用程式可以繼續使用 Java Azure 所支援的所有版本。
* **最新的 Java JDKs 支援**。 Java JDKs 的最新版本現在支援 Azure 工具組 IntelliJ。
* **Azure SDK v2.9.1 支援**。 最新版 Azure SDK 現在最小的必要條件，如 IntelliJ Azure 工具組。
* **整合式的範例**。 Azure 工具組的 IntelliJ 現在功能，協助開發人員開始的數個範例應用程式。
* **HDInsight 工具整合**。 Azure 工具組與 IntelliJ 的現在建立的 azure HDInsight 工具。 如需詳細資訊，請參閱[IntelliJ HDInsight 工具外掛程式]。
* **遠端偵錯 Java Web 應用程式**。 Azure 工具組的 IntelliJ 現在支援遠端偵錯 Java Azure 應用程式服務的 web 應用程式。

### <a name="april-12-2016"></a>2016 年 4 月 12日日

Azure 工具組 IntelliJ-2016 年 4 月發行的包含下列增強功能︰

* **Azure SDK v2.9.0 支援**。 最新版 Azure SDK 現在最小的必要條件，如 IntelliJ Azure 工具組。
* **其他合用性、 回應和效能相關 Azure Web App 支援的改良功能**。 在此工具組與 Azure 結果的通訊方式的效能最佳化的數字回應速度更快的 ui 上。
* **若要刪除現有的 Web 應用程式容器內 IntelliJ 從 Azure 中的功能**。 Azure 工具組的 IntelliJ 現在可讓您不需離開 IntelliJ 刪除現有的 Azure Web 容器。

## <a name="see-also"></a>另請參閱 ##

Java Ide 的 Azure 工具套件的相關詳細資訊，請參閱下列連結︰

- [針對蝕 azure 工具組]
  - [安裝蝕 Azure 工具組]
  - [建立 Azure 中蝕認識全球 Web 應用程式]
  - [Azure 工具組蝕的新增功能]
- [針對 IntelliJ azure 工具組]
  - [安裝 IntelliJ Azure 工具組]
  - [建立在 IntelliJ Azure 認識全球 Web 應用程式]
  - *Azure 工具組的 IntelliJ （本文） 中的新功能*

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心]。

<!-- URL List -->

[針對蝕 azure 工具組]: ./azure-toolkit-for-eclipse.md
[針對 IntelliJ azure 工具組]: ./azure-toolkit-for-intellij.md
[建立 Azure 中蝕認識全球 Web 應用程式]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[建立在 IntelliJ Azure 認識全球 Web 應用程式]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安裝蝕 Azure 工具組]: ./azure-toolkit-for-eclipse-installation.md
[安裝 IntelliJ Azure 工具組]: ./azure-toolkit-for-intellij-installation.md
[Azure 工具組蝕的新增功能]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547

[HDInsight 工具外掛程式，IntelliJ。]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md
