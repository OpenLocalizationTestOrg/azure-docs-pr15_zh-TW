<properties
   pageTitle="設定 VirtualBox Docker 主機 |Microsoft Azure"
   description="若要設定預設 Docker 的執行個體，使用 Docker 電腦和 VirtualBox 的逐步指示"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>設定 VirtualBox Docker 主機

## <a name="overview"></a>概觀
本文會引導您完成設定預設 Docker 的執行個體，使用 Docker 電腦和 VirtualBox。 如果您使用的[Docker Windows beta 版](http://beta.docker.com/)，則不需要此設定。

## <a name="prerequisites"></a>必要條件
需要安裝下列工具。

- [Docker 工具箱](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>使用 Windows PowerShell 設定 Docker 用戶端

若要設定 Docker 用戶端，只是開啟 Windows PowerShell，並執行下列步驟︰

1. 建立預設 docker 主機執行個體。

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. 請確認預設的執行個體設定並執行。 （您應該會看到名為 「 預設 」 執行的執行個體。

    ```PowerShell
    docker-machine ls 
    ```
        
    ![docker 機器 1 輸出][0]
 
1. 設定預設為目前的主機，並設定您的命令介面。

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. 顯示 [作用中的 Docker 容器。 清單應該是空的。

    ```PowerShell
    docker ps
    ```

    ![docker ps 輸出][1]
 
> [AZURE.NOTE]每當您重新啟動您的部署電腦，您需要重新啟動您的本機 docker 主機。
> 若要這麼做，議題下列命令，在命令提示字元︰ `docker-machine start default`。

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
