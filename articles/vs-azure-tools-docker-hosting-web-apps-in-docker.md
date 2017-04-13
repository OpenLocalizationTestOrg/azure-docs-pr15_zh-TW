<properties
   pageTitle="ASP.NET 核心 Linux Docker 容器部署至遠端 Docker 主機 |Microsoft Azure"
   description="瞭解如何使用 Docker Visual Studio 工具部署至 Docker 容器 Azure Docker 主機 Linux VM 上執行的 ASP.NET 核心 web 應用程式"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>ASP.NET 容器部署至遠端 Docker 主機

## <a name="overview"></a>概觀
Docker 是輕量型容器引擎，一些虛擬機器，您可以使用 host （主機） 應用程式和服務的方法類似。
本教學課程中會引導您使用[Visual Studio 2015 工具 Docker](http://aka.ms/DockerToolsForVS)延伸到 Docker 主機上使用 PowerShell 的 Azure 部署 ASP.NET 核心應用程式。

## <a name="prerequisites"></a>必要條件
以下被需要完成本教學課程︰

- [如何使用 Azure docker-電腦](./virtual-machines/virtual-machines-linux-docker-machine.md)所述，建立 Azure Docker 主機 VM
- 安裝[Visual Studio 2015 更新 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Microsoft ASP.NET 核心 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
- 安裝[Docker-Preview 的 Visual Studio 2015 工具](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1.建立 ASP.NET 核心 web 應用程式
下列步驟會引導您完成建立基本的 ASP.NET 核心應用程式會在本教學課程中使用。

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2.新增 Docker 支援

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3.使用 DockerTask.ps1 PowerShell 指令碼 

1.  開啟專案的根目錄 PowerShell 提示。 

    ```
    PS C:\Src\WebApplication1>
    ```

1.  驗證遠端主機正在執行。 您應該會看到狀態 = 執行 

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] 如果您使用的 Docker beta 版，您的主機不會列在此處。

1.  建立應用程式使用-建立參數

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] 如果您使用 Docker Beta，省略-機器引數
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  執行應用程式，使用-執行參數

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] 如果您使用 Docker Beta，省略-機器引數
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

    Docker 完成後，您應該會看到類似以下結果︰

    ![檢視您的應用程式][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
