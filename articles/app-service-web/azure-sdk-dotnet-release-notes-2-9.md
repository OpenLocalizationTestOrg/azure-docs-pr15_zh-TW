<properties 
   pageTitle="Azure SDK.NET 2.9 版本資訊" 
   description="Azure SDK.NET 2.9 版本資訊" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

# <a name="azure-sdk-for-net-29-release-notes"></a>Azure SDK.NET 2.9 版本資訊

##<a name="overview"></a>概觀

這份文件包含 Azure SDK.NET 2.9 發行版本資訊。 

如需詳細瞭解這個版本中更新的詳細資訊，請參閱[Azure SDK 2.9 公告張貼的文章](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)。

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Visual Studio 2015 更新 2 和 Visual Studio 「 15 」 azure SDK 2.9 預覽
 
此更新包括下列錯誤修正︰

- 與相關的問題將 API 用戶端產生的程式碼產生資料夾的名稱及/或命名空間名稱為字串 「 未知 Type 」 會顯示在放入產生的程式碼。
- 若要在其中驗證資訊已傳遞至排程器佈建程序失敗的排程 WebJobs 與相關的問題。

此更新包含下列新功能︰

- 支援的應用程式服務佈建] 對話方塊的 [服務] 索引標籤中的第二個應用程式服務。 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Visual Studio 2015 更新 2 azure 資料湖工具
 
此更新包括下列各項︰

- Visual Studio **Azure 資料湖工具**現在併入 Azure SDK.NET 版本。 當您安裝 Azure SDK 時，會自動安裝工具。 

    工具經常更新，請[在這裡](http://aka.ms/datalaketool)以取得更新。

- **伺服器總管**現在可讓您檢視所有並建立某些 U SQL 中繼資料的項目。 如需詳細資訊，請參閱[此](https://azure.microsoft.com/documentation/services/data-lake-analytics/)部落格。


##<a name="hdinsight-tools"></a>HDInsight 工具 

Visual Studio **HDInsight 工具**現在支援 HDInsight 版本 3.3，包括顯示 Tez 圖形和其他語言的修正。


##<a name="azure-resource-manager"></a>Azure 資源管理員 

此版本加入[KeyVault](../resource-manager-keyvault-parameter.md)支援 ARM 範本。

##<a name="see-also"></a>另請參閱

[Azure SDK 2.9 公告文章](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
