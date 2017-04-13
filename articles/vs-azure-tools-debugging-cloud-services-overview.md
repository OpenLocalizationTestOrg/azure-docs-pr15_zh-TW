<properties 
   pageTitle="偵錯 Azure 雲端服務 |Microsoft Azure"
   description="偵錯 Azure 雲端服務"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-cloud-services"></a>偵錯雲端服務

Microsoft Visual Studio 和 Azure SDK 使用 Azure 工具偵錯 Azure 的應用程式，您可以使用不同的方法︰

- 您可以偵錯 Azure 應用程式從 Visual Studio 開發，就像任何 Visual C# 或 Visual Basic 應用程式中一樣。 如需詳細資訊，請參閱[偵錯本機電腦上的雲端服務](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)。

- 若要從角色中執行的程式碼記錄的詳細的資訊，是否開發環境中或 Azure 中執行的角色，您可以使用 Azure 診斷。 如需詳細資訊，請參閱[收集的資料使用 Azure 診斷記錄](http://go.microsoft.com/fwlink/p/?LinkId=400450)。

- 如果您使用 Visual Studio 企業撰寫.NET Framework 4 或.NET Framework 4.5 適用對象的角色，您可以啟用 IntelliTrace 在您部署 Visual Studio Azure 雲端服務的時間。 IntelliTrace 提供您可以使用 Visual Studio 偵錯應用程式，如同執行 Azure 中的記錄。 如需詳細資訊，請參閱[偵錯 IntelliTrace 與 Visual Studio 已發佈的雲端服務]( http://go.microsoft.com/fwlink/p/?LinkId=623016)。

- 您可以啟用遠端偵錯時，在您的雲端服務，當您部署從 Visual Studio 雲端服務的時間。 如果您選擇要啟用遠端偵錯部署，遠端偵錯服務會安裝執行每一個角色執行個體的虛擬機器上。 這些服務，例如 msvsmon.exe，不會影響效能或產生額外的成本。 如需詳細資訊，請參閱[偵錯 Azure 中的雲端服務](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)。



