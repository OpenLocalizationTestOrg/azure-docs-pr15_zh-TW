1. 在 Visual Studio**方案總管]**中，以滑鼠右鍵按一下專案，然後選取 [**新增 > Docker 支援**從操作功能表。

    ![新增 Docker 支援操作功能表](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. ASP.NET 核心中加入 Docker 支援產生的數個 Docker 相關檔案新增到專案時，包括 Docker 撰寫檔案、 部署 Windows PowerShell 指令碼，以及 Docker 屬性檔案 web 專案。 

    ![Docker 檔案新增至專案](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]如果使用[Docker Windows Beta](https://beta.docker.com)，開啟 Properties\Docker.props、 移除的預設值，並重新啟動 Visual Studio 的值，才會生效。
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
