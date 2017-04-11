<properties
    pageTitle="網頁上 Azure 堆疊的已知的問題和疑難排解的應用程式 |Microsoft Azure"
    description="詳細的指引，部署 Azure 堆疊的 Web 應用程式"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
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
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Web 應用程式資源提供者的已知的問題與疑難排解

> [AZURE.NOTE] Azure 堆疊 TP1 部署僅適用於下列資訊。

如果您遇到問題部署，或使用 Azure 堆疊上的 Web 應用程式時請參閱下方的指導方針進行。

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure 堆疊 Web 應用程式中無法使用入口網站

![Azure 堆疊 Web 應用程式建立新 Web 應用程式][1]

完成之後，您[註冊 Azure 堆疊 Web 應用程式提供者的](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm)如果您無法找到 Web + 行動刀，然後請嘗試下列動作︰
* **登出入口網站**，並**關閉瀏覽器**，然後在**新的瀏覽器視窗登入入口網站**並再試一次。

如果您仍然無法看見的網頁與行動請嘗試下列動作︰

1.  **HYPER-V 管理員** **Azure 堆疊主機機器**上找出**xRPVM**及 VM**連線**。
2.  開啟**命令提示字元以系統管理員身分**，然後執行**IISRESET**
3.  返回**ClientVM**並開啟**新的瀏覽器視窗**，**登入入口網站**，然後再試一次。

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>部署失敗時建立新的 [資源] 群組中的 Web 應用程式

第一個預覽的 Web 應用程式中，**所有的 Web 應用程式**必須建立 Web 應用程式資源提供者 （**AppService 本機**） 為相同的 [資源] 群組中。  這是已知的問題，並會解析在未來的預覽。

## <a name="other-issues"></a>其他問題

如果您遇到其他 Azure 堆疊上的 Web 應用程式的問題請張貼中 [Azure 堆疊論壇] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) 監視我們的小組成員的文章。


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
