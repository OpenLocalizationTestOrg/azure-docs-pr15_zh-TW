<properties
    pageTitle="將 Web 應用程式資源提供者新增至 Azure 堆疊 |Microsoft Azure"
    description="詳細的指引，部署 Azure 堆疊的 Web 應用程式"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>將 Web 應用程式資源提供者新增至 Azure 堆疊

> [AZURE.NOTE] Azure 堆疊 TP1 部署僅適用於下列資訊。

將 Web 應用程式資源提供者新增至 Azure 堆疊有 7 個步驟︰

1.  下載所需的元件。
2.  建立要由 Azure 堆疊 Web 應用程式使用的憑證。
3.  若要下載階段及安裝 Azure 堆疊 Web 應用程式中使用的安裝程式。 
4.  驗證 Web 應用程式安裝。
5.  建立 DNS 記錄的前端及管理伺服器負載平衡器。
6.  登錄手錶最新的 Web 應用程式資源提供者。
7.  測試磁碟機的 Web 應用程式資源提供者。

## <a name="download-required-components"></a>下載所需的元件

1.  下載[Azure 堆疊應用程式服務預覽安裝程式](http://aka.ms/azasinstaller)。 
2.  下載[Azure 堆疊應用程式服務部署協助指令碼](http://aka.ms/azashelper)。 
3.  協助指令碼 zip 檔案中擷取檔案，必須有三種指令碼︰
    - 建立 AppServiceCerts.ps1
    - 建立 AppServiceDnsRecords.ps1
    - Register AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>建立要由 Azure 堆疊 Web 應用程式使用的憑證

第一個指令碼搭配 Azure 堆疊憑證授權單位，若要建立 3 由 Web 應用程式所需的憑證。 確保您執行的 PowerShell 為 azurestack\administrator ClientVM 上執行指令碼︰
1.  在執行**azurestack\administrator**的 PowerShell 工作階段，執行**建立 AppServiceCerts.ps1**指令碼。  這會建立三個憑證，所需的 Web 應用程式的指令碼的相同資料夾。
2.  輸入安全 pfx 檔案，並記下，當您需要在 Azure 堆疊 Web 應用程式安裝程式中輸入該密碼。

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>若要下載並安裝 Azure 堆疊 Web 應用程式中使用的安裝程式

Appservice.exe 安裝程式將會︰
1.  提示若要接受 Microsoft 和協力廠商 Eula 使用者。
2.  收集 Azure 堆疊部署資訊。
3.  建立 blob 容器中指定的堆疊 Azure 儲存體帳戶。
4.  下載安裝 Azure 堆疊 Web 應用程式資源提供者所需的檔案。
5.  準備安裝部署 Azure 堆疊環境中的 Web 應用程式資源提供者。
6.  上傳檔案到應用程式服務儲存帳戶。
7.  [展示啟動 Azure 資源管理員範本所需的資訊。

下列步驟會引導您安裝階段︰

>[AZURE.NOTE] 您必須使用提高權限的帳戶 （本機或網域管理員） 來執行安裝程式。 如果您為 azurestack\azurestackuser 登入，系統會提示您提高權限的認證。 

1.  執行**azurestack\administrator**appservice.exe。 
2.  按一下 [**部署使用 Azure 資源管理員**]。

![Azure 堆疊應用程式服務 Technical Preview 1 安裝程式][1]

3.  檢閱並接受 Microsoft 軟體測試版授權合約，，然後按 [**下一步**。
4.  檢閱並接受第三個 partylicense 條款，然後再按 [**下一步**。
5.  檢閱應用程式雲端服務的設定資訊，然後按一下 [**下一步**]。

![Azure 堆疊應用程式服務 Technical Preview 1 應用程式服務雲端設定][2]

6. 按一下 [**連線**（Azure 堆疊訂閱方塊旁）。

![Azure 堆疊應用程式服務技術預覽 1 應用程式服務雲端設定畫面兩個][3]

7.  Azure 堆疊驗證視窗中提供**的 Azure Active Directory 服務管理員帳戶**及**密碼**，然後按一下 [**登入**。
**附註︰**這是您部署 Azure 堆疊時所提供的 Azure Active Directory 帳戶。
    - 按一下 [ **Azure 堆疊訂閱**旁的方塊右側的**向下箭號**，然後選取 [您的訂閱。

![Azure 堆疊應用程式服務 Technical Preview 1 訂閱選取範圍][5]

8.  按一下**Azure 堆疊位置**旁邊的方塊右側的**向下箭號**。
    - 選取 [**區域**]。
9. 輸入您的系統管理員的**名稱**。
10. 輸入的系統管理員的**密碼**。
11. 檢閱**SQL Server 詳細資料**，然後進行必要的變更。
12. 檢閱**系統管理員的登入帳戶**，然後進行必要的變更。
13. 輸入**系統管理員的密碼**。
14. 按一下 [**下一步**]。  此時安裝程式會立即提供的 SQL server 驗證連線詳細資料。

![Azure 堆疊應用程式服務 Technical Preview 1 訂閱選取範圍][4]    

15. 按一下 [ **Web 應用程式的預設 SSL 憑證檔案**旁的 [**瀏覽]** ，然後瀏覽至**webapps。AzureStack.Local** [先前建立](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps)的憑證。
16. 輸入您在建立憑證時設定的**憑證密碼**。
17. 按一下 [**資源提供者 SSL 憑證檔案**旁的 [**瀏覽]** ，然後瀏覽至**管理。AzureStack.Local** [先前建立](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps)的憑證。
18. 輸入您在建立憑證時設定的**憑證密碼**。
19. 按一下 [**資源提供者根憑證檔案**旁的 [**瀏覽]** ，然後瀏覽至**管理。AzureStack.Local** [先前建立](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps)的憑證。
20. 按一下 [**下一步**，安裝程式會驗證提供的憑證密碼。

![Azure 堆疊應用程式服務 Technical Preview 1 憑證的詳細資訊][6]

部署會需要大約 45 到 60 分鐘才能完成。

![Azure 堆疊應用程式服務 Technical Preview 1 安裝進度][7]

21. 安裝程式成功完成後按一下 [**結束**]。

## <a name="validate-web-apps-installation"></a>驗證 Web 應用程式安裝

1.  **Azure 堆疊主機機器**上開啟 [ **HYPER-V 管理員**]。
2.  找出**CN0 VM**及 VM**連線**。
![Azure 堆疊應用程式服務 Technical Preview 1 HYPER-V 管理員][8]
3.  此 VM 桌面上按一下**Web 雲端管理主控台**。
![Azure 堆疊應用程式服務 Technical Preview 1 管理主控台][9]
4.  瀏覽至**受管理的伺服器**。
5.  所有電腦時**準備好**進行下一個步驟。 
![Azure 堆疊應用程式服務 Technical Preview 1 受管理的伺服器狀態][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>管理伺服器與前端負載平衡器建立 DNS 記錄
1.  開啟**azurestack\administrator**PowerShell 執行個體。
2.  瀏覽至 [下載並解壓縮[必要步驟](#Download-Required-Components)中的指令碼的位置。
3.  這樣會建立 DNS 項目，若要啟用的正/反投影結束伺服器路由傳送入口網站和 web 應用程式要求執行**建立 AppServiceDnsRecords.ps1**指令碼]。  在 Web 應用程式，兩個軟體負載平衡器 (SLBs)，ARM 部署網路資源提供者中建立。 指向管理伺服器與前端伺服器。 入口網站和 ARM Azure 堆疊應用程式服務資源提供者要求移至管理伺服器。

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>ARM 註冊新部署的 Azure 堆疊 Web 應用程式提供者
1.  開啟**azurestack\administrator**PowerShell 執行個體。
2.  瀏覽至 [下載並解壓縮[必要步驟](#Download-Required-Components)中的指令碼的位置。
3.  執行**Register AppServiceResourceProvider.ps1**指令碼。 

>[AZURE.NOTE] 輸入的**虛擬機器移系統管理員**和**密碼**安裝中的欄位，或您資源的提供者註冊會失敗，請輸入使用者名稱和密碼**完全 （包括上方和下方的大小寫）** 。

## <a name="test-drive-azure-stack-web-apps"></a>測試磁碟機 Azure 堆疊 Web 應用程式

現在您已經部署，註冊的 Web 應用程式資源提供者，您可以測試，以確保租用戶，可以部署 web 應用程式。

1.  在 Azure 堆疊入口網站中，按一下 [新增網頁 + Mobile，然後按一下按一下 Web 應用程式。
2.  在 Web 應用程式刀中，輸入 [Web 應用程式] 方塊中的名稱。
3.  在 [資源] 群組中，按一下 [新增]，然後在 [資源群組] 方塊中輸入名稱。 
4.  按一下 [應用程式服務方案/位置，然後按一下 [建立新檔案。
5.  在應用程式服務方案刀中，輸入 [應用程式服務計劃] 方塊中的名稱。
6.  按一下價格層，按一下免費共用共用、 按一下 [選取]、 按一下 [確定]，然後按一下 [建立]。
7.  在下一分鐘，新的 web 應用程式的磚會出現在儀表板。 按一下 [] 磚。
8.  在 web 應用程式刀中，按一下 [瀏覽] 以檢視此應用程式的預設網站]。


**部署 WordPress、 DNN 或 Django 網站 （選用）**

1. 在 Azure 堆疊入口網站中，按一下 「 + 」，移至 Azure 服務商場部署 Django 網站，並等待順利完成。 Django web 平台使用檔案系統的資料庫，而且不需要 SQL 或 MySQL 等任何其他資源的提供者。  

2. 如果您也部署 MySQL 資源提供者，您可以部署市集 WordPress 網站。 當您收到提示時，資料庫參數時，輸入使用者名稱為*User1@Server1*（使用者名稱與您所選擇的伺服器名稱）。

3. 如果您也部署的 SQL Server 資源提供者，您可以部署市集 DNN 網站。 當您收到提示時，資料庫參數時，請挑選 [已連線至您的資源提供者的 SQL Server 的電腦中的 [資料庫]。

## <a name="next-steps"></a>後續步驟

您也可以嘗試其他[平台服務 (PaaS) 服務](azure-stack-tools-paas-services.md)，例如[SQL Server 資源提供者](azure-stack-sql-rp-deploy-short.md)和[MySQL 資源提供者](azure-stack-mysql-rp-deploy-short.md)。

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
