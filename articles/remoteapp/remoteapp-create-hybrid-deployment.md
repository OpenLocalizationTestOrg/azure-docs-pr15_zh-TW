<properties
    pageTitle="如何建立混合式集合 Azure RemoteApp |Microsoft Azure"
    description="瞭解如何建立連線到您的內部網路的 RemoteApp 部署。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo"/>

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>如何建立混合式集合 Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

有兩種 Azure RemoteApp 集合︰

- 雲端︰ 完全位於 Azure。 您可以選擇儲存在雲端中的所有資料 (因此雲端專用的集合) 或將 VNET 連接您的集合，儲存那里的資料。   
- 混合式︰ 包含虛擬網路的內部部署 access-這需要使用 Azure AD 和內部部署 Active Directory 環境。

不知道您需要嗎？ 查看[您需要的 Azure RemoteApp 何種類型的集合](remoteapp-collections.md)。

本教學課程中會引導您建立混合式集合的程序。 有八個步驟︰

1.  決定要使用您的集合哪些[圖像](remoteapp-imageoptions.md)。 您可以建立自訂的圖像，或使用其中一個與您的訂閱包含 Microsoft 影像。
2. 設定您的虛擬網路。 查看[VNET 規劃](remoteapp-planvnet.md)及[調整](remoteapp-vnetsizing.md)詳細資訊。
2.  建立集合。
2.  您的本機網域加入您的集合。
3.  將範本圖像新增至您的集合。
4.  設定目錄同步處理。 Azure RemoteApp 需要您與 Azure Active Directory 任一 1） 設定 Azure Active Directory 同步處理密碼同步處理] 選項，或 2） 設定 Azure Active Directory 同步處理不密碼同步處理] 選項，但使用至 AD FS 同盟網域整合。 請參閱[適用於 Active Directory RemoteApp 使用的設定資訊](remoteapp-ad.md)。
5.  發佈 RemoteApp 應用程式。
6.  設定使用者存取權。

**開始之前**

您需要執行下列動作，才能建立集合︰

