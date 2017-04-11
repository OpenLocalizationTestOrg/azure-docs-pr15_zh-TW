<properties 
    pageTitle="如何建立 Azure RemoteApp 雲端集合 |Microsoft Azure" 
    description="瞭解如何建立部署的 Azure RemoteApp 會儲存在 Azure 雲端的資料。" 
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

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>如何建立 Azure RemoteApp 雲端集合

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

有兩種[Azure RemoteApp 集合](remoteapp-collections.md)︰ 

- 雲端︰ 完全位於 Azure。 您可以選擇儲存在雲端中的所有資料 (因此雲端專用的集合) 或將 VNET 連接您的集合，儲存那里的資料。   
- 混合式︰ 包含虛擬網路的內部部署 access-這需要使用 Azure AD 和內部部署 Active Directory 環境。

本教學課程中會引導您建立的雲端集合的程序。 有四個步驟︰ 

1.  建立 Azure RemoteApp 集合。
2.  您也可以選擇設定目錄同步處理。 如果您使用的 Azure AD + Active Directory，您必須同步處理的使用者、 連絡人與您內部部署的 Active Directory Azure AD 租用戶的密碼。
5.  發佈應用程式。
6.  設定使用者存取權。


**開始之前**

您需要執行下列動作，才能建立集合︰

- [註冊](https://azure.microsoft.com/services/remoteapp/)Azure RemoteApp。 
- 收集您想要授與存取權的使用者的相關資訊。 這可以是 Microsoft 帳戶資訊或使用者的 Active Directory 工作帳戶資訊。
- 此程序假設您是 [移至使用您的訂閱中提供的範本圖像的其中一個，或您已上傳您想要使用的範本圖像。 如果您需要上傳的不同的範本圖像，則您可以執行的範本圖像的頁面。 只要按一下 [**上傳的範本圖像**，然後依照精靈中的步驟。 
- 想要使用的 Office 365 專業增強版的圖像？ 請查看資訊[以下](remoteapp-officesubscription.md)。
- 想要提供自訂應用程式或 LOB 程式？ 建立新的[圖像](remoteapp-imageoptions.md)，並在您的雲端集合中使用。
- 找出您是否需要連線到 VNET。 如果您選擇要連線至 VNET，確認它是否符合[縮放指導方針](remoteapp-vnetsizing.md)，便[可以連線至 RemoteApp](remoteapp-vnet.md)。 查看[VNET 規劃文章](remoteapp-planvnet.md)如需詳細資訊。
- 如果您使用的 VNET，決定您是否要加入至您的本機 Active Directory 網域。

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>步驟 1︰ 建立雲端集合-包含或不含 VNET##


使用下列步驟，以**建立雲端專用集合**︰

1. 在 [管理] 入口網站中，移至 [RemoteApp 頁面。
2. 按一下 [**新增 > 快速建立**。
3. 輸入您的集合的名稱，然後選取您的地區。
4. 選擇您想要使用的標準或基本的方案。
5. 選擇要用於此集合的範本。 

    **提示︰**您的訂閱 RemoteApp 隨附包含 Office 365 或 Office 2013 （用於試用） 程式、 一些發佈 （例如 Word) 和其他人準備好要發佈的[範本圖像](remoteapp-images.md)。 您也可以建立新的[圖像](remoteapp-imageoptions.md)，並在您的雲端集合中使用。


1. 按一下 [**建立 RemoteApp 集合**]。
    
    **重要︰**它可能需要 30 分鐘佈建您集合。

建立您的 Azure RemoteApp 集合之後，按兩下 [集合中的名稱。 [**快速入門**] 頁面便會出現-這是您完成設定集合。

若要建立的**雲端 + VNET 集合**中使用下列步驟︰

1. 在 [管理] 入口網站中，移至 [Azure RemoteApp 頁面。
2. 按一下 [**新** > **VNET 建立**。
3. 輸入您的集合的名稱。
4. 選擇您想要使用的標準或基本的方案。
5. 選擇您已經建立 VNET。 不知道該怎麼做？ 現在，步驟會在[混合式](remoteapp-create-hybrid-deployment.md)主題。
6. 決定您是否要加入您的集合至您的網域。 如果是的您必須使用 AD Connect 整合 Azure AD 與 Active Directory 環境。 討論下方**步驟 2**中。
6. 按一下 [**建立 RemoteApp 集合**]。


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>步驟 2︰ 設定 Active Directory 目錄同步處理 （選用） ##

如果您想要使用 Active Directory，Azure RemoteApp 需要之間 Azure Active Directory 與您內部部署 Active Directory 同步處理的使用者、 連絡人及密碼，以您的 Azure Active Directory 租用戶的目錄同步處理。 規劃資訊，請參閱[Azure RemoteApp 設定作用中的目錄](remoteapp-ad.md)。 您也可以前往直接[AD 連線](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/)的資訊。

## <a name="step-3-publish-apps"></a>步驟 3︰ 發佈應用程式 ##

Azure RemoteApp 應用程式是應用程式或您提供給使用者的程式。 將其位於您上傳集合的範本圖像。 當使用者存取應用程式時，應用程式會出現在其本機的環境中執行，但其實執行中 Azure 虛擬機器中。 

您的使用者可以存取應用程式之前，必須先將它們發行 – 發佈應用程式可讓您的使用者存取應用程式透過遠端桌面用戶端。
 
您可以將多個應用程式發佈至 Azure RemoteApp 集合。 發佈的頁面上，按一下 [**發佈**]，將程式]。 您可以發行從**[開始**] 功能表的範本圖像或應用程式的範本圖像上指定的路徑。 如果您選擇新增 [**開始**] 功能表，選擇 [發佈的應用程式]。 如果您選擇提供應用程式的路徑，提供的名稱應用程式與安裝範本圖像上位置的路徑。

## <a name="step-4-configure-user-access"></a>步驟 4︰ 設定使用者存取權 ##

現在您已經建立您的集合，您需要新增您想要能夠使用遠端資源的使用者。 如果您使用 Active Directory，所提供的存取權需要存在於與訂閱相關聯的 Active Directory 租用戶中的使用者您用來建立此集合。

1.  從 [快速入門] 頁面上，按一下 [**設定使用者存取權限**]。 
2.  輸入您想要授與存取權 （從 Active Directory) 的公司帳戶或 Microsoft 帳戶。

    **附註︰** 

    請確定您使用“user@domain.com”格式。

    如果您使用的 Office 365 專業增強版集合中，您必須使用 Active Directory 身分識別為您的使用者。 這可協助驗證授權。 

3.  使用者進行驗證後，按一下 [**儲存**]。


## <a name="next-steps"></a>後續步驟 ##

這樣就可以-成功建立並部署您 Azure RemoteApp 雲端集合。 下一個步驟是讓您下載並安裝遠端桌面用戶端的使用者。 您可以尋找 Azure RemoteApp 快速入門] 頁面上的用戶端的 URL。 然後有使用者登入用戶端存取您發佈的應用程式。

### <a name="help-us-help-you"></a>協助我們協助您 
您知道，除了評等這份文件] 下方，向下進行註解，您可以進行變更本身的文件嗎？ 遺漏的內容？ 有錯誤？ 我是否撰寫的項目只混亂？ 向上捲動，然後按一下 [若要變更的 [**編輯 GitHub 上**-那些以供檢閱，傳送給我們，然後，我們在登入，就會看到您的變更和這裡改良項目。