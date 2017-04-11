<properties
    pageTitle="管理 Hadoop 叢集使用 Azure CLI |Microsoft Azure"
    description="如何使用 Azure CLI 管理 Hadoop 叢集 HDIsight 中"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>管理 Hadoop 叢集中使用 CLI Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

瞭解如何使用[Azure 命令列介面](../xplat-cli-install.md)來管理 Hadoop 叢集中 Azure HDInsight。 Azure CLI Node.js 中實作。 它可以使用任何支援 Node.js，包括 Windows、 Mac 和 Linux 的平台上。

本文涵蓋只將 Azure CLI 使用 HDInsight。 如何使用 Azure CLI 的一般指南，請參閱[安裝及設定 Azure CLI][azure-command-line-tools]。

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure CLI** ，請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md)安裝與設定的資訊。
- **連線至 Azure**，使用下列命令︰

        azure login

    如需有關使用公司或學校帳戶驗證的詳細資訊，請參閱[連線至 Azure CLI 從 Azure 訂閱](xplat-cli-connect.md)。
    
- **切換至 Azure 資源管理員模式**，請使用下列命令︰

        azure config mode arm

若要相關說明，請使用**-h**切換。  例如︰

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>建立叢集

請參閱[建立 Linux 為基礎的叢集中使用 CLI Azure HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)。

##<a name="list-and-show-cluster-details"></a>清單，然後顯示叢集詳細資料
使用下列命令清單，然後顯示叢集詳細資料︰

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI。CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>刪除叢集

若要刪除叢集使用下列命令︰

    azure hdinsight cluster delete <Cluster Name>

您也可以刪除包含叢集的資源群組來刪除叢集。 請注意，這將會刪除包括預設儲存帳戶] 群組中的所有資源。

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>縮放比例叢集

若要變更的 Hadoop 叢集大小︰

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>啟用或停用叢集 HTTP 存取

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>啟用或停用叢集 RDP 存取

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>後續步驟
本文中，您已經學會如何執行不同 HDInsight 叢集系統管理工作。 若要深入瞭解，請參閱下列文章︰

* [藉由使用 [Azure 入口網站管理 HDInsight] [hdinsight-admin-portal]
* [使用 PowerShell 的 Azure 管理 HDInsight] [hdinsight-admin-powershell]
* [開始使用 Azure HDInsight] [hdinsight-get-started]
* [如何使用 Azure CLI] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "清單，然後顯示叢集"
