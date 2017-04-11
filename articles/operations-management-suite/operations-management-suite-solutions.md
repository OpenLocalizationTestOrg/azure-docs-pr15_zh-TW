<properties
   pageTitle="解決方案的作業管理套件 (OMS) |Microsoft Azure"
   description="解決方案擴充功能的作業管理套件 (OMS)，提供客戶可以新增至其 OMS 工作區的封裝的管理案例。  本文提供建立的客戶及合作夥伴的如何自訂解決方案的詳細資訊。"
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>管理解決方案作業管理套件 (OMS) （預覽版本） 中

>[AZURE.NOTE]這是在預覽中目前的 OMS 管理解決方案的初步的文件。    

管理解決方案擴充功能的作業管理套件 (OMS)，提供客戶可以新增至他們的環境的封裝的管理案例。  除了[microsoft 提供的解決方案](../log-analytics/log-analytics-add-solutions.md)，夥伴和客戶可以建立自己的環境中使用的管理解決方案，或提供透過社群的客戶。

## <a name="finding-and-installing-management-solutions"></a>尋找並安裝管理解決方案
有多種方法找出並安裝管理解決方案，下列各節所述。

### <a name="azure-marketplace"></a>Azure Marketplace
Microsoft 提供管理解決方案與受信任的合作夥伴可能安裝從 Azure Marketplace Azure 入口網站中。

1. Azure 入口網站登入。
2. 在左窗格中，選取 [**更多服務**]。
3. [向下**解決方案**捲動，或輸入 [**篩選**] 對話方塊中的*解決方案*。
4. 按一下 [ **+ 新增**] 按鈕。
5. 搜尋您感興趣瀏覽]、 按一下 [**篩選**] 按鈕，或在 [**搜尋積木**] 方塊中輸入的解決方案。
6. 按一下檢視的詳細的資訊的服務商場項目。
4. 按一下 [**建立**]，開啟 [**新增方案**] 窗格。
5. 您將會提示您所需資訊，例如[OMS 工作區與自動化帳戶](#oms-workspace-and-automation-account)除了的方案中的任何參數值。
6. 按一下 [**建立**安裝方案。

### <a name="oms-portal"></a>OMS 入口網站
從方案庫 OMS 入口網站中可以安裝管理 Microsoft 所提供的解決方案。

1. 登入 OMS 入口網站。
2. 按一下 [**方案庫**] 磚。
2. 在 OMS 方案庫頁面上，瞭解每一個可用的解決方案。 按一下您要新增至 OMS 的方案名稱。
3. 在頁面上您所選擇的方案，會顯示方案的詳細的資訊。 按一下 [**新增**]。
4. 新的方案新增概觀] 頁面的 OMS 您可以開始使用 OMS 服務處理您的資料後出現的方塊。

### <a name="azure-quickstart-templates"></a>Azure 快速入門範本
社群的成員可以送出管理解決方案 Azure 快速入門範本。  您可以下載這些範本的更新版本的安裝，或檢查即可瞭解如何[建立您自己的解決方案](#creating-a-solution)。

1. 按照[OMS 工作區與自動化帳戶](#oms-workspace-and-automation-account)連結的工作區與帳戶所述的程序。
2. 移至[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。  
3. 搜尋感興趣的解決方案。
4. 若要檢視其詳細資料的結果，請選取方案。
5. 按一下 [**部署至 Azure** ] 按鈕。
6. 會提示您提供的方案中的任何參數的資訊，例如資源群組和除了值的位置。
7. 按一下 [安裝方案**購買**。

### <a name="deploy-azure-resource-manager-template"></a>部署 Azure 資源管理員範本
您收到來自社群，或該您[自行建立](#creating-a-solution)實作為資源管理員範本，而且您可以使用標準方法[部署範本](../resource-group-template-deploy-portal.md)的解決方案。  請注意，在安裝之前方案，您必須建立連結[OMS 工作區與自動化帳戶](#oms-workspace-and-automation-account)。

## <a name="oms-workspace-and-automation-account"></a>OMS 工作區與自動化帳戶
大部分的管理解決方案需要[OMS 工作區](../log-analytics/log-analytics-manage-access.md)以包含檢視及[自動化帳戶](../automation/automation-security-overview.md#automation-account-overview)包含 runbooks 和相關的資源。 工作區與帳戶必須符合下列要求。

- 解決方案只能使用一個 OMS 工作區和自動化帳戶。  
- OMS 工作區及解決方案所使用的自動化帳戶必須彼此連結。 OMS 工作區可能只能連結至自動化帳戶，請和自動化帳戶可能只能連結至一個 OMS 工作區。
- 若要連結，OMS 工作區和自動化帳戶必須在相同的資源群組及區域。  例外狀況是東亞美國地區 OMS 工作區及與自動化東亞美式 2 中的帳戶。

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>建立 OMS 工作區和自動化帳戶之間的連結
如何指定 OMS 工作區和自動化帳戶取決於您的方案的安裝方法。

- 當您安裝 Microsoft 解決方案透過 OMS 入口網站時，安裝目前的 OMS 工作區中並沒有自動化帳戶。

- 當您安裝的方案，透過 Azure Marketplace 時，系統會提示您 OMS 工作區和自動化帳戶和它們之間的連結會為您建立。  

- 針對外部 Azure Marketplace 的方案，您必須安裝解決方案之前連結的 OMS 工作區及自動化帳戶。  您可以在 Azure Marketplace 中選取任何方案，並選取 OMS 工作區及自動化帳戶。  您不需要實際安裝方案，因為會建立連結，只要選取 [OMS 工作區] 和 [自動化帳戶。  一旦建立連結，然後您可以使用該 OMS 工作區與自動化帳戶的任何解決方案。 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>驗證 OMS 工作區和自動化帳戶之間的連結
您可以驗證 OMS 工作區與使用下列程序自動化帳戶之間的連結。

1. 選取 [Azure 入口網站中的 [自動化帳戶]。
2. 捲動到底部的 [**設定**] 窗格。
3. 如果沒有 [**設定**] 窗格中，名為 [ **OMS 資源**的區段，此帳戶被附加至 OMS 的工作區。
4. 選取 [檢視連結至此自動化帳戶 OMS 工作區的詳細資料的**工作區**]。


## <a name="listing-management-solutions"></a>列出管理解決方案
連結至 Azure 訂閱的工作區中檢視管理解決方案可使用下列程序。

1. Azure 入口網站登入。
2. 在左窗格中，選取 [**更多服務**]。
3. [向下**解決方案**捲動，或輸入 [**篩選**] 對話方塊中的*解決方案*。
4. 將列安裝所有工作區中的解決方案。

請注意，您可以檢視只能安裝在目前的工作區使用 OMS 入口網站中的 Microsoft 解決方案。

## <a name="removing-a-management-solution"></a>移除管理解決方案
移除管理解決方案之後，也會移除方案中的所有資源。  

1. 在使用中[列出的解決方案](#listing-solutions)的程序 Azure 入口網站中找到解決方案。
2. 選取您想要移除的方案。
3. 按一下 [**刪除**] 按鈕。

## <a name="creating-a-management-solution"></a>建立管理解決方案
完成建立管理解決方案的指南，可[建立](operations-management-suite-solutions-creating.md)solutions 作業管理套件 (OMS)。 


## <a name="next-steps"></a>後續步驟

- 搜尋[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates)的不同的資源管理員範本的範例。
- 建立您自己的[管理解決方案](operations-management-suite-solutions-creating.md)。
 