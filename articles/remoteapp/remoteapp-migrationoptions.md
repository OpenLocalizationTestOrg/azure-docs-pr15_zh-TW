
<properties 
    pageTitle="登出 Azure RemoteApp 移轉選項 |Microsoft Azure" 
    description="瞭解登出 Azure RemoteApp 移轉選項。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>登出 Azure RemoteApp 移轉選項

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

如果您已經停止[退休通知](https://go.microsoft.com/fwlink/?linkid=821148)，使用 Azure RemoteApp 或因為您已完成您的評估，您需要從 Azure RemoteApp 移轉到另一個應用程式服務。 有兩種不同的方法，移轉︰ 自我管理 （通常稱為 [IaaS] 服務為基礎結構） 部署或完整的管理 （通常稱為平台的服務） 或 [PaaS/SaaS] 的服務的軟體提供。 

自助 IaaS 是管理、 營運，以及您直接部署虛擬機器 (Vm) 或實體系統上所擁有的自助部署。 其他結束時，完全受管理的 PaaS/SaaS 提供更多等 Azure RemoteApp-合作夥伴提供服務圖層處理作業遠端解決方案的頂端，而服務時，客戶，請執行某些圖像和應用程式的管理。

閱讀的詳細資訊，包括不同的主機服務選項的範例。    

## <a name="self-managed-iaas-solutions"></a>自我管理 (IaaS) 解決方案

### <a name="rds-on-iaas"></a>**在 IaaS RDS** 
您可以將原生工作階段為基礎 （在 Windows Server) 的遠端桌面服務的部署使用 RemoteApp 或桌上型電腦上內部部署或主控環境 （例如 Azure Vm）。 在 IaaS 部署 RDS 適合已經熟悉的客戶，以及有 RDS 部署與現有技術專業知識。 

> [AZURE.NOTE]
> 您需要大量授權的軟體保證 (SA) RDS 用戶端存取授權才能使用這個部署選項。

