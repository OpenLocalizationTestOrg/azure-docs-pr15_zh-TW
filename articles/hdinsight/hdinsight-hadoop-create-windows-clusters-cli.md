<properties
   pageTitle="建立 Windows 型 Hadoop 叢集中使用 CLI Azure HDInsight"
    description="瞭解如何建立使用 Azure CLI 叢集 Azure HDInsight。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>建立 Windows 型 Hadoop 叢集中使用 CLI Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

瞭解如何建立使用 CLI Azure HDInsight 叢集。 其他叢集建立的工具和功能按一下此頁面上方的索引標籤上選取，或查看[叢集建立方法](hdinsight-provision-clusters.md#cluster-creation-methods)。

##<a name="prerequisites"></a>先決條件︰

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


本文中的指示進行之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure CLI**。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="connect-to-azure"></a>連線至 Azure

連線至 Azure 中使用下列命令︰

    azure login

如需有關使用公司或學校帳戶驗證的詳細資訊，請參閱[連線至 Azure CLI 從 Azure 訂閱](../xplat-cli-connect.md)。

切換至 ARM 模式中使用下列命令︰

    azure config mode arm

若要取得說明，請使用**-h**切換。  例如︰

    azure hdinsight cluster create -h

##<a name="create-clusters"></a>建立叢集

您可以建立 HDInsight 叢集之前，您必須具有 Azure 資源管理 (ARM) 和 Azure Blob 儲存體帳戶。 若要建立 HDInsight 叢集，您必須指定下列項目︰

- **Azure 資源群組**︰ 必須 Azure 資源群組內建立資料湖分析帳戶。 Azure 資源管理員可讓您使用的應用程式群組中的資源。 您可以部署、 更新或刪除的所有資源在單一、 協同作業應用程式。

    列出您的訂閱中的資源群組︰

        azure group list

    若要建立新的資源群組︰

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight 叢集名稱**

- **位置**︰ 其中一個支援 HDInsight 叢集 Azure 資料中心。 如需支援位置的清單，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

- **預設儲存帳戶**︰ HDInsight 作為預設檔案系統中使用 Azure Blob 儲存容器。 您可以建立 HDInsight 叢集前需要 Azure 儲存體帳戶。

    若要建立新的 Azure 儲存體帳戶︰

        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

    > [AZURE.NOTE]儲存帳戶必須組與資料中心的 HDInsight 中。
    > 無法 ZRS，儲存帳戶類型，因為 ZRS 不支援表格。

    使用 [Azure 入口網站中建立 Azure 儲存體帳戶資訊，請參閱 [建立、 管理，或刪除儲存的帳戶] [azure-建立-storageaccount]。

    如果您已經有儲存帳戶，但不是知道客戶名稱] 和 [帳戶金鑰]，您可以使用下列命令以擷取的資訊︰

        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

    使用 Azure 入口網站取得資訊的詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account#manage-your-storage-account)的 [管理您的儲存空間帳戶 」 一節。

- **（選擇性） 預設 Blob 容器**︰**建立 azure hdinsight 叢集**] 命令會建立容器，如果不存在。 如果您選擇預先建立的容器，您可以使用下列命令︰

    azure 存放容器建立-帳戶名稱 「<Storage Account Name>」-帳戶金鑰<Storage Account Key>[ContainerName]

準備好的儲存空間帳戶之後，您已準備好建立叢集︰


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##<a name="create-clusters-using-configuration-files"></a>建立使用設定檔的叢集
一般而言，您建立 HDInsight 叢集、 執行工作]，然後刪除叢集縮減成本。 命令列介面可讓您儲存檔案]，將設定的選項，讓您可以重複使用它每次建立叢集。  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

範例︰ 建立包含時建立叢集執行指令碼動作的設定檔。

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##<a name="create-clusters-with-script-action"></a>使用指令碼動作建立叢集

建立包含時建立叢集執行指令碼動作的設定檔。

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

建立叢集與指令碼的巨集指令

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


一般的指令碼動作的詳細資訊，請參閱 <<c0>自訂 HDInsight 叢集使用指令碼動作 (Linux)。


## <a name="create-clusters-using-arm-templates"></a>建立叢集使用手錶範本

您可以使用 CLI 呼叫 ARM 範本來建立叢集。 請參閱[Azure CLI 部署](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli)。

## <a name="see-also"></a>另請參閱

- [Azure HDInsight 快速入門](hdinsight-hadoop-linux-tutorial-get-started.md)-瞭解如何開始使用您的 HDInsight 叢集
- [送出 Hadoop 工作以程式設計方式](hdinsight-submit-hadoop-jobs-programmatically.md)-瞭解程式設計的方式將工作提交給 HDInsight
- [管理 Hadoop 叢集中使用 CLI Azure HDInsight](hdinsight-administer-use-command-line.md)
- [使用 Azure CLI Mac 版、 Linux，以及 Windows Azure 服務管理](../virtual-machines-command-line-tools.md)
