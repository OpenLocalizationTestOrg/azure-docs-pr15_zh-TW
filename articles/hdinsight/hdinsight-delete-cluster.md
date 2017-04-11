<properties
pageTitle="如何刪除 HDInsight 叢集 |Azure"
description="您可以刪除 HDInsight 叢集的各種方式的相關資訊。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="how-to-delete-an-hdinsight-cluster"></a>如何刪除 HDInsight 叢集

計費啟動叢集會建立並當叢集會刪除，而且時累每分鐘，因此不再使用時，您應該一律刪除叢集停駐點之後的 HDInsight 叢集。 在此文件中，您將學習如何使用 Azure 入口網站與 PowerShell 的 Azure Azure CLI 中刪除。

> [AZURE.IMPORTANT] 刪除 HDInsight 叢集並不會刪除叢集相關聯的 Azure 儲存體帳戶。 這個選項可讓您保留並重複使用叢集所儲存的任何資料。

##<a name="azure-portal"></a>Azure 入口網站

1. 登入[Azure 入口網站](https://portal.azure.com)，然後選取您的 HDInsight 叢集。 如果 HDInsight 叢集不已釘選至儀表板，您可以搜尋，以使用 （放大鏡圖示） 上的 [搜尋] 欄位的名稱右側的導覽列。

    ![入口網站的搜尋](./media/hdinsight-delete-cluster/navbar.png)

2. 刀開啟後叢集，選取 [__刪除__] 圖示。 出現提示時，選取__[是]__以刪除叢集。

    ![刪除] 圖示](./media/hdinsight-delete-cluster/deletecluster.png)

##<a name="azure-powershell"></a>Azure PowerShell

從 PowerShell 提示中，請刪除叢集使用下列命令︰

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

__CLUSTERNAME__換成您 HDInsight 叢集的名稱。

##<a name="azure-cli"></a>Azure CLI

在提示，使用下列刪除叢集︰

    azure hdinsight cluster delete CLUSTERNAME
    
__CLUSTERNAME__換成您 HDInsight 叢集的名稱。