使用 [部署及修補 （讀取[概觀](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/)，然後[移取得這些](https://aka.ms/rdautomation)） 的範本時，部署上 Azure Vm RDS，比以前更容易。 雖然有多個自訂與設定，您可以使用 [[自動調整大小的指令碼](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76)，取得相同的彈性縮放功能中 Azure RemoteApp Azure 傳統部署模型資源 （不 Azure 資源模型資源）。 當您部署 RDS Azure Vm 上的時，提供支援透過[Azure 支援](https://azure.microsoft.com/support/plans/)，相同的支援專業人員支援 Azure RemoteApp。 您可以取得成本連絡[Azure 支援](https://azure.microsoft.com/support/plans/)，根據您現有的使用方式的估計值，或您可以自行執行計算使用推出，發行成本計算機。  此外，N 數列 Vm （目前在私人預覽版本），您可以加 vGPU-進一步瞭解關於新增 vGPU 以及如何駕馭[RDS 改良功能，在 Windows Server 2016 中的](https://myignite.microsoft.com/videos/2794)，我們 Ignite 工作階段。   

我們可以協助您部署[Windows Server 2012 R2](http://aka.ms/rdsonazure)和[Windows Server 2016](http://aka.ms/rdsonazure2016)逐步部署指南。 請參閱[遠端桌面部落格](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services)的最新消息。
 
### <a name="citrix-on-iaas"></a>**在 IaaS Citrix** 
部署工作階段型 XenApp 或 XenDesktop 可以原生 Citrix 部署內部部署或裝載環境 (例如 Azure Vm 上)。 

請參閱逐步部署指南 》 [Citrix XA 7.6 上 Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx)，如需詳細資訊。 進一步瞭解[在 Azure Citrix](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx)，包括價格計算機。 您也可以找到[Citrix 連絡](http://citrix.com/English/contact/index.asp)討論您使用的選項。

## <a name="fully-managed-paassaas-offerings"></a>完整的管理 (PaaS/SaaS) 產品

### <a name="citrix-cloud"></a>**Citrix 雲端** 
[Citrix 現有的雲端方案](https://www.citrix.com/products/citrix-cloud/)的相同架構 Citrix XenApp Express。 Citrix 提出[50%折扣升級](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/)現有的 Azure RemoteApp 客戶。 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express （在技術預覽版本）**
[註冊其技術預覽](http://now.citrix.com/remoteapp)，並觀看其[Ignite 工作階段](https://myignite.microsoft.com/videos/2792)(開頭分 20:30)。 XenApp Express 是 Citrix 雲端架構相同，只不過它包含簡化的管理使用者介面與其他功能和 Azure RemoteApp 類似的功能。 

進一步瞭解[Citrix XenApp Express](http://now.citrix.com/remoteapp)。   

### <a name="citrix-service-provider-program"></a>**Citrix 服務提供者計劃** 
Citrix 服務提供者程式可輕鬆進行虛擬雲端運算 SMBs，以簡化的服務提供者提供這些他們想簡便 pay-as-you-go 模型中的服務。 Citrix 服務提供者放大 Microsoft SPLA 事業並使用任何裝置展開其 RDS 平台投資，任何位置存取，最大的應用程式支援、 豐富的體驗，增加的安全性及增加的擴充。 接著，Citrix 服務提供者吸引多個訂閱者、 增加客戶滿意度和減少其操作的成本。 [進一步瞭解](http://www.citrix.com/products/service-providers.html)或[尋找合作夥伴](https://www.citrix.com/buy/partnerlocator.html)。

### <a name="microsoft-hosted-service-provider"></a>**Microsoft 代管服務提供者** 
主機服務提供者通常是提供完整的管理裝載於 Windows 桌面與應用程式服務，可能包含管理 Azure 資源、 作業系統、 應用程式，與技術服務使用協力廠商的授權合約 Microsoft 與其他軟體提供者，以及要允許充任訂閱者存取授權 (SAL) 服務提供者授權合約。 詳細資料] 與 [連絡人的資訊是專為協助客戶其 Azure RemoteApp 移轉主機服務提供者，則會提供下列資訊。 查看已完成上 IaaS 學習路徑和評估 RDS[目前的代管服務提供者清單](http://aka.ms/rdsonazurecertified)。  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en)專門 Isv 轉換到雲端及 ISV' 想要最佳化其目前的雲端安裝。 ASPEX 提供各種受管理的服務與 devops，諮詢服務。  

主要位置︰ Antwerp，比利時

操作區域︰ 西歐

合作夥伴狀態: [「 銀色 」](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Microsoft 雲端服務提供者︰ 是

提供工作階段 RemoteApp 和桌面解決方案︰ 是的同時

Azure RemoteApp 移轉解決方案︰ 是的[了解更多](https://www.aspex.be/en/azure-remote-apps)

**連絡人︰**

- 電話︰ +3232202198
- 郵件︰[info@aspex.be](mailto:info@aspex.be)
- Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (平台名稱︰ MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com)是 IT 公司簡化、 最佳化和不按比例縮放的移轉與傳送遠端桌面與遠端應用程式]，在 Microsoft Azure 雲端基礎架構自動化平台。 

MyCloudIT 平台 95%，成本 30%的 Azure 減少部署時間，並將其客戶的整個 IT 基礎結構移到雲端中的幾個按鍵。 合作夥伴現在可以從一個全域的儀表板管理客戶、 世界各地的服務使用者從未，然後放大收入，但不新增其他成本或擴充 Azure 訓練課程。  

主要位置︰ 達拉斯，傳送，美國

操作區域︰ 世界各地

合作夥伴狀態︰ [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Microsoft 雲端服務提供者︰ 是

提供工作階段 RemoteApp 和桌面解決方案︰ 是的同時

Azure RemoteApp 移轉解決方案︰ 是的[了解更多](https://mycloudit.com/remote-app-microsoft/)

**連絡人︰**
- 方柏納 Garoutte，VP 的商業部門

   電話︰ 972-218-0741

   電子郵件︰[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com)專門提供主控桌面解決方案，提供完整的桌面和 ISV 應用程式將其從 Azure 和自己的資料中心內建在 Microsoft 技術基準全域用戶端的體驗。

主要位置︰ 倫敦的公司，uk （英國);新加坡;休斯頓、 傳送

操作區域︰ 世界各地

合作夥伴狀態︰ Gold

Microsoft 雲端服務提供者︰ 是

提供工作階段 RemoteApp 和桌面解決方案︰ 是的同時

Azure RemoteApp 移轉解決方案︰ 是的[了解更多](http://www.acuutech.com/ara-migration/)

**連絡人︰**

- 英國︰

  5/6 紐約屋 Langston 道路

  Loughton，Essex IG10 3TQ
  
  電話: + 44 (0) 20 8502 2155年
 
- 新加坡︰

  100 Cecil #09 02、 地址 
  
  地球，新加坡 069532
  
  電話: + 65 6709 4933
 
- 北美地區︰ 

  3601 S Sandman St。
  
  套件 200，77098 休斯頓，傳送
  
  電話︰ +1 713 691 0800

## <a name="need-more-help"></a>需要更多協助嗎？
仍需要協助您選擇，或進一步有疑問嗎？ 您可以使用下列兩種方法之一來取得協助。 

1.  電子郵件與我們連絡[arainfo@microsoft.com](mailto:arainfo@microsoft.com)。
2.  連絡[支援部門 Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 必須先開啟[Azure 支援的大小寫](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
3.  打電話給我們。 [尋找本機銷售數字](https://azure.microsoft.com/overview/sales-number/)。
