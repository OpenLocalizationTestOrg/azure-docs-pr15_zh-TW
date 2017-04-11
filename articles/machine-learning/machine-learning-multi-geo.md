<properties
   pageTitle="多重地理說明文 |Microsoft Azure"
   description="瞭解如何建立工作區及發佈 web 服務 Azure 不同從南部中央美國 (SCUS) 區域中 Azure 的區域。"
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>多重地理協助文件

本文將說明如何建立工作區和其他 Azure 區域中的 [發佈 web 服務。

## <a name="create-a-workspace"></a>建立工作區

1. 登入 Azure 傳統入口網站。

2.  按一下 [ **+ 新增** > **資料服務** > **電腦學習** > **快速建立**。  在 [**位置]**選取另一個區域，例如**東南亞**。
![多重地理協助圖像 1][1]
3. 選取工作區中，然後按 [**登入毫升 Studio**。
![多重地理協助圖像 2][2]

4. 現在，您有工作區就像其他任何工作區，您可以使用的其他區域。 若要在您的工作區之間切換，請查看右上方您的畫面。 按一下下拉式清單，選取 [區域]，然後選取工作區。 所有項目是本機工作區的區域。比方說，所有您建立的工作區的 web 服務會在工作區位於相同的地區。
![多重地理協助圖像 3][3]

## <a name="open-an-experiment-from-gallery"></a>從圖庫中開啟實驗

如果您從圖庫中開啟實驗，您也可以選取您想要複製實驗的區域。

![多重地理協助圖像 4][4a]

## <a name="web-service-management"></a>Web 服務管理

若要以程式設計方式管理 web 服務，例如的重新訓練，使用的特定地區的地址︰
- https://asiasoutheast.management.azureml.net
- https://europewest.management.azureml.net

### <a name="things-to-note"></a>請注意的事項

1.  您可以只複製實驗之間屬於相同的區域以下列方式的工作區。 如果您要複製實驗跨不同區域中的工作區，您可以使用[PowerShell](http://aka.ms/amlps) commandlet[*複製 AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment)來完成的。 其他因應措施是發佈到圖庫實驗中未列出模式]，然後開啟在工作區中從其他區域。
2.  區域選取器只會顯示一個區域的工作區，一次。 在未來，您可以查看您有權存取所有區域上同時工作區的完整清單。  
3.  免費的工作區或來賓存取 （匿名） 工作區會建立並裝載於南部中央美制]。在未來，您可以建立空閒/來賓存取工作區中，在您選擇的區域。  
4.  從工作區中東南亞部署 web 服務也會裝載在東南亞。 在未來，您可以建立一個區域中，實驗的彈性，並部署產生 web 服務端點到不同的區域。  

## <a name="more-information"></a>詳細資訊

[Azure 電腦學習論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning)上提出問題。

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png
