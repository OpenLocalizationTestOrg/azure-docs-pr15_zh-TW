<properties
    pageTitle="使用空白的邊緣節點中 HDInsight |Microsoft Azure"
    description="如何新增可做為用戶端的 HDInsight 叢集 ampty 邊緣節點並測試主機 HDInsight 應用程式。"
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
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>使用空白的邊緣節點中 HDInsight

瞭解如何新增空白的邊緣節點到 Linux 型 HDInsight 叢集。 空白的邊緣節點是 Linux 虛擬機器具有相同的用戶端工具安裝並設定與 headnodes，但執行任何 hadoop 服務。 您可以使用邊緣節點存取叢集、 測試您的用戶端應用程式，並裝載您的用戶端應用程式。 

您可以新增現有的 HDInsight 叢集，當您建立叢集為新叢集空白邊緣節點。 新增空白的邊緣節點完成使用 Azure 資源管理員範本。  下列範例會示範如何完成使用範本︰

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

範例所示，您可以選擇性地呼叫要執行其他設定，例如安裝[Apache 色調](hdinsight-hadoop-hue-linux.md)邊緣節點中的[指令碼的巨集指令](hdinsight-hadoop-customize-cluster-linux.md)。

建立邊緣節點之後，您可以連線到使用 SSH，在邊緣節點，並執行存取 Hadoop 叢集 HDInsight 中的用戶端工具。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>新增現有的叢集邊緣節點

在此區段中，您可以使用資源管理員範本將邊緣節點新增至現有的 HDInsight 叢集。  在[GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode)找資源管理員範本。 資源管理員範本通話位於 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh 指令碼動作指令碼。 指令碼不會執行任何動作。  這是示範呼叫的指令碼動作，從資源管理員範本。

**若要將空白邊緣節點新增至現有的叢集**

1. 如果您還沒有尚未，請建立 HDInsight 叢集。  請參閱[Hadoop 教學課程︰ 開始使用 HDInsight Linux 型 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
2. 按一下 [登入 Azure 和 Azure 入口網站中開啟 Azure 資源管理員範本下列圖像。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 設定下列內容︰

    - CLUSTERNAME︰ 輸入現有 HDInsight 叢集的名稱。
    - EDGENODESIZE︰ 選取其中一個 VM 大小。
    - EDGENODEPREFIX︰ 為**新**的預設值。  使用預設值，會**新 edgenode**邊緣節點名稱。  您可以自訂從入口網站的前置字元。 您也可以自訂範本的完整名稱。


4. 按一下**[確定**] 儲存變更。
5. 在 [**資源] 群組**中，選取 [資源群組]。
6. 按一下 [**校閱法律條款**，，然後按一下 [**購買**。
7. 選取 [**固定至儀表板**]，然後按一下 [**建立**。

## <a name="add-an-edge-node-when-creating-a-cluster"></a>建立叢集時新增邊緣節點

在此區段中，您可以使用資源管理員範本建立 HDInsight 叢集邊緣節點。  資源管理員範本可以公用[Azure Blob 儲存容器](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)中找到。 資源管理員範本通話位於 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh 指令碼動作指令碼。 指令碼不會執行任何動作。  這是示範呼叫的指令碼動作，從資源管理員範本。

**若要將空白邊緣節點新增至現有的叢集**

1. 如果您還沒有尚未，請建立 HDInsight 叢集。  請參閱[Hadoop 教學課程︰ 開始使用 HDInsight Linux 型 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
2. 按一下 [登入 Azure 和 Azure 入口網站中開啟 Azure 資源管理員範本下列圖像。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 設定下列內容︰
        
    - CLUSTERNAME︰ 輸入若要建立新的叢集的名稱。
    - CLUSTERLOGINUSERNAME︰ 輸入 Hadoop HTTP 使用者名稱。  預設名稱是**管理員**。
    - CLUSTERLOGINPASSWORD︰ 輸入 Hadoop HTTP 使用者的密碼。
    - SSHUSERNAME︰ 輸入 SSH 使用者名稱。 預設名稱是**sshuser**。
    - SSHPASSWORD︰ 輸入 SSH 使用者的密碼。
    - 位置︰ 請輸入資源群組的名稱、 叢集及預設儲存帳戶的位置。
    - CLUSTERTYPE︰ 預設值是**hadoop**。
    - CLUSTERWORKERNODECOUNT︰ 預設值為 2。
    - EDGENODESIZE︰ 選取其中一個 VM 大小。
    - EDGENODEPREFIX︰ 為**新**的預設值。  使用預設值，會**新 edgenode**邊緣節點名稱。  您可以自訂從入口網站的前置字元。 您也可以自訂範本的完整名稱。

4. 按一下**[確定**] 儲存變更。
5. 在 [**資源] 群組**中，輸入新的資源群組名稱。
6. 按一下 [**校閱法律條款**，，然後按一下 [**購買**。
7. 選取 [**固定至儀表板**]，然後按一下 [**建立**。 


## <a name="access-an-edge-node"></a>存取邊緣節點

邊緣節點 ssh 端點是<EdgeNodeName>。<ClusterName>-ssh.azurehdinsight.net:22。  例如，新-edgenode.myedgenode0914-ssh.azurehdinsight.net:22。

邊緣節點會顯示為 Azure 入口網站上的應用程式。  入口網站可讓您存取使用 SSH 邊緣節點的資訊。

**驗證 edge 節點 SSH 端點**

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 開啟 edge 節點 HDInsight 叢集。
3. 您可以按一下 [**應用程式**從叢集刀。 您應該看到邊緣節點。  預設名稱是**新 edgenode**。
4. 按一下邊緣節點。 您應該會看到 SSH 結束點。

**若要使用登錄區的邊緣節點**

5. 使用 SSH 連線至邊緣節點。  請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 Unix，或 OS X 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)，或[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)。
6. 您已連線至使用 SSH 邊緣節點之後，請使用下列命令以開啟登錄區主控台︰

        hive
7. 執行下列命令以在叢集顯示登錄區資料表︰

        show tables;

## <a name="delete-an-edge-node"></a>刪除邊緣節點

從 Azure 入口網站，您可以刪除邊緣節點。

**若要存取邊緣節點**

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 開啟 edge 節點 HDInsight 叢集。
3. 您可以按一下 [**應用程式**從叢集刀。 您應該看到邊緣節點的清單。  
4. 以滑鼠右鍵按一下您要刪除邊緣節點，然後按一下 [**刪除**。
5. 按一下 [ **]**以確認。

## <a name="next-steps"></a>後續步驟

本文中，您已經學會如何新增邊緣節點以及如何存取邊緣節點。 若要深入瞭解，請參閱下列文章︰

- [安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md)︰ 了解如何安裝叢集 HDInsight 應用程式。
- [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰ 了解如何部署至 HDInsight 解除發佈的 HDInsight 應用程式。
- [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)︰ 了解如何發佈 Azure Marketplace 的您自訂 HDInsight 應用程式。
- [MSDN︰ 安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)︰ 了解如何定義 HDInsight 應用程式。
- [使用指令碼的巨集指令的自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)︰ 了解如何安裝其他應用程式使用指令碼的巨集指令。
- [建立 Linux 型 Hadoop 叢集中使用的資源管理員範本的 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰ 了解如何呼叫建立 HDInsight 叢集資源管理員範本。

