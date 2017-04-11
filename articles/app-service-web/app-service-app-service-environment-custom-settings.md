<properties
    pageTitle="自訂應用程式服務環境的設定"
    description="自訂設定應用程式服務環境中的設定"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>自訂設定應用程式服務環境中的設定

## <a name="overview"></a>概觀 ##
應用程式服務環境是隔離至單一客戶，因為沒有可套用至應用程式服務環境獨佔特定設定。 本文中的各種特定的自訂所使用的應用程式服務環境。

如果您沒有應用程式服務環境，請參閱[如何建立應用程式服務環境](app-service-web-how-to-create-an-app-service-environment.md)。

您可以在新的**clusterSettings**屬性中使用陣列儲存應用程式服務環境自訂項目。 此屬性的*hostingEnvironments* Azure 資源管理員實體的 [內容] 字典中找到。

下列縮寫的資源管理員範本片段顯示**clusterSettings**屬性︰


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

資源管理員範本以更新應用程式服務環境中，可以包含**clusterSettings**屬性。

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>若要更新的應用程式服務環境中使用 Azure 資源檔案總管
或者，您可以使用[Azure 資源檔案總管](https://resources.azure.com)來更新應用程式服務環境。  

1. 在資源總管] 中，移至節點的應用程式服務環境 (**訂閱** > **resourceGroups** > **提供者** > **Micrososft.Web** > **hostingEnvironments**)。 然後按一下您想要更新特定應用程式服務環境。

2. 在右窗格中，按一下 [允許互動式資源檔案總管中編輯上方工具列中的 [**讀/寫**]。  

3. 按一下 [**編輯**的藍色按鈕，讓資源管理員範本可供編輯。

4. 捲動到右窗格的底部。 **ClusterSettings**屬性是在最下方，您可以在其中輸入或更新其值。

5. 輸入 （或複製並貼上） **clusterSettings**屬性中您想要的設定值的陣列。  

6. 按一下綠色**放置**] 按鈕，以確認變更應用程式服務環境右窗格的頂端找到。

不過您提交變更時，需要大約 30 分鐘乘以的前端應用程式服務環境中，變更才會生效。
比方說，如果應用程式服務環境中有四個前端，將小時後才會大致兩個設定更新完成。 設定變更正在復原，而沒有其他縮放比例或設定變更的作業可能需要應用程式服務環境中的位置。

## <a name="disable-tls-10"></a>停用 TLS 1.0 ##
客戶週期性問題，尤其是在處理 PCI 法規遵循的客戶稽核，是明確地停用其應用程式的 [TLS 1.0 的方式。

您可以停用 TLS 1.0 透過下列**clusterSettings**項目︰

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>變更 TLS 加密套件順序 ##
客戶從另一個問題是，他們可以修改加密交涉其伺服器的清單，並且如下修改**clusterSettings** ，如下所示。 可從 [MSDN 本文] 擷取密碼所提供的套件的清單 (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx)。

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  如果 SChannel 無法了解密碼套件設定不正確的值，所有 TLS 通訊到您的伺服器可能會都停止運作。 在這種情況下，您必須從**clusterSettings**移除*FrontEndSSLCipherSuiteOrder*項目，並提交更新的資源管理員範本，以還原為預設的密碼套件設定。  請謹慎使用此功能。

## <a name="get-started"></a>快速入門
Azure 快速入門的資源管理員範本網站包含的範本[建立應用程式服務環境](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)的基本定義。


<!-- LINKS -->

<!-- IMAGES -->
