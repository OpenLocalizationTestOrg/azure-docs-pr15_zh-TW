<properties
   pageTitle="Azure 資源檔案總管 |Microsoft Azure"
   description="Azure 資源檔案總管，以及如何使用它來檢視及更新部署透過 Azure 資源管理員說明"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Azure 資源檔案總管來檢視及修改資源
[Azure 資源檔案總管](https://resources.azure.com)是位於您已經建立您的訂閱中的資源的絕佳工具。 藉由使用這項工具，您可以瞭解如何資源的結構，並查看分派給每個資源的內容。 您可以進一步瞭解的 REST API 作業和 PowerShell 指令程式所使用的資源類型，，您可以發行透過介面的命令。 當您正在建立資源管理員範本，因為它可讓您可以檢視現有的資源的內容，資源檔案總管就會特別有用。

[資源檔案總管] 工具的來源會提供[github](https://github.com/projectkudu/ARMExplorer)，如果您需要實作類似的行為，在您的應用程式中提供的寶貴的參照。

## <a name="view-resources"></a>檢視資源
瀏覽至[https://resources.azure.com](https://resources.azure.com)和[Azure 入口網站](https://portal.azure.com)，您可以使用相同的認證登入。

載入之後，在左側的樹狀檢視可讓您向下切入至您的訂閱和資源群組︰

![樹狀檢視](./media/resource-manager-resource-explorer/are-01-treeview.png)

當您切入資源群組，您會看到的資源群組中的提供者︰

![提供者](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

您可以從該處開始切入至資源執行個體。 在下面的螢幕擷取畫面，您可以看到`sltest`樹狀檢視中的 SQL Server 執行個體。 在右側，您可以看到 REST API 邀請，您可以使用該資源的相關資訊。 瀏覽至 [資源] 節點，資源檔案總管會自動將 GET 要求擷取資源的相關資訊。 在下方 URL 較大的文字區域，您會看到 API 的回應。 

當您熟悉資源管理員範本的本文內容開始看起來很熟悉 ！ [**屬性**] 區段的回應比對範本的 [**屬性**] 區段中，您可以提供的值。

![sql server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

資源檔案總管可讓您將向下切入以探索若是 SQL 資料庫伺服器的子資源，有子資源資料庫及防火牆規則之類的。

探索資料庫會顯示我們該資料庫的屬性。 在下面的螢幕擷取畫面我們可以看到的資料庫`edition`是`Standard`和`serviceLevelObjective`（或資料庫層） 是`S1`。

![sql 資料庫](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>變更資源

一旦您瀏覽到資源，您可以選取 [編輯] 按鈕，將可編輯的 JSON 內容。 然後您可以使用資源檔案總管編輯 JSON 和傳送放入要求變更資源。 例如下, 圖顯示變更為資料庫層`S0`:

![資料庫-放入要求](./media/resource-manager-resource-explorer/are-05-database-put.png)

選取 [**保留**您提交要求。 

一旦已提交要求資源檔案總管重新發出重新整理狀態 GET 要求。 在此情況下，我們可以看到的`requestedServiceObjectiveId`已更新，這與不同`currentServiceObjectiveId`指出進行中的縮放比例的作業。 您可以按一下 [開始] 按鈕，以手動重新整理狀態。

![資料庫-取得 request2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>在 [資源上執行動作

[**動作**] 索引標籤可讓您以查看並執行其他的其餘部分作業。 例如，當您選取網站的資源，[動作] 索引標籤會顯示一長串的可用的作業，其中一些如下所示。

![web 文章要求](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>叫用透過 PowerShell API
資源檔案總管中的 PowerShell] 索引標籤會顯示 cmdlet，以使用互動目前瀏覽您的資源。 根據您所選取的資源類型，顯示的 PowerShell 指令碼的範圍是從簡單的指令程式 (例如`Get-AzureRmResource`和`Set-AzureRmResource`) 若要更複雜的 cmdlet （例如交換網站上的位置）。 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

如需 PowerShell 的 Azure cmdlet 的詳細資訊，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](powershell-azure-resource-manager.md)

## <a name="summary"></a>摘要
當您使用與資源管理員，資源檔案總管可以是非常實用的工具。 它會找出方法使用 PowerShell 來查詢，並在進行變更的絕佳方式。 如果您使用 REST API 則開始，以及開始撰寫程式碼之前，以快速測試 API 呼叫的好方法。 然後，如果您正在撰寫範本很適合用於了解資源階層，並尋找要放置設定-您可以在入口網站中進行變更，然後找到 [資源檔案總管中的 [對應的項目 ！

如需詳細資訊，請觀看[頻道 9 David Ebbo 史 Hanselman 與視訊](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


