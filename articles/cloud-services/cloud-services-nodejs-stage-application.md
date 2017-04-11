<properties 
    pageTitle="階段的雲端服務部署 (Node.js) |Microsoft Azure" 
    description="瞭解如何將應用程式部署 Azure 至暫存的環境，然後使用虛擬 IP (VIP) 交換生產環境中部署。" 
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



# <a name="staging-an-application-in-azure"></a>執行 Azure 中的應用程式

若要先測試，然後將其移至 [生產環境的應用程式會在網際網路上存取 Azure 中執行的環境可以部署封裝的應用程式。 執行環境的是完全一樣生產環境，但您只能存取由 Azure 產生模糊 url 分段應用程式。 您已驗證您的應用程式運作正常之後，可以將部署到生產環境，藉由執行虛擬 IP (VIP) 交換。

> [AZURE.NOTE] 本文中的步驟只適用於一個 Azure 雲端服務裝載的節點應用程式。

## <a name="step-1-stage-an-application"></a>步驟 1︰ 階段應用程式

這項工作會說明如何使用**Microsoft Azure PowerShell**階段應用程式。

1.  當發佈服務，只要傳送**-位置****發佈 AzureServiceProject** cmdlet 的參數。

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  登入[Azure 傳統入口網站]，然後選取**雲端服務**。 建立雲端服務，並已經更新**臨時**欄狀態，以**執行**之後，按一下服務名稱。

    ![顯示執行服務的入口網站][cloud-service]

3.  選取 [**儀表板**，然後選取 [**執行**。

    ![雲端服務儀表板][cloud-service-dashboard]

4. 請注意右邊的**網站 URL**項目中的值。 DNS 名稱是 Azure 產生的模糊內部識別碼。

    ![網站 url][cloud-service-staging-url]

現在您可以驗證的應用程式運作正常執行環境中使用暫存的網站 URL。

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>步驟 2︰ 升級生產交換 Vip 的應用程式

您已驗證升級的版本中執行環境的應用程式之後，您可以快速使其提供生產調換執行與生產環境虛擬 IPs (Vip)。

> [AZURE.NOTE] 此步驟假設您已經部署至生產環境的應用程式，並分段升級的應用程式的版本。

1.  登入[Azure 傳統入口網站]，按一下 [**雲端服務**，然後選取服務名稱。

2.  從 [**儀表板**選取**臨時**，，然後按一下頁面底部的 [**交換**。 這會開啟 [VIP 交換] 對話方塊。

    ![vip 交換] 對話方塊][vip-swap-dialog]

3.  檢閱資訊，然後再按一下**[確定]**。 兩個部署開始臨時部署會切換至生產以及生產部署會切換至臨時更新。

成功，您有分段部署，而且由臨時中部署交換 Vip 升級產品部署。

## <a name="additional-resources"></a>其他資源

- [如何將生產服務升級部署調換 Vip Azure 中]

[Azure 傳統入口網站]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[如何將生產服務升級部署調換 Vip Azure 中]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
