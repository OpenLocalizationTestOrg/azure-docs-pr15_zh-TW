<properties
    pageTitle="部署到多個區域的 Web 服務的方式 |Microsoft Azure"
    description="部署 （複本） 到其他新的 Web 服務區域的步驟。"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>部署到多個區域的 Web 服務的方式

新的 Azure Web 服務可讓您輕鬆地部署 web 服務給多個區域，並不需要多個訂閱或工作區。 

價格是特定，因此您必須定義部署 web 服務的每個區域的帳單計劃的區域。

## <a name="to-create-a-plan-in-another-region"></a>若要建立另一個區域中的計劃

1. 登入[Microsoft Azure 電腦學習 Web 服務](https://services.azureml.net/)。
2. 按一下 [**方案**] 功能表選項。
3. 檢視頁面方案，請在 [**新增**]。
4. 從 [**訂閱**] 下拉式清單中，選取 [將存放新方案的訂閱]。
5. 從 [**地區**] 下拉式清單中，選取 [新方案的區域]。 規劃選項選取之區域會顯示在頁面的 [**方案選項**] 區段。
6. 從 [**資源群組**] 下拉式清單中，選取 [計劃的資源群組]。 從 [資源群組的詳細資訊，請參閱[透過入口網站管理 Azure 資源](../azure-portal/resource-group-portal.md)。
7. **計劃名稱**中輸入方案的名稱。
8. 在 [**方案選項**] 底下，按一下 [新方案的帳單等級]。
9. 按一下 [**建立**]。


## <a name="deploying-the-web-service-to-another-region"></a>部署至另一個區域的 web 服務

1. 按一下 [ **Web 服務**] 功能表選項。
2. 選取您要部署到新的區域的 Web 服務。
3. 按一下 [**複製**]。
4. 在**Web 服務名稱**，輸入 web 服務的新名稱。
5. 在 [ **Web 服務描述**] 中，輸入 web 服務描述。
6. 從 [**訂閱**] 下拉式清單中，選取 [新的 web 服務將存放訂閱]。
7. 從 [**資源群組**] 下拉式清單中，選取 [web 服務的資源群組]。 從 [資源群組的詳細資訊，請參閱[透過入口網站管理 Azure 資源](../azure-portal/resource-group-portal.md)。
8. 從 [**地區**] 下拉式清單中，選取 [要部署 web 服務的區域]。
9. 從**儲存的帳戶**] 下拉式清單中，選取儲存的帳戶來儲存 web 服務。
10. 從**價格計劃**] 下拉式清單中，請在您選取的步驟 8 中的區域中選取一個方案。
11. 按一下 [**複製**]。

