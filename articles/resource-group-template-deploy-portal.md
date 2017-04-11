<properties 
    pageTitle="使用 Azure 入口網站部署 Azure 資源 |Microsoft Azure" 
    description="使用 Azure 入口網站和 Azure 資源管理部署資源。" 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>部署資源管理員範本與 Azure 入口網站的資源

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [入口網站](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

本主題說明如何使用[Azure 入口網站](https://portal.azure.com)與[Azure 資源管理員](azure-resource-manager/resource-group-overview.md)部署 Azure 資源。 若要瞭解管理您的資源，請參閱[透過入口網站管理 Azure 資源](./azure-portal/resource-group-portal.md)。

目前不是每個服務支援入口網站或資源管理員。 如需這些服務，您需要使用[傳統的入口網站](https://manage.windowsazure.com)。 針對每個服務的狀態，請參閱[Azure 入口網站的可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)。

## <a name="create-resource-group"></a>建立資源群組

1. 若要建立空白的資源] 群組中，選取 [**新增** > **管理** > **資源群組**。

    ![建立空白的資源群組](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. 讓它的名稱和位置，，如有必要，選取 [訂閱]。 您需要提供 [資源] 群組的位置，因為資源群組會將儲存的資源相關的中繼資料。 基於規範，您可能會想要指定的中繼資料的儲存位置。 一般而言，我們建議您指定大部分的資源所在的位置。 使用相同的位置，可以簡化您的範本。

    ![設定] 群組中的值](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>部署資源服務商場

建立資源群組之後，您可以對其部署資源，從服務商場項目。 服務商場提供常見的案例預先定義的解決方案。

1. 若要開始在部署，選取 [**新增**] 和 [您想要部署的資源類型。 然後，尋找您想要部署的資源的特定的版本。

    ![部署資源](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. 如果看不到您想要部署的特定方案，您可以搜尋服務商場。

    ![搜尋服務商場](./media/resource-group-template-deploy-portal/search-resource.png)

3. 根據所選的資源類型，您有部署前所設定的相關內容的集合。 這些選項不顯示在這裡，他們會根據而定的資源類型。 針對所有類型，您必須先選取目的地資源群組。 下圖顯示如何建立 web 應用程式，並將其部署到您所建立的資源群組。

    ![建立資源群組](./media/resource-group-template-deploy-portal/select-existing-group.png)

    或者，您可以決定要部署資源時，建立資源群組。 選取 [**建立新**，並為資源群組名稱。

    ![建立新的資源群組](./media/resource-group-template-deploy-portal/select-new-group.png)

4. 您的部署開始。 部署可能需要幾分鐘。 部署完成後，您會看到通知。

    ![檢視通知](./media/resource-group-template-deploy-portal/view-notification.png)

5. 在部署您的資源之後, 您可以新增更多資源至資源群組資源群組刀上使用 [**新增**] 命令。

    ![新增資源](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>部署資源中的自訂範本

如果您想要執行部署，但不是市場中使用的範本，您可以建立自訂的範本，以定義您的方案的基礎結構。 若要瞭解如何建立範本，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

1. 若要部署自訂的範本，透過入口網站，選取 [**新增**]，然後開始搜尋**範本**部署鍵，直到您可以從選項] 中選取它。

    ![搜尋範本部署](./media/resource-group-template-deploy-portal/search-template.png)

2. 從 [可用的資源，請選取**範本部署**。

    ![選取範本部署](./media/resource-group-template-deploy-portal/select-template.png)

3. 在啟動範本部署之後, 開啟可用來自訂的空白範本。

    ![建立範本](./media/resource-group-template-deploy-portal/show-custom-template.png)

    在編輯器] 中新增 JSON 語法，以定義您想要部署的資源。 選取 [完成，[**儲存**]。 撰寫 JSON 語法，請參閱[資源管理員範本逐步解說](resource-manager-template-walkthrough.md)。

    ![編輯範本](./media/resource-group-template-deploy-portal/edit-template.png)

4. 或者，您可以從[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)選取已存在的範本。 這些範本所提供的社群。 它們涵蓋許多常見的案例中，然後某人新增類似於您想要部署的範本。 您可以搜尋範本，找出符合您的狀況的項目。

    ![選取 [快速入門範本](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    在編輯器] 中，您可以檢視所選的範本。

5. 提供所有其他的值後, 選取 [**建立**部署範本。 

    ![部署範本](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>部署資源從範本儲存至您的帳戶

入口網站可讓您將範本儲存至您的 Azure 帳戶，並重新部署更新版本。 如需有關如何使用這些儲存範本，[快速入門 Azure 入口網站上的私人範本](./marketplace-consumer/mytemplates-getstarted.md)的詳細資訊。

1. 若要尋找您已儲存的範本，請選取 [**瀏覽** > **範本**。

    ![瀏覽範本](./media/resource-group-template-deploy-portal/browse-templates.png)

2. 從範本儲存至您的帳戶清單中，選取您想要處理的項目。

    ![已儲存的範本](./media/resource-group-template-deploy-portal/saved-templates.png)

3. 選取 [**部署**重新部署此儲存的範本]。

    ![部署儲存的範本](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>後續步驟

- 若要檢視稽核記錄，請參閱[稽核作業與資源管理員](resource-group-audit.md)。
- 若要疑難排解部署錯誤，請參閱[Azure 入口網站與疑難排解資源群組部署](resource-manager-troubleshoot-deployments-portal.md)。
- 若要從部署或資源群組擷取範本，請參閱[匯出 Azure 資源管理員範本從現有的資源](resource-manager-export-template.md)。





