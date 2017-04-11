<properties
    pageTitle="在應用程式服務環境中建立 web 應用程式"
    description="瞭解如何建立 web 應用程式與應用程式服務方案應用程式服務環境"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>在應用程式服務環境中建立 web 應用程式

## <a name="overview"></a>概觀

本教學課程中會顯示如何在[應用程式服務環境](app-service-app-service-environment-intro.md)(ASE) 中建立 web 應用程式與應用程式服務方案。 

> [AZURE.NOTE] 如果您想要瞭解如何建立 web 應用程式，但不需要執行應用程式服務環境中，請參閱[建立.NET web 應用程式](web-sites-dotnet-get-started.md)或其中一個其他語言和架構相關的教學課程。

## <a name="prerequisites"></a>必要條件

本教學課程假設您已建立的應用程式服務環境。 如果您尚未執行此步驟，請參閱[建立應用程式服務環境](app-service-web-how-to-create-an-app-service-environment.md)。 

## <a name="create-a-web-app"></a>建立 web 應用程式

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，按一下 [**新增 > Web + 行動 > Web App**。 

    ![][1]

2. 選取您的訂閱。  

    如果您有多個訂閱，請注意，若要建立您的應用程式服務環境的應用程式，您需要使用同一份訂閱您所建立的環境時。 

3. 選取或建立資源群組]。

    *資源群組*可讓您管理相關的 Azure 資源為單位，並建立您的應用程式的*角色型存取控制*(RBAC) 規則時，很有用。 如需詳細資訊，請參閱[管理您 Azure 資源][ResourceGroups]。 

4. 選取或建立的應用程式服務方案。

    管理*應用程式服務方案*的 web 應用程式的設定。  通常您選取價格，支付的價格會套用到應用程式服務方案，而不是個別的應用程式。 支付配置給 ASE 計算執行個體中 ASE 而不是與您 ASP 有列出。  若要縮放設定您的應用程式服務的執行個體不按比例縮放的 web 應用程式的執行個體數目計劃，而且會影響所有 web 應用程式的計劃中。  某些功能，例如網站位置或 VNET 整合也有計劃中的數量限制。  如需詳細資訊，請參閱[Azure 應用程式服務方案概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    查看會加註的方案名稱下方的位置，您可以在您 ASE 識別的應用程式服務方案。  

    ![][5]

    如果您想要使用的應用程式服務方案已存在於應用程式服務環境中，選取該計劃。 如果您想要建立新的應用程式服務方案，請參閱此教學課程中，[建立應用程式服務環境的應用程式服務方案](#createplan)的下一節。

5. 輸入您的 web 應用程式的名稱，然後再按一下 [**建立**]。 

    如果您 ASE 使用外部 VIP ASE 中的應用程式的 URL 是: [*站台名稱*]。[*應用程式服務環境的名稱*]。，而不是 [*站台名稱*] 的 p.azurewebsites.net。 azurewebsites.net
    
    如果您 ASE 使用內部 VIP 然後應用程式的 URL 是 ASE: [*站台名稱*]。[*子網域建立期間，指定 ASE*]   
    選取您 ASP ASE 建立期間之後，您就會看到更新**名稱**下的子網域

## <a name="createplan"></a>建立應用程式服務計劃

當您在應用程式服務環境中建立的應用程式服務方案時，您工作者有不同的選擇為 ASE 中有未共用的同事。  您必須使用工作者是管理員配置給 ASE 的項目  這表示，若要建立新的方案，您必須具備配置的執行個體數比您 ASE 背景工作集區至所有已在該背景工作集區計劃更多的同事。  如果您沒有足夠的工作者您 ASE 工作者資料庫建立您的方案中，您需要使用您的 ASE 系統管理員以取得新增。

其他裝載的應用程式服務環境的應用程式服務方案的差別缺乏價格選取範圍。  有應用程式服務環境時您由系統的計算資源的付款，並在該環境中沒有新增方案的費用。  以正常方式當您建立的應用程式服務方案您選取用來決定您的帳單的價格計劃。  應用程式服務環境是基本上私人位置，您可以在其中建立內容。  您使用的環境，而不主控您的內容。

下列指示顯示如何建立教學課程的前一節所述的 web 應用程式時建立的應用程式服務方案。

1. 計劃選取範圍使用者介面中按一下 [**建立新**，並提供您計劃的名稱，就像您平常 ASE 以外。

2. 選取您想要使用從您的位置選擇器 ASE。

    應用程式服務環境位於基本上私用部署，因為它會顯示在 [位置]。 

    ![][2]

    之後的 ASE 位置選擇器中的選取範圍，應用程式服務方案建立使用者介面就會更新。  位置現在會顯示名稱 ASE 系統和地區，和價格的計劃選擇器會取代為 [背景工作集區選擇器。  

    ![][3]

### <a name="selecting-a-worker-pool"></a>選取 [背景工作集區

通常在 Azure 應用程式服務和以外的應用程式服務環境中，有 3 計算大小所提供的專用的價格計劃的選取範圍。  以類似的方式，對於 ASE 可以定義最多 3 的同事的資料庫，然後指定計算大小所用的背景工作集區。  所代表的意義的租用戶的 ASE 為該改為選取與您的應用程式服務方案計算大小的價格計劃中，選取 [稱為*背景工作集區*。  

背景工作集區選取範圍使用者介面會顯示用於名稱下方的背景工作集區計算大小。  可用的數量指的是多少計算執行個體可供使用的資料庫中。  總資料庫實際上可能會有超過此數字的多個執行個體，但此 value 指的是只要數不是使用中。  如果您需要調整您要新增更多計算資源，請參閱 「[設定您的應用程式服務環境](app-service-web-configure-an-app-service-environment.md)的應用程式服務環境。

![][4]

在此範例中，您會看到只有兩個可用的背景工作集區。 這是因為 ASE 管理員只配置主機到這些兩個工作者集區。  Vm 將它分派時，就會出現第三個。  

## <a name="after-web-app-creation"></a>在 web 應用程式建立後

有一些考量執行 web 應用程式及管理需要考慮 ASE 應用程式服務方案。  

如先前所述，ASE 的擁有者會負責系統的大小和結果也就是負責有足夠的容量裝載您要的應用程式服務方案。 如果不有任何可用的同事，您無法建立您的應用程式服務方案。  這也是將 true 變為擴充 web 應用程式。  如果您需要更多的執行個體您想要有取得您的應用程式服務環境系統管理員，若要新增更多的同事。

建立您的 web 應用程式和應用程式服務方案之後最好不按比例縮放。  在 ASE 永遠必須至少 2 個執行個體的應用程式服務計劃以提供您的應用程式的容錯能力。  在 ASE 縮放的應用程式服務方案是相同的應用程式服務方案 UI 透過標準。  如需有關縮放比例、[不按比例縮放的應用程式服務環境中的 web 應用程式的方式](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
