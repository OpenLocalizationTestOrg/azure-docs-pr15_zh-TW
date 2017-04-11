<properties
    pageTitle="如何建立並部署雲端服務 |Microsoft Azure"
    description="瞭解如何建立並部署雲端服務，使用 [Azure 入口網站。"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>如何建立並部署雲端服務

> [AZURE.SELECTOR]
- [Azure 入口網站](cloud-services-how-to-create-deploy-portal.md)
- [Azure 傳統入口網站](cloud-services-how-to-create-deploy.md)

Azure 入口網站提供您建立並部署雲端服務的兩種方法︰*快速建立*和*自訂建立*。

本文說明如何使用快速建立方法來建立新的雲端服務，然後使用 [上傳並部署雲端服務套件 Azure 中的 [**上傳**。 當您使用這個方法時，Azure 入口網站可讓您可以使用方便的連結，當您完成所有的需求。 如果您已準備好要部署您的雲端服務，您所建立，您可以將兩者同時使用自訂建立執行。

> [AZURE.NOTE] 如果您打算要發佈您的雲端服務的 Visual Studio 小組服務 (VSTS)，請使用 [快速建立，，並選擇 [設定 VSTS 發佈從 Azure 快速入門或儀表板。 如需詳細資訊，請參閱[連續的傳送到使用 Visual Studio 小組服務 Azure][TFSTutorialForCloudService]，或請參閱**快速入門**] 頁面的說明。

## <a name="concepts"></a>概念
三個元件，才能部署 Azure 中的雲端服務應用程式︰

- **服務定義**  
  雲端服務定義檔案 (.csdef) 定義的服務模型，包括角色的數量。

- **服務設定**  
  雲端服務設定檔 (.cscfg) 提供設定的設定雲端服務與個別的角色，包括角色執行個體數目。

- **服務套件**  
  服務套件 (.cspkg) 包含的應用程式碼和設定服務定義檔案。

您可以進一步瞭解這些，以及如何建立套件[以下](cloud-services-model-and-package.md)。

## <a name="prepare-your-app"></a>準備您的應用程式
您可以佈署雲端服務之前，您必須建立雲端服務套件 (.cspkg) 從您的應用程式碼和雲端服務設定檔 (.cscfg)。 Azure SDK 提供準備這些必要的部署檔案所需的工具。 您可以安裝 SDK [Azure 下載](https://azure.microsoft.com/downloads/)頁面上，在您想要開發應用程式碼中的語言。

前匯出服務套件，三個雲端服務功能需要特殊的設定︰

- 如果您想要部署雲端服務資料加密，[設定您的應用程式](cloud-services-configure-ssl-certificate-portal.md#modify)的 SSL 使用安全通訊端層 (SSL)。

- 如果您想要設定的角色執行個體，遠端桌面[設定的角色](cloud-services-role-enable-remote-desktop.md)遠端桌面連線。 這只能夠在傳統入口網站。

- 如果要設定監控您的雲端服務的詳細資訊，請啟用 Azure 診斷雲端服務。 *最小監控*（預設監控層級） 使用主機作業系統的角色執行個體 （虛擬機器） 的所收集的效能計數器。 *監控詳細資訊*」 會收集內啟用更深入的分析，在應用程式處理期間發生的問題的角色執行個體的效能資料所根據的其他指標。 若要瞭解如何啟用 Azure 診斷程式，請參閱[Azure 中的啟用診斷](cloud-services-dotnet-diagnostics.md)。

若要建立雲端服務的 web 角色或工作者角色的部署使用，您必須[建立服務套件](cloud-services-model-and-package.md#servicepackagecspkg)。

## <a name="before-you-begin"></a>開始之前

- 如果您還沒有安裝 Azure SDK，按一下 [**安裝 Azure SDK**以開啟[Azure 下載 」 頁面](https://azure.microsoft.com/downloads/)上，並為下載 SDK 中您想要開發程式碼的語言。 （您必須有機會稍後再執行此動作）。

- 如果任何角色執行個體需要憑證，請建立憑證。 雲端服務以私密金鑰需要.pfx 檔。 [您可以上傳至 Azure 憑證]()時建立及部署雲端服務。

- 如果您打算要部署至相關性群組的雲端服務，建立相關性群組。 若要將您的雲端服務和其他 Azure 服務部署中的同一個位置，您可以使用相關性群組。 您可以建立相關性] 群組中的 Azure 的傳統入口網站，在 [**相關性群組**] 頁面上的 [**網路**] 區域。


## <a name="create-and-deploy"></a>建立並部署

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下 [**新增 > 虛擬機器**，然後向下捲動並按一下 [**雲端服務**。

    ![發佈您的雲端服務](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. 在顯示的 [資訊] 頁面的底部，按一下 [**建立**]。 
4. 在新的**雲端服務**刀中，輸入值**DNS 名稱**。
5. 建立新的**資源群組**，或選取現有的項目。
6. 選取的**位置**。
7. 按一下 [**套件**]。 這會開啟**上傳的套件**刀。 在 [必要] 欄位中填滿。  

     如果您的角色包含單一的執行個體，確定已**部署即使一或多個角色包含單一的執行個體**。

8. 請確定已選取 [**開始部署**。
9. 按一下**[確定]**關閉 [**上傳的套件**刀。
10. 如果您沒有任何要新增的憑證，請按一下 [**建立**]。

    ![發佈您的雲端服務](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>上傳憑證

如果您的部署套件已[設定為使用憑證](cloud-services-configure-ssl-certificate-portal.md#modify)，您可以立即上傳的憑證。

1. 選取**憑證**，並在**新增憑證**刀中，選取 [SSL 憑證.pfx 檔，然後憑證，提供的**密碼**
2. 按一下 [**附加憑證**]，然後**新增憑證**刀上按一下**[確定]** 。
3. 按一下 [在**雲端服務**刀上的 [**建立**]。 當部署達到**好**的狀態時，您可以繼續下一個步驟。

    ![發佈您的雲端服務](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## <a name="verify-your-deployment-completed-successfully"></a>請確認您已順利完成的部署

1. 按一下 [雲端服務執行個體。

    狀態應該會顯示服務會**執行**。

2. 按一下 [**基本功能**，在網頁瀏覽器中開啟您的雲端服務的**網站 URL** 。

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>後續步驟

* [一般設定您的雲端服務](cloud-services-how-to-configure-portal.md)。
* 設定[自訂網域名稱](cloud-services-custom-domain-name-portal.md)。
* [管理您的雲端服務](cloud-services-how-to-manage-portal.md)。
* 設定[ssl 憑證](cloud-services-configure-ssl-certificate-portal.md)。