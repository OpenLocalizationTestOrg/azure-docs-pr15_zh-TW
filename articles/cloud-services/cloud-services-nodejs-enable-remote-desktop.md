<properties 
    pageTitle="啟用遠端桌面雲端服務 (Node.js)" 
    description="瞭解如何啟用遠端桌面存取裝載 Azure Node.js 應用程式虛擬機器。" 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="enabling-remote-desktop-in-azure"></a>啟用遠端桌面 Azure 中

遠端桌面可讓您存取執行 Azure 中的角色執行個體的桌面。 您可以使用遠端桌面連線設定虛擬機器或與您的應用程式的問題進行疑難排解。

> [AZURE.NOTE] 本文適用於裝載為 Azure 雲端服務的 Node.js 應用程式。


## <a name="prerequisites"></a>必要條件

- 安裝和設定[Powershell 的 Azure](../powershell-install-configure.md)。
- Azure 雲端服務部署 Node.js 應用程式。 如需詳細資訊，請參閱[建立並部署至 Azure 雲端服務的 Node.js 應用程式](cloud-services-nodejs-develop-deploy-app.md)。


## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>若要設定存取遠端桌面服務的步驟 1︰ 使用 Azure PowerShell

若要使用遠端桌面，您需要更新 Azure 服務定義和設定的使用者名稱、 密碼和憑證。 

包含您的應用程式的來源檔案的電腦執行下列步驟。

1. 以管理員身分執行**Windows PowerShell** 。 （從**[開始] 功能表**或**[開始] 畫面**中，搜尋**Windows PowerShell**。）

2.  瀏覽至包含的服務定義 (.csdef) 和 [服務設定 (.cscfg) 檔案的目錄。

3. 輸入下列 PowerShell 指令程式︰

        Enable-AzureServiceProjectRemoteDesktop

4. 出現提示時，輸入使用者名稱和密碼。

    ![啟用 azureserviceprojectremotedesktop][enable-rdp]

3.  輸入要發佈變更下列 PowerShell 指令程式︰

        Publish-AzureServiceProject

    ![發佈 azureserviceproject][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>步驟 2︰ 連線至角色的執行個體

發佈更新服務定義後，您可以連線至角色的執行個體。

1.  在[Azure 傳統入口網站]中，選取**雲端服務**，然後選取您的服務。

    ![Azure 傳統入口網站][cloud-services]

2.  按一下**執行個體**，然後按一下**生產**或**執行**，若要查看服務的執行個體。 選取 [執行個體，然後按一下 [**連線**] 頁面的底部。

    ![執行個體頁面][3]

2.  當您按一下 [**連線**的網頁瀏覽器就會提示您儲存.rdp 檔案。 開啟此檔案。 （例如，如果您使用的 Internet Explorer，按一下**開啟**）。

    ![提示若要開啟或儲存.rdp 檔案][4]

3.  檔案開啟時，會出現下列安全性提示︰

    ![Windows 安全性提示][5]

4.  按一下 [**連線**]，就會出現安全性提示輸入認證以存取執行個體。 輸入您在 [步驟 1] 中建立的密碼，[步驟 1︰ 使用 PowerShell 的 Azure 遠端桌面存取服務設定]，然後按一下**[確定]**。

    ![使用者名稱與密碼的提示][6]

連線時，遠端桌面連線會顯示 Azure 中的執行個體的桌面。 

![遠端桌面工作階段][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>步驟 3︰ 設定服務停用遠端桌面存取 

當您不再需要至雲端中的角色執行個體的遠端桌面連線時，停用使用[PowerShell 的 Azure]遠端桌面存取。

1.  輸入下列 PowerShell 指令程式︰

        Disable-AzureServiceProjectRemoteDesktop

2.  輸入要發佈變更下列 PowerShell 指令程式︰

        Publish-AzureServiceProject

## <a name="additional-resources"></a>其他資源

- [遠端存取 Azure 中的角色執行個體] 
- [使用遠端桌面 Azure 角色]
- [Node.js 開發人員中心](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure 傳統入口網站]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[遠端存取 Azure 中的角色執行個體]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[使用遠端桌面 Azure 角色]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 