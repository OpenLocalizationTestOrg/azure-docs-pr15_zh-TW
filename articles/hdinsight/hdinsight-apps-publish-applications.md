<properties
    pageTitle="發佈 HDInsight 應用程式 |Microsoft Azure"
    description="瞭解如何建立及發佈 HDInsight 應用程式。"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>發佈到 Azure Marketplace 的 HDInsight 應用程式

HDInsight 應用程式是使用者可以在 Linux 型 HDInsight 叢集安裝的應用程式。 Microsoft，獨立軟體廠商 (ISV)，或您自己，可以開發這些應用程式。 在本文中，您將學習如何發佈到 Azure Marketplace HDInsight 應用程式。  一般發佈到 Azure Marketplace 的詳細資訊，請參閱[發佈 Azure Marketplace 提供的優惠方案](../marketplace-publishing/marketplace-publishing-getting-started.md)。

HDInsight 應用程式使用*顯示您的授權 (BYOL)*模型，位置應用程式提供者會負責授權給使用者，應用程式，並為其建立的資源，例如 HDInsight 叢集和其 Vm/節點 Azure 要只支付的使用者。 此時，應用程式本身的計費不是透過 Azure 完成。

其他 HDInsight 應用程式相關文章︰

- [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰ 了解如何安裝叢集 HDInsight 應用程式。
- [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰ 了解如何安裝並測試自訂 HDInsight 應用程式。

 
## <a name="prerequisites"></a>必要條件

以提交服務商場您自訂的應用程式，您必須建立並測試您的自訂應用程式。 請參閱下列文章︰

- [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰ 了解如何安裝並測試自訂 HDInsight 應用程式。

您必須也註冊您的開發人員帳戶。 請參閱[發佈 Azure Marketplace 提供的優惠方案](../marketplace-publishing/marketplace-publishing-getting-started.md)，並[建立 Microsoft 開發人員的帳戶](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。

## <a name="define-application"></a>定義應用程式

有兩個步驟發佈 Azure 服務商場應用程式。  第一次您定義**createUiDef.json**檔案，以表示哪些叢集相容於; 是您的應用程式然後將發佈從 Azure 入口網站範本。 以下是範例 createUiDef.json 檔案。

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|功能變數  | 描述   | 可能的值|
|-------|---------------|----------------|
|類型  | 應用程式的相容於叢集類型。    |Hadoop、 HBase、 火花，（或任何一種組合）|
|層級  | 應用程式的相容於叢集層。    |標準、 進階版 （或兩者）|
|版本|  應用程式的相容於 HDInsight 叢集類型。    |3.4|

## <a name="package-application"></a>封裝應用程式

建立包含安裝 HDInsight 應用程式的所有必要的檔案的 zip 檔案。 您必須在[發佈應用程式](#publish-application)中的 zip 檔案。

- [createUiDefinition.json](#define-application)。
- mainTemplate.json。 請參閱在[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)的範例。

    >[AZURE.IMPORTANT] 名稱的應用程式安裝指令碼名稱必須是針對下列格式的特定叢集唯一的。 此外任何安裝及解除安裝指令碼動作應該冪，這表示指令碼可呼叫 repeatly 時產生相同的結果。
    
    >   名稱 」: 「 [concat 函數 (「 色調-安裝-v0 」、 「-'，uniquestring('applicationName')] 」
        
    >請注意，有三個部分的指令碼名稱︰
        
    >   1. 指令碼名稱前置字元，包括應該應用程式的名稱或相關應用程式的名稱。
    >   2. A 「-」 的可讀性。
    >   3. 應用程式的名稱做為參數的唯一字串函數。

    >   例如，成為上述最後︰ 色調-安裝-v0-4wkahss55hlas 保存指令碼的 [動作] 清單中。 範例 JSON 內容，請參閱[https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)。

- 所有必要的指令碼。

> [AZURE.NOTE] （如果有的話，包括 web 應用程式檔案） 的應用程式檔案可以位於任何公開存取的結束點。

## <a name="publish-application"></a>發佈應用程式

請遵循下列步驟，以發佈 HDInsight 應用程式︰

1. [Azure 發佈入口網站](https://publish.windowsazure.com/)登入。
2. 按一下 [建立新的方案範本左邊的 [**方案範本**]。
3. 輸入標題，然後按一下 [**建立新的方案範本**。
3. 按一下 [**加入 Azure 程式的帳戶中建立開發人員中心**以註冊您的公司，如果您尚未這麼做。  請參閱[建立 Microsoft 開發人員的帳戶](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。
4. 按一下 [**定義一些拓撲，即可開始使用**。 解決辦法範本是所有其拓撲"parent"。 您可以定義多個拓撲一個優惠/方案範本中。 當提議放入臨時，其推入所有其拓撲的。 
4. 輸入拓撲名稱，然後按一下 [加號。
5. 輸入新的版本，然後按一下 [加號。
6. 準備[套件應用程式](#package-application)中 zip 檔案上傳。  
7. 按一下 [**要求認證**。 Microsoft 憑證小組會檢視的檔案，並拓撲的認證。

## <a name="next-steps"></a>後續步驟

- [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰ 了解如何安裝叢集 HDInsight 應用程式。
- [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰ 了解如何部署至 HDInsight 解除發佈的 HDInsight 應用程式。
- [使用指令碼的巨集指令的自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)︰ 了解如何安裝其他應用程式使用指令碼的巨集指令。
- [建立 Linux 型 Hadoop 叢集中使用 Azure 資源管理員範本的 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰ 了解如何呼叫建立 HDInsight 叢集資源管理員範本。
- [使用空白的邊緣節點中 HDInsight](hdinsight-apps-use-edge-node.md)︰ 了解如何使用空白的邊緣節點存取 HDInsight 叢集、 測試 HDInsight 應用程式，以及裝載 HDInsight 應用程式。