- [註冊](https://azure.microsoft.com/services/remoteapp/)Azure RemoteApp。
- 用來作為 Azure RemoteApp 服務帳戶的 Active Directory 中建立使用者帳戶。 限制此帳戶的權限，使其只可以加入機器網域。
- 收集您內部網路的相關資訊︰ IP 位址資訊和 VPN 裝置詳細資料。
- 安裝[Azure PowerShell](../powershell-install-configure.md)模組。
- 收集您想要授與存取權的使用者的相關資訊。 您需要的 Azure Active Directory 使用者主要名稱 (例如，name@contoso.com)為每位使用者。 請確定 UPN 符合之間 Azure AD 與 Active Directory。
- 選擇您的範本圖像。 Azure RemoteApp 範本圖像包含應用程式，並想要發佈您的使用者的程式。 如需詳細資訊，請參閱[Azure RemoteApp 圖像選項](remoteapp-imageoptions.md)。
- 想要使用的 Office 365 專業增強版的圖像？ 請查看資訊[以下](remoteapp-officesubscription.md)。
- [設定 Active Directory RemoteApp 的](remoteapp-ad.md)。



## <a name="step-1-set-up-your-virtual-network"></a>步驟 1︰ 設定虛擬網路
您可以部署混合式集合使用現有的 Azure 虛擬網路，或您可以建立新的虛擬網路。 虛擬的網路可讓您的使用者存取您透過 RemoteApp 遠端資源的區域網路上的資料。 使用 Azure 虛擬網路可讓您集合直接網路存取其他 Azure 服務和部署至該虛擬網路的虛擬機器。

請確定您在建立您 VNET 之前檢閱[VNET 規劃](remoteapp-planvnet.md)及[VNET 大小](remoteapp-vnetsizing.md)詳細資訊。

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>建立 Azure VNET 並加入您的 Active Directory 部署

開始建立[虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。 這是 Azure 入口網站中的 [**網路**] 索引標籤。 您需要您虛擬網路連線到已同步處理到您的 Azure Active Directory 租用戶的 Active Directory 部署。

如需詳細資訊，請參閱[建立一個虛擬 Azure 入口網站的網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>請確定您虛擬網路已準備好進行 Azure RemoteApp
建立您的集合之前，現在就讓我們請確定您的新虛擬網路已準備好。 如果要驗證，可以執行下列動作︰

1. 建立您剛剛建立的 RemoteApp 虛擬網路的子網路內 Azure 虛擬機器。
2. 使用遠端桌面連線至虛擬機器。 （按一下**連線**）。
3. 加入至您想要用於 RemoteApp 相同 Active Directory 部署。

未的工作嗎？ 您的虛擬網路和子網路已可供 Azure RemoteApp ！

您可以找到關於建立 Azure 虛擬機器和使用遠端桌面[以下](https://msdn.microsoft.com/library/azure/jj156003.aspx)連線到他們的詳細資訊。

## <a name="step-2-create-an-azure-remoteapp-collection"></a>步驟 2︰ 建立 Azure RemoteApp 集合 ##



1. 在[Azure 入口網站](http://manage.windowsazure.com)中，移至 [Azure RemoteApp 頁面。
2. 按一下 [**新增 > 建立 VNET**。
3. 輸入您的集合的名稱。
4. 選擇您想要使用的標準或基本的方案。
5. 選擇您 VNET，從下拉式清單，然後您子網路。
6. 選擇加入至您的網域。
5. 按一下 [**建立 RemoteApp 集合**]。

建立您的 Azure RemoteApp 集合之後，按兩下 [集合中的名稱。 [**快速入門**] 頁面便會出現-這是您完成設定集合。

是否應該發生的問題？ 查看[混合式集合疑難排解資訊](remoteapp-hybridtrouble.md)。

## <a name="step-3-link-your-collection-to-the-local-domain"></a>步驟 3︰ 連結至本機網域您集合 ##


1. 在 [**快速入門**] 頁面上，按一下 [**加入本機的網域**]。
2. 將 Azure RemoteApp 服務帳戶新增至您的本機 Active Directory 網域。 您需要的網域名稱、 組織單位、 服務帳戶使用者名稱和密碼。

    這是如果您已追蹤[的 Azure RemoteApp 設定 Active Directory](remoteapp-ad.md)中的步驟，您所收集的資訊。


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>步驟 4︰ 連結至 Azure RemoteApp 圖像 ##

Azure RemoteApp 範本圖像包含您想要與使用者共用的程式。 您可以建立新的[範本圖像](remoteapp-imageoptions.md)或連結至現有的圖像 （一個已匯入或上傳至 Azure RemoteApp）。 您也可以連結至其中一個 Azure RemoteApp[範本圖像](remoteapp-images.md)包含 Office 365 或 Office 2013 （用於試用） 程式。

如果您要上傳新的圖像，您需要輸入名稱，然後選擇圖像的位置。 在精靈的下一個頁面上，您會看到一組的 PowerShell cmdlet-複製，並從上傳影像提高權限的 Windows PowerShell 提示中執行下列 cmdlet。

如果您連結至現有的範本圖像，只要指定影像名稱、 位置及相關聯的 Azure 訂閱。



## <a name="step-5-configure-active-directory-directory-synchronization"></a>步驟 5︰ 設定 Active Directory 目錄同步處理 ##

Azure RemoteApp 需要您與 Azure Active Directory 任一 1） 設定 Azure Active Directory 同步處理密碼同步處理] 選項，或 2） 設定 Azure Active Directory 同步處理不密碼同步處理] 選項，但使用至 AD FS 同盟網域整合。

請參閱[AD 連線](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/)-本文可協助您設定目錄整合 4 的步驟。

規劃的資訊與詳細的步驟，請參閱[目錄同步處理藍圖](http://msdn.microsoft.com//library/azure/hh967642.aspx)。

## <a name="step-6-publish-apps"></a>步驟 6︰ 發佈應用程式 ##

Azure RemoteApp 應用程式是應用程式或您提供給使用者的程式。 將其位於您上傳集合的範本圖像。 當使用者存取應用程式時，會出現在其本機的環境中，執行，但其實 Azure 中執行。

您的使用者可以存取應用程式，您需要發佈給 – 這個選項可讓前使用者會透過遠端桌面用戶端存取應用程式。

您可以將多個應用程式發佈至您的集合。 [發佈] 頁面上，按一下 [新增應用程式的**發佈**]。 您可以發行從**[開始**] 功能表的範本圖像或應用程式的範本圖像上指定的路徑。 如果您選擇新增 [**開始**] 功能表，選擇要新增的程式。 如果您選擇提供應用程式的路徑，提供的名稱應用程式與安裝範本圖像上位置的路徑。

## <a name="step-7-configure-user-access"></a>步驟 7︰ 設定使用者存取權 ##

現在您已經建立您的集合，您需要新增您想要能夠使用遠端資源的使用者。 您用來建立此 Azure RemoteApp 集合所提供的存取權需要存在於與訂閱相關聯的 Active Directory 租用戶中的使用者。

1.  從 [快速入門] 頁面上，按一下 [**設定使用者存取權限**]。
2.  輸入您想要授與存取權 （從 Active Directory) 的公司帳戶或 Microsoft 帳戶。

    **附註︰**

    請確定您使用“user@domain.com”格式。

    如果您使用的 Office 365 專業增強版集合中，您必須使用 Active Directory 身分識別為您的使用者。 這可協助驗證授權。


3.  使用者經過驗證後，按一下 [**儲存**]。


## <a name="next-steps"></a>後續步驟 ##
這樣就可以-成功建立並部署您 Azure RemoteApp 混合式集合。 下一個步驟是讓您下載並安裝遠端桌面用戶端的使用者。 您可以尋找 Azure RemoteApp 快速入門] 頁面上的用戶端的 URL。 然後有使用者登入用戶端存取您發佈的應用程式。



### <a name="help-us-help-you"></a>協助我們協助您
您知道，除了評等這份文件] 下方，向下進行註解，您可以進行變更本身的文件嗎？ 遺漏的內容？ 有錯誤？ 我是否撰寫的項目只混亂？ 向上捲動，然後按一下 [若要變更的 [**編輯 GitHub** -那些以供檢閱，傳送給我們，然後，我們在登入，就會看到您的變更和這裡改良項目。
