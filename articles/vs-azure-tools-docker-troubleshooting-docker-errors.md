<properties
   pageTitle="使用 Visual Studio 的 Windows 上 Docker 用戶端錯誤疑難排解 |Microsoft Azure"
   description="疑難排解使用 Visual Studio 建立並部署至 Docker Windows 上的 web 應用程式，使用 Visual Studio 時，會遇到的問題。"
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
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>疑難排解 Visual Studio Docker 開發

當 Docker 預覽使用 Visual Studio 工具，您可能會遇到預覽性質的一些問題。
以下是一些常見的問題和解決方案。


## <a name="unable-to-validate-volume-mapping"></a>無法驗證大量對應
大量對應，才能與容器中的 [應用程式] 資料夾中共用的程式碼及應用程式的二進位檔案。  特定大量對應當中的 docker compose.dev.debug.yml 和 docker compose.dev.release.yml 檔案。 為檔案主機電腦上已變更，容器會反映這些類似的資料夾結構中的變更。

要啟用大量對應，請開啟 [**設定...** ]，請從 Docker Windows 」 就 「 系統匣圖示，然後選取 [**共用磁碟機**] 索引標籤。  確定共用的磁碟機字母主控您的專案，以及 %USERPROFILE%所在的磁碟機字母，以檢查，然後按一下 [**套用]**。

若要測試若運作大量對應之後已共用的磁碟機,，重建和在 Visual Studio 或嘗試從 F5 命令從下列提示︰

*在 Windows 命令提示字元*

*[附註︰ 這是假設您的使用者] 資料夾位於"C"磁碟機，並且已共用過。 如果您有共用的另一個磁碟機，視需要更新]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Linux 容器中*

```
/ # ls
```

您應該會看到目錄從使用者/公用資料夾清單。
如果沒有顯示任何檔案，而且您 /c/Users/Public 資料夾不是空白檔案，大量對應未妥善設定。 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

將蟲孔目錄，若要查看的內容變更`/c/Users/Public`目錄︰

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**附註︰***容器檔案系統使用 Linux Vm 工作時，會區分大小寫。*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>建立: 「 PrepareForBuild 」 任務意外失敗。

Microsoft.DotNet.Docker.CommandLine.ClientException︰ 試著連接時發生錯誤︰

確認預設 docker 主機正在執行。 開啟命令提示字元，以及執行︰

```
docker info
```

如果這會傳回錯誤再嘗試啟動**Docker 在 Windows 版**桌面應用程式。  如果桌面應用程式執行的然後**就**圖示系統匣中應該會看到。 以滑鼠右鍵按一下系統匣圖示，然後開啟 [**設定**]。  按一下 [**重設**] 索引標籤，然後**重新啟動 Docker...**]。

##<a name="manually-upgrading-from-version-031-to-040"></a>手動從版本 0.31 至 0.40 升級


1. 備份專案
1. 刪除專案中的下列檔案︰

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. 關閉方案，並移除.xproj 檔案中的下列行︰

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. 重新開啟方案
1. 移除 Properties\launchSettings.json 檔案中的下列行︰

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. 移除相關 Docker project.json publishOptions 中從下列檔案︰

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. 解除安裝舊版並安裝 Docker 工具 0.40，]，然後按一下 [**新增]-> [Docker 支援**一次從內容功能表 ASP.Net 核心網頁或主控台應用程式。 這會回到您的專案中新增新的必要的 Docker 成品。 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>錯誤對話方塊時嘗試**Docker 支援]-> [新增**或偵錯 (F5) 容器中將 ASP.NET 核心應用程式

我們有時候看到解除安裝和安裝擴充功能，Visual Studio MEF （受管理的擴充架構） 快取可以損毀。 當發生這種情況，可能會導致各種錯誤對話方塊時新增 Docker 支援及/或嘗試執行或偵錯 (F5) ASP.NET 核心應用程式。 暫時解決這個問題，執行下列步驟，以刪除並重新產生 MEF 快取。

1. 關閉所有執行個體的 Visual Studio
1. 開啟 %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. 刪除下列資料夾
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. 開啟 Visual Studio
1. 再次嘗試此案例 
