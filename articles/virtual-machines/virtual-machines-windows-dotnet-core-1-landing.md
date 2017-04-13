<properties
   pageTitle="Azure 虛擬機器 DotNet 核心教學課程 1 |Microsoft Azure"
   description="Azure 虛擬機器 DotNet 核心教學課程"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>自動化應用程式部署至 Azure 虛擬機器

這個四部分系列詳細資料部署及設定 Azure 資源和使用 Azure 資源管理範本的應用程式]。 本系列範例範本部署及部署範本檢查。 本系列的目標是教育上 Azure 資源之間的關聯性，並可將部署完全整合的 Azure 資源管理員範本的體驗。 這份文件假設基本知識與 Azure 資源管理員的層級，啟動此教學課程之前先熟悉 Azure 資源管理員的基本概念。

## <a name="music-store-application"></a>音樂 store 應用程式

使用此系列的樣本是.Net 模擬音樂商店購物體驗的核心應用程式。 這個應用程式部署到 Linux 」 或 「 Windows 虛擬系統、 兩個已建立範例部署。 應用程式包含 web 應用程式和 SQL 資料庫。 閱讀本系列中的文章之前, 部署應用程式使用可在此頁面上找到 [部署] 按鈕。 完全部署時，應用程式 / Azure 架構看起來類似下圖中。 

音樂市集資源管理員範本可以在這裡找到，[音樂市集 Windows 範本](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![音樂 Store 應用程式](./media/virtual-machines-windows-dotnet-core/music-store.png)

每個元件，包括相關的範本 JSON 會檢查下列四種文件。

- [**應用程式架構**](./virtual-machines-windows-dotnet-core-2-architecture.md)– 例如網站及資料庫應用程式元件，就必須裝載於 Azure 電腦資源，例如虛擬機器和 Azure SQL 資料庫。 這份文件引導對應運算需求，Azure 資源和部署 Azure 資源管理員範本與這些資源。 

- [[**存取與安全性**](./virtual-machines-windows-dotnet-core-3-access-security.md)-裝載 Azure 中的應用程式時，就必須考慮存取應用程式的方式，以及如何不同的應用程式元件存取彼此。 這份文件詳細資料提供及保護網際網路存取應用程式與應用程式元件間的存取權。

- [**可用性和小數位數**](./virtual-machines-windows-dotnet-core-4-availability-scale.md)– 可用性和縮放比例參考應用程式可保持執行期間基礎結構停機時間，並不按比例縮放以符合應用程式的需要計算資源的能力。 此文件詳細資料部署負載平衡所需的元件和高度可用的應用程式。

- [**應用程式部署**](./virtual-machines-windows-dotnet-core-5-app-deployment.md)-部署應用程式拖曳到 Azure 虛擬機器時，必須考慮的虛擬機器安裝應用程式的二進位檔案的方法。 這份文件詳細資料使用 Azure 虛擬機器自訂指令碼擴充的自動化應用程式安裝。

開發 Azure 資源管理員範本時的目標是要自動部署 Azure 基礎結構，並安裝和任何的設定裝載於此 Azure 基礎結構的應用程式。 使用這些文章提供此體驗的範例。

## <a name="deploy-the-music-store-application"></a>部署音樂 store 應用程式

音樂市集應用程式，可以使用此按鈕部署。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Azure 資源管理員範本需要下列參數值。

|參數名稱 |描述   |
|---|---|
|ADMINUSERNAME   | 適用於虛擬機器和 Azure SQL 資料庫的管理員使用者名稱。  |
|ADMINPASSWORD | 適用於 Azure 虛擬機器和 SQL 資料庫的密碼。  |
|NUMBEROFINSTANCES | 若要建立的虛擬機器中的數字。 每個這些虛擬機器主控音樂市集 web 應用程式，而所有流量都是負載平衡在它們之間。 |
|PUBLICIPADDRESSDNSNAME | 全域唯一的公用 IP 位址相關聯的 DNS 名稱。 |

當範本部署完成時，瀏覽至使用任何網際網路瀏覽器的公用 IP 位址。 .Net 核心音樂網站就會顯示。

## <a name="next-steps"></a>後續步驟

<hr>

[步驟 1-應用程式架構與 Azure 資源管理員範本](./virtual-machines-windows-dotnet-core-2-architecture.md)

[步驟 2-存取和 Azure 資源管理員範本中的安全性](./virtual-machines-windows-dotnet-core-3-access-security.md)

[步驟 3-可用性和 Azure 資源管理員範本中的縮放比例](./virtual-machines-windows-dotnet-core-4-availability-scale.md)

[步驟 4-與 Azure 資源管理員範本的應用程式部署](./virtual-machines-windows-dotnet-core-5-app-deployment.md)


