<properties
    pageTitle="Azure AD 網域服務︰ 建立或選取虛擬網路 |Microsoft Azure"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>建立或選取虛擬網路 Azure AD 網域服務

## <a name="guidelines-to-select-an-azure-virtual-network"></a>若要選取 Azure 虛擬網路的指導方針
> [AZURE.NOTE] **之前**︰ 參照[網路 Azure AD 網域服務的考量](active-directory-ds-networking.md)。


## <a name="task-2-create-an-azure-virtual-network"></a>任務 2︰ 建立 Azure 虛擬網路
下一個設定工作是建立 Azure 虛擬網路和子網路內。 您可以啟用此子網路中的 Azure AD 網域服務虛擬網路中。 如果您已經有您想要使用現有的虛擬網路，您可以略過此步驟。

> [AZURE.NOTE] 請確定您建立或選擇 Azure AD 網域服務中使用 Azure 虛擬網路屬於 Azure AD 網域服務已受 Azure 區域。 請參閱知道 Azure 區域 Azure AD 網域服務提供 [[依地區 Azure 服務](https://azure.microsoft.com/regions/#services/)] 頁面。

請注意下虛擬網路的名稱，讓您選取右虛擬網路時啟用在後續的設定步驟的 Azure AD 網域服務。

執行下列設定步驟，以建立您想要啟用 Azure AD 網域服務中 Azure 虛擬網路。

1. 瀏覽至**Azure 傳統入口網站**([https://manage.windowsazure.com](https://manage.windowsazure.com))。

2. 選取在左窗格的 [**網路**] 節點。

    ![網路節點](./media/active-directory-domain-services-getting-started/networks-node.png)

3. 按一下 [] 工作窗格底部的頁面上的 [**新增**]。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. 在**網路服務**的節點中，選取 [**虛擬網路**]。

5. 按一下 [**快速建立**建立虛擬網路。

    ![虛擬網路-快速建立](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. 指定虛擬網路的**名稱**。 您也可以選擇為此網路設定的**位址空間**或**最大 VM 計數**。 現在，您可以將 [ **DNS 伺服器**] 設定設為 [無]。 您可以更新之後您啟用 Azure AD 網域設定服務的 DNS 伺服器。

7. 請確定您在 [**位置**] 下拉式清單中選取的支援 Azure 區域。 請參閱知道 Azure 區域 Azure AD 網域服務提供 [[依地區 Azure 服務](https://azure.microsoft.com/regions/#services/)] 頁面。

8. 若要建立虛擬網路，請按一下 [**建立虛擬網路**] 按鈕。

    ![Azure AD 網域服務中建立虛擬網路。](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. 建立虛擬網路之後，請選取虛擬網路，然後按一下 [**設定**] 索引標籤。

    ![建立子網路](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. 瀏覽至 [**虛擬網路位址空間**] 區段。 按一下 [**新增子網路**，指定子網路名稱**AaddsSubnet**。 按一下 [建立子網路的 [**儲存**]。

    ![建立子網路 Azure AD 網域服務。](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>任務 3-啟用 Azure AD 網域服務
若要[啟用 Azure AD 網域服務](active-directory-ds-getting-started-enableaadds.md)是下一個設定工作。
