<properties
    pageTitle="Azure 虛擬網路的網域服務 azure AD︰ 更新 DNS 設定 |Microsoft Azure"
    description="快速入門 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Azure AD 網域服務-Azure 虛擬網路更新 DNS 設定

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>任務 4︰ 更新 DNS 設定的 Azure 虛擬網路
在前面設定工作，您已成功啟用 Azure AD 網域服務為您的目錄。 下一步是以確保在虛擬網路的電腦可以連線，並使用這些服務。 更新為指向用以 Azure AD 網域服務會提供虛擬網路的兩個 IP 位址虛擬網路的 DNS 伺服器設定。

> [AZURE.NOTE] 您已啟用為目錄的 Azure AD 網域服務後，在您的目錄中的 [**設定**] 索引標籤上顯示的 Azure AD 網域服務，請注意下 IP 位址。

執行下列設定步驟來更新虛擬網路有啟用 Azure AD 網域服務的 DNS 伺服器設定。

1. 瀏覽至**Azure 傳統入口網站**([https://manage.windowsazure.com](https://manage.windowsazure.com))。

2. 選取在左窗格的 [**網路**] 節點。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. 在**虛擬網路**] 索引標籤中，選取您已啟用檢視其內容的 Azure AD 網域服務虛擬網路。

4. 按一下 [**設定**] 索引標籤。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. 在 [ **dns** ] 區段中，輸入 Azure AD 網域服務的 IP 位址。

6. 請確定您輸入的兩個目錄的 [**設定**] 索引標籤上的 [**網域服務**] 區段中所顯示的 IP 位址。

7. 若要儲存此虛擬網路的 DNS 伺服器設定，按一下 [**儲存**] 工作窗格底部的頁面上。

   ![更新虛擬網路的 DNS 伺服器的設定。](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] 在更新之後的虛擬網路的 DNS 伺服器設定，可能需要一段時間的虛擬機器上的網路以取得更新的 DNS 設定。 如果虛擬機器無法連線至該網域，您可以清除 DNS 快取，（例如。 「 ipconfig /flushdns，即可）' 虛擬機器上。 這個命令強制重新整理的虛擬機器上的 [DNS 設定。


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>任務 5-啟用密碼同步處理至 Azure AD 網域服務
下一個設定工作是[啟用 Azure AD 網域服務的密碼同步處理](active-directory-ds-getting-started-password-sync.md)。
