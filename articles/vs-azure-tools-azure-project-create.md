<properties
   pageTitle="使用 Visual Studio 建立的 Azure 專案 |Microsoft Azure"
   description="使用 Visual Studio 建立 Azure 的專案"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="creating-an-azure-project-with-visual-studio"></a>使用 Visual Studio 建立 Azure 的專案

Visual Studio Azure 工具提供的範本，可讓您建立的 Azure 雲端服務。 工具也可協助您設定、 偵錯，及部署 Azure 雲端服務。

Azure 雲端服務方案包含下列類型的專案︰

- **Azure 專案**

    Azure 專案方案中有與角色專案間的關聯。 也包含的服務定義和服務設定檔。 服務定義檔案定義執行階段設定，包括何種角色所需的應用程式，結束點，然後虛擬機器大小。 服務設定檔設定要執行的角色的執行個體數目，以及角色所定義的設定值。 如需這些設定的詳細資訊，請參閱[如何︰ 設定 Visual Studio Azure 雲端服務的角色](vs-azure-tools-configure-roles-for-cloud-service.md)。

- **Web 角色專案**

    工作者角色執行背景處理。 儲存服務，以及與其他以網際網路為基礎的服務，就可以通訊工作者角色。 工作者角色可以有 HTTP、 HTTPS 或 TCP 端點的任何數字。

    - **ASP.NET 網頁角色**，建立 web 前端 ASP.NET 應用程式
    - **ASP.NET MVC5 網頁角色**
    - **ASP.NET MVC4 網頁角色**
    - **ASP.NET MVC3 網頁角色**
    - **WCF 服務 Web 角色**，建置 WCF 服務
    - **Silverlight 商務應用程式網頁角色**（需要 Visual Studio 2012）

- **快取工作者角色**

    所提供的專用的快取至您的應用程式的角色。

- **與服務匯流排佇列工作者角色**

    服務匯流排佇列工作程序提供訊息佇列功能進行通訊。 如需詳細資訊，請參閱[如何使用服務匯流排佇列](http://go.microsoft.com/fwlink/?LinkId=260560)。

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>若要在 Visual Studio 建立 Azure 雲端服務專案

1. 以系統管理員身分啟動 Microsoft Visual Studio。

1. 在功能表列中，選擇 [**檔案**，**新增****專案**]。

1. 在 [**專案類型**] 窗格中，選擇**雲端**Visual C# 或 Visual Basic 專案範本節點。

1. 在 [**範本**] 窗格中，選擇 [ **Azure 雲端服務**。

1. 指定您要用來開發專案的.NET framework 哪個版本。

1. 輸入名稱和專案的位置和解決方案的名稱。 選擇**[確定**] 按鈕。

1. 在 [**新 Azure 專案**] 對話方塊中，選擇您想要新增的角色，選擇向右箭號按鈕，以將其新增至您的方案。 您可以新增任何數量的角色，視需要。

1. 若要重新命名的角色，您已新增至您的專案，請將游標暫留在 [**新 Azure 專案**] 對話方塊中的角色，選擇 [**重新命名**] 圖示右邊的角色。 您也可以重新角色之後已新增命名您的方案中。
