<properties
   pageTitle="縮放比例 web 服務 |Microsoft Azure"
   description="瞭解如何增加並行，新增新的結束點來縮放 web 服務。"
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="azure 電腦學習，web 服務 operationalization，縮放比例、 端點，並行"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>縮放比例 Web 服務

>[AZURE.NOTE] 本主題將說明適用於傳統電腦學習 Web 服務的技巧。 

根據預設，每個已發佈的 Web 服務設定為支援 20 同時的要求，並可高為 200 同時的要求。 雖然 Azure 傳統入口網站提供這個值的方式，Azure 電腦學習會自動最佳化設定以提供您的 web 服務的最佳效能，會忽略入口網站的值。 

如果您計劃以呼叫超過 200 的最大值同時呼叫值較高負載 API 會支援，您應該在相同的 Web 服務上建立多個端點。 您可以再隨機分散負載所有。

## <a name="add-new-endpoints-for-same-web-service"></a>加入新的相同的 web 服務的端點

Web 服務的縮放比例是常見工作。 若要縮放的一些原因是，支援超過 200 封同時的要求、 提高可用性透過多個端點，或提供個別的結束點的 web 服務。 您可以藉由新增[Azure 傳統入口網站](https://manage.windowsazure.com/)，或是[Azure 電腦學習 Web 服務](https://services.azureml.net/)入口網站的同一個 Web 服務的其他端點增加小數位數。

如需有關新增新的端點的詳細資訊，請參閱[建立結束點](machine-learning-create-endpoint.md)。

請記住，使用高並行計算可能會危害如果您不呼叫 API 相對率。 您可能會看到月逾時及/或特殊圖文集的延遲時間如果您將在高負載設定 API 的較低的載入。

同步 Api 通常用的情況下低的延遲想要的位置。 延遲以下所示的時間完成一個要求時，API 所需的時間並沒有任何網路延遲帳戶。 例如，假設您有 50 ms 延遲的 API。 若要完全使用可用的容量流速層級高和 Max 同時呼叫 = 20，您需要呼叫此 API 20 * 1000 / 50 = 400 時間秒。 200 的最大值同時呼叫進一步擴充，可讓您撥打秒，假設 50 ms 延遲的 API 4000 時間。

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
