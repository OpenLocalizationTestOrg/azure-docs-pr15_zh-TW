<properties 
    pageTitle="Azure RemoteApp 常見問題集 |Microsoft Azure" 
    description="了解解答的常見問題集 Azure RemoteApp。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Azure RemoteApp 常見問題集

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

我們對 Azure RemoteApp 下列疑問。 有其他人？ 請造訪[RemoteApp 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp)，讓我們知道您需要知道，或下方下拉式註解。

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>找不到您要尋找的內容？ 有我們未回答的問題嗎？
如果您找不到資訊在需要時，或您有其他我們無法涵蓋以下的問題，請移至[Azure RemoteApp 論壇](http://aka.ms/araforum)並提出您的問題。 我們隨時都可以新增更多的答案。

## <a name="what-is-azure-remoteapp"></a>什麼是 Azure RemoteApp？ ##


- **什麼是 Azure RemoteApp？** RemoteApp 是 Azure 服務可協助您提供許多不同的使用者裝置從安全、 遠端存取應用程式。 進一步瞭解[Azure RemoteApp](remoteapp-whatis.md)。
- **什麼是 [部署] 選項？** 有兩種 RemoteApp 集合︰ 雲端與混合式部署。 您需要的一個取決於因素，例如是否需要加入的網域。 我們討論的決策所有[以下](remoteapp-collections.md)。

## <a name="quick-tips-on-using-azure-remoteapp"></a>使用 Azure RemoteApp 快速秘訣 ##
- **多久我被中斷連線？多久我閒置之前可讓我在開機？** 4 小時。 如果您或您的使用者的其中一個閒置 4 小時，您將會自動登出 Azure RemoteApp。 請參閱[Azure 訂閱及服務限制，配額和限制](../azure-subscription-service-limits.md)的其他預設設定。
- **是否可以免費試用這項服務？** [是]。 沒有免費試用 30 天。 您可以在試用期結束之後, 轉場的付費帳戶 （您可以使用生產環境中） 或停止使用服務。 移至[portal.azure.com](http://portal.azure.com)開始免費試用版-建立新的執行個體的 RemoteApp。 使用免費的試用版，您可以建立 RemoteApp 2 的執行的個體，與每個執行個體的 10 個使用者。 請記住，此試用版只放 30 天。
## <a name="azure-remoteapp-subscription-details"></a>Azure RemoteApp 訂閱詳細資料 ##

- **服務限制是什麼？** 您可以瞭解的預設設定和 Azure RemoteApp [Azure 訂閱和服務限制，配額，以及限制式](../azure-subscription-service-limits.md)中的服務限制。 讓我們知道您是否擁有更多的問題。
- **我必須擁有多少使用者？** 有 20 個使用者的最小值。 我要重複要把清除-最小值為 20。 會向您收費的 20。 
- **多少 RemoteApp 成本？** 請參閱[Azure RemoteApp 定價詳細資料](https://azure.microsoft.com/pricing/details/remoteapp/)。
- **其中一種集合成本超過另一個嗎？** 是的就可以了，視您的集合需求而定。 混合式集合需要從 Azure RemoteApp 連線到您的內部部署網路。 如果您使用現有的 VNET/Express 路由，有任何其他成本。 但如果您使用新的 Azure VNET 及閘道器或 Express 路由，您需要付費[VPN 閘道器](https://azure.microsoft.com/pricing/details/vpn-gateway)或[快速傳送](https://azure.microsoft.com/pricing/details/expressroute/)。 成本 （詳細說明連結） 是在您每月 Azure RemoteApp 成本。

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>集合-支援的項目，您應該使用其、 與其他人
- **支援自訂-業務 (LOB) 應用程式？** [是]。 若要 Azure RemoteApp 中使用自訂的應用程式，建立[自訂的範本圖像](remoteapp-create-custom-image.md)，然後上傳至 RemoteApp 集合。
- **我的自訂 LOB 應用程式會在 Azure RemoteApp 中運作？** 圖的出測試最好的方法。 查看[RD 相容性中心](http://www.rdcompatibility.com/compatibility/default.aspx)。
- **（雲端或混合式部署） 的部署方法最適合我的組織？** 如果您要使用單一登入 (SSO) 完全整合，以及保護內部部署網路連線，混合式集合會提供最完整的使用體驗。 雲端集合提供敏捷式且容易的方式來找出您的部署，使用多個驗證方法。 進一步瞭解[部署選項](remoteapp-whatis.md)。
- **我們有 SQL 或另一個資料庫有內部部署或 Azure 中。部署類型我們應該使用？** SQL 或後端資料庫在哪裡而定。 如果資料庫在私人網路，請使用混合式集合。 如果資料庫網際網路上公開，並讓用戶端連線的連線，您可以使用雲端集合。
- **磁碟機對應、 USB 和序列連接埠、 剪貼簿共用和印表機重新導向又是什麼？** Azure RemoteApp 支援所有這些功能。 依預設會啟用剪貼簿共用與印表機重新導向。 您可以瞭解更多關於重新導向[以下](remoteapp-redirection.md)。 


## <a name="template-images"></a>範本圖像
- **我可以使用雲端或現有的虛擬機器文件另存範本我 RemoteApp 集合？** 是 ！ 您可以建立根據 Azure VM 圖像、 使用其中一個與您的訂閱，包含圖像或建立自訂的圖像。 查看[RemoteApp 圖像選項](remoteapp-imageoptions.md)。


## <a name="network-options"></a>網路選項
- **混合式集合需要 VNET。我們可以使用我們現有 VNET 嗎？** 您可以如果現有 VNET Azure VNET。 請參閱 「 步驟 1︰ 設定虛擬網路 」 在[混合式集合指示](remoteapp-create-hybrid-deployment.md)如需詳細資訊。
- **可以使用 VNET 雲端集合嗎？** 您確實可以。 請參閱[建立雲端集合](remoteapp-create-cloud-deployment.md)，特別是步驟 1，如需詳細資訊。

## <a name="authentication-options"></a>驗證選項



- **如何驗證？支援的方法？** 雲端集合支援 Microsoft 帳戶和 Azure Active Directory 帳戶，也就是以及 Office 365 帳戶。 混合式集合從 Windows Server Active Directory 部署; 支援有 （使用的工具，例如[Azure Active Directory 同步處理](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)） 同步處理的 Azure Active Directory 帳戶具體來說，或是使用密碼同步處理] 選項已同步處理設定的 Active Directory Federation Services (AD FS) 同盟與同步處理。 您也可以設定[多重因素驗證 (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/)。

>[AZURE.NOTE]Azure Active Directory 使用者必須與您的訂閱相關聯的租用戶。 （您可以檢視及修改您的訂閱在入口網站中的 [**設定**] 索引標籤上。 請參閱[變更租用戶的 Azure Active Directory RemoteApp 使用](remoteapp-changetenant.md)如需詳細資訊。）

- **為什麼不能讓我的 Azure Active Directory 帳戶存取？** Azure Active Directory 使用者必須與您的訂閱相關聯的目錄。 您可以檢視或修改入口網站中的 [設定] 索引標籤上的目錄。 如需詳細資訊，請參閱[變更租用戶的 Azure Active Directory RemoteApp 使用](remoteapp-changetenant.md)。

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>用戶端-何種裝置可以使用存取 Azure RemoteApp 嗎？
您可以尋找建議的用戶端資訊，包括安裝不同的用戶端存取[您的應用程式中 Azure RemoteApp](remoteapp-clients.md)的步驟。

- **哪些裝置與作業系統是否用戶端應用程式支援？**
第一個、 電腦及平板電腦的內容︰ 
    - 在 Windows 10 （用戶端預覽版本）
    - Windows 8.1 和 Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Android 平板電腦
    - ipad 上與電話︰
    - iPhone
    - 在 android 手機
    - 在 Windows Phone
 
    [下載](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)現在 RemoteApp 用戶端。
- **Azure RemoteApp 是否支援型的用戶端？** 是的支援下列內嵌 Windows 型用戶端︰
    - Windows 內嵌標準 7
    - Windows 內嵌 8 標準
    - Windows 內嵌 8.1 產業 Pro
    - 在 Windows 10 IoT 企業版

- **哪個版本的 Windows Server 功能受支援的遠端桌面工作階段 host （主機) (RDSH)？** Windows Server 2012 R2。

## <a name="support-and-feedback"></a>支援和意見反應


- **什麼是 RemoteApp 的支援計劃？** 免費提供管理帳單與訂閱支援。 技術支援是透過[Azure 服務方案](https://azure.microsoft.com/support/plans/)。 您也可以透過[Azure 討論論壇，在](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)我們取得免費社群支援。 
- **我要如何送出意見反應？** 請造訪[意見反應](https://feedback.azure.com/forums/247748-azure-remoteapp/)。
- **可以與人員若要進一步瞭解 Azure RemoteApp？** 除了我們[討論區](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)，這是絕佳張貼問題，您可以加入每週[詢問專家網路研討會](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)，討論 RemoteApp 的所有項目。
- **為何 RemoteApp 文件？** 我們樂意要求您。 除了在入口網站的說明抽屜的說明內容 （只要按一下**？** 在任何頁面上入口網站），下列文章可教您所有相關 RemoteApp:
    - **快速入門︰**
        - [什麼是 RemoteApp？](remoteapp-whatis.md)
        - [RemoteApp 範本圖像中有什麼？](remoteapp-images.md)
        - [授權運作方式為何？](remoteapp-licensing.md)
        - [如何 RemoteApp 和 Office 共同作業？](remoteapp-o365.md)
        - [如何重新導向中運作 RemoteApp](remoteapp-redirection.md)嗎？
    - **部署︰**
        - [建立自訂的範本圖像](remoteapp-create-custom-image.md)
        - [建立混合式集合](remoteapp-create-hybrid-deployment.md)
        - [建立雲端集合](remoteapp-create-cloud-deployment.md)
        - [設定 Azure Active Directory RemoteApp](remoteapp-ad.md)
        - [發佈應用程式中 RemoteApp](remoteapp-publish.md)
    - **管理︰**
        - [新增使用者](remoteapp-user.md)
        - [設定和使用 RemoteApp 的最佳作法](remoteapp-bestpractices.md)  

    影片 ！ 我們也有數個 RemoteApp 的相關影片。 有些會提供簡介 （[Azure RemoteApp 簡介](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)），而其他人會引導您完成部署 （[雲端部署](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be)與[混合式部署](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)）。 簽出 ！

 
### <a name="help-us-help-you"></a>協助我們協助您 
您知道，除了評等這份文件] 下方，向下進行註解，您可以進行變更本身的文件嗎？ 遺漏的內容？ 有錯誤？ 我是否撰寫的項目只混亂？ 向上捲動，然後按一下 [若要變更的 [**編輯 GitHub** -那些以供檢閱，傳送給我們，然後，我們在登入，就會看到您的變更和這裡改良項目。
