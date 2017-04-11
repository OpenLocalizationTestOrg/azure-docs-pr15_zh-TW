<properties 
    pageTitle="如何建立使用 Azure 資源管理員範本 ILB ASE |Microsoft Azure" 
    description="瞭解如何建立使用 Azure 資源管理員範本內部負載平衡器 ASE。" 
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
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>如何建立使用 Azure 資源管理員範本 ILB ASE

## <a name="overview"></a>概觀 ##
虛擬網路內部的位址，而不是公用 VIP 可以建立應用程式服務環境。  Azure 元件，稱為內部負載平衡器 (ILB) 會提供此內部的地址。  可以使用 [Azure 入口網站建立 ILB ASE。  它也可以建立使用自動化透過 Azure 資源管理員範本。  本文將引導的步驟和 Azure 資源管理員範本建立 ILB ASE 時所需的語法。

自動建立 ILB ASE 是三個步驟︰
1. 第一次基底 ASE 會建立在虛擬網路，而不公用 VIP 使用內部負載平衡器位址。  在此步驟中，將根網域名稱會指派到 ILB ASE。
2. ILB ASE 建立後上, 傳的 SSL 憑證。  
3. 上傳的 SSL 憑證明確指派給 ILB ASE 為其 「 預設 」 SSL 憑證。  這個 SSL 憑證用於 SSL 流量 ILB ASE 上的應用程式時應用程式收件者使用一般的根網域指派給 ASE (例如 https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>建立基本 ILB ASE ##
範例 Azure 資源管理員範本與相關聯的參數檔案，並用於 GitHub[以下][quickstartilbasecreate]。

大部分的*azuredeploy.parameters.json*檔案中的參數是常見建立 ILB ASEs 以及 ASEs 繫結至公用 VIP。  通話參數的特殊，請參閱下方的清單] 或 [的是記事的唯一的建立 ILB ASE 時︰


- *interalLoadBalancingMode*︰ 在大多數情況下設定此 3，這表示連接埠 80/443、 HTTP/HTTPS 流量和控制項資料通道連接埠聆聽 FTP 服務上 ASE，將結合 ILB 配置虛擬網路內部的地址。  如果為 2 改為設定此屬性，然後 FTP 服務相關 HTTP/HTTPS 流量將會保留在公用 VIP 時，將 ILB 地址，結合連接埠 （控制項與資料頻道）。
-  *dnsSuffix*︰ 此參數會將指定給 ASE 預設根網域。  Azure 應用程式服務公用變化，在所有 web 應用程式的預設根網域會是*azurewebsites.net*。  不過 ILB ASE 是內部客戶的虛擬網路，因為它沒有意義使用公用服務的預設根網域。  不過，ILB ASE 應有公司內部虛擬網路中使用有意義的預設根網域。  例如，假設 Contoso 公司可能會針對應用程式是為了讓解析與中 Contoso 虛擬網路存取，才會使用預設根網域的*內部 contoso.com* 。 
-  *ipSslAddressCount*︰ 此參數都會自動預設*azuredeploy.json*檔案中的 0 的值為因為 ILB ASEs 只能有單一 ILB 位址。  有 ILB ASE，沒有明確 IP SSL 地址，因此 ILB ASE IP SSL 位址集區必須設定為零，否則佈建就會發生錯誤。 

一旦*azuredeploy.parameters.json*檔案具有已填入的 ILB ASE，ILB ASE 就可以建立使用下列 Powershell 程式碼片段。  變更檔案路徑，以符合您的電腦上 Azure 資源管理員範本檔案位置。  此外，請記得提供您自己的 Azure 資源管理員部署名稱及資源群組名稱的值。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure 資源管理員之後範本會送出會建立 ILB ASE 幾個小時。  建立完成後，ILB ASE 會顯示在清單中的應用程式服務環境觸發部署的訂閱 UX 入口網站。

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>上傳及設定 「 預設 」 SSL 憑證 ##

ILB ASE 建立後，SSL 憑證應該 ASE 與相關聯的 「 預設 」 SSL 憑證用於建立 SSL 連線至應用程式。  繼續假設 Contoso Corporation 範例中，如果 ASE 的預設 DNS 尾碼是*內部 contoso.com*，然後連線至*https://some-random-app.internal-contoso.com*需要 SSL 憑證有效的 **.internal contoso.com*。 

有多種方式取得有效的 SSL 憑證包括內部 Ca、 憑證傳來外部的發行者，以及使用自我簽署的憑證。  無論 SSL 憑證的來源，下列憑證屬性必須正確設定︰

- *主旨*︰ 這個屬性必須設定為 **.your 根網域-here.com*
- *主體的別名*︰ 這個屬性必須包含兩**.your 根網域-here.com*，及* *.scm.your-根-網域-here.com*。  第二個項目的是將使用的表單*your-app-name.scm.your-root-domain-here.com*位址會進行 SSL 連線至每一個應用程式相關聯的 SCM/Kudu 網站。

使用有效的 SSL 憑證之後，需要兩個額外的準備步驟。  SSL 憑證必須轉換/儲存為.pfx 檔。  請記住，.pfx 檔必須包含所有中繼和根憑證，也必須使用密碼保護。

然後的結果.pfx 檔必須轉換成 base64 字串，因為會使用 Azure 資源管理員範本上載 SSL 憑證。  Azure 資源管理員範本是以文字檔案，因為.pfx 檔必須要轉換成 base64 字串，所以您可能會包含做為參數的範本。

下列 Powershell 程式碼片段顯示產生自我簽署的憑證的範例，憑證匯出為.pfx 檔，將.pfx 檔轉換成 base64 編碼的字串，然後儲存 base64 編碼字串至另一個檔案。  Base64 編碼的 Powershell 程式碼的[Powershell 指令碼的部落格]加以調整[examplebase64encoding]。

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
一旦 SSL 憑證已成功產生，會轉換為 base64 編碼字串，設定[預設的 SSL 憑證]GitHub 範例 Azure 資源管理員範本[configuringDefaultSSLCertificate]可用。

以下列出*azuredeploy.parameters.json*檔案中的參數︰

- *appServiceEnvironmentName*︰ 設定 ILB ASE 的名稱。
- *existingAseLocation*︰ 文字字串，包含 Azure 區域 ILB ASE 部署的位置。  例如: 「 南部美國中部 」。
- *pfxBlobString*: based64 編碼.pfx 檔的字串表示。  使用先前顯示的程式碼片段，想要複製的 「 exportedcert.pfx.b64 」 中所包含的字串，然後貼中為*pfxBlobString*屬性的值。
- *密碼*︰ 安全性.pfx 檔所使用的密碼。
- *certificateThumbprint*: 憑證指紋。  如果您從 Powershell 擷取此值 (例如*$certificate。指紋*從舊的程式碼片段)，您可以使用為值-是。  但是如果您從 Windows [憑證] 對話方塊中複製的值，請記得取出無關的空格。  *CertificateThumbprint*看起來應該像︰ AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: 好記的字串識別碼為自己選取用來識別憑證。  名稱用於代表 SSL 憑證*Microsoft.Web/certificates*實體唯一 Azure 資源管理員識別碼的一部分。  名稱**必須**結束以下列後置字元︰ \_yourASENameHere_InternalLoadBalancingASE。  這個後置字元會使用憑證用於保護 ILB 啟用 ASE 標記為入口網站。


*Azuredeploy.parameters.json*縮寫的範例如下所示︰


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

一旦已填入*azuredeploy.parameters.json*檔案，您可以使用下列 Powershell 程式碼片段設定預設的 SSL 憑證。  變更檔案路徑，以符合您的電腦上 Azure 資源管理員範本檔案位置。  此外，請記得提供您自己的 Azure 資源管理員部署名稱及資源群組名稱的值。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure 資源管理員之後範本會送出花費大致主版頁面分鐘的時間的分鐘數 ASE 前端以套用變更。  比方說，使用預設大小 ASE，使用兩個前端，範本會需要大約一小時又二十分鐘才能完成。  當正在執行範本 ASE 會無法調整。  

範本完成後，可以透過 HTTPS 存取 ILB ASE 上的應用程式，並會使用預設的 SSL 憑證安全連線。  應用程式上 ILB ASE 收件者使用的應用程式的名稱加上的預設主機名稱時，就會使用預設的 SSL 憑證。  比方說*https://mycustomapp.internal-contoso.com*可使用的預設 SSL 憑證 **.internal contoso.com*。

不過，就像公用多租用戶服務上執行應用程式，開發人員可以也設定個別的應用程式，自訂主機名稱，然後再設定 [唯一的 SNI SSL 憑證繫結的個別的應用程式。  


## <a name="getting-started"></a>快速入門

若要開始使用應用程式服務環境，請參閱[應用程式服務環境簡介](app-service-app-service-environment-intro.md)

所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
