<properties
    pageTitle="Azure AD 網域服務︰ 網路指導方針 |Microsoft Azure"
    description="Azure Active Directory 網域服務的網路考量"
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
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD 網域服務的網路考量

## <a name="how-to-select-an-azure-virtual-network"></a>如何選取 Azure 虛擬網路
下列方針可協助您選擇要用於 Azure AD 網域服務的虛擬網路。

### <a name="type-of-azure-virtual-network"></a>Azure 虛擬網路的類型

- 您可以啟用傳統 Azure 虛擬網路中的 Azure AD 網域服務。

- Azure AD 網域服務**無法使用 Azure 資源管理員所建立的虛擬網路中啟用**。

- 您可以 Azure AD 網域服務已啟用傳統虛擬網路連線資源管理員為基礎的虛擬網路。 此後，您可以使用 Azure AD 網域服務中的資源管理員為基礎的虛擬網路。 如需詳細資訊，請參閱 [[網路連線能力](active-directory-ds-networking.md#network-connectivity)] 區段。

- **地區的虛擬網路**︰ 如果您打算使用現有的虛擬網路，確保它地區的虛擬網路。

    - 使用舊版的相關性群組機制的虛擬網路無法使用 Azure AD 網域服務。

    - 若要使用 Azure AD 網域服務[移轉到地區的虛擬網路舊版虛擬網路](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。


### <a name="azure-region-for-the-virtual-network"></a>Azure 虛擬網路區域

- Azure AD 網域服務受管理的網域已部署作為虛擬網路的同一 Azure 地區在您選擇啟用的服務。

- Azure 區域支援 Azure AD 網域服務中，選取虛擬網路。

- 請參閱知道 Azure 區域 Azure AD 網域服務提供 [[依地區 Azure 服務](https://azure.microsoft.com/regions/#services/)] 頁面。


### <a name="requirements-for-the-virtual-network"></a>虛擬網路的需求

- **您 Azure 負載靠近**︰ 選取目前的裝載/主控需要存取 Azure AD 網域服務的虛擬機器的虛擬網路。

- **自訂/顯示-您-擁有您的 DNS 伺服器**︰ 請確定並未的虛擬網路設定的自訂 DNS 伺服器。

- **使用相同的網域名稱的現有網域**︰ 請確定您沒有可用的虛擬網路上相同的網域名稱與現有的網域。 例如，假設您有稱為 'contoso.com' 已經使用所選的虛擬網路上的網域。 之後，您嘗試啟用以相同的網域名稱 （亦即 'contoso.com'） 的虛擬網路上的 Azure AD 網域服務受管理的網域。 嘗試啟用 Azure AD 網域服務時，就會發生錯誤。 此錯誤是因為名稱衝突的虛擬網路上的網域名稱。 在此情況下，您必須使用不同的名稱，設定讓您 Azure AD 網域服務受管理的網域。 或者，您可以取消佈建現有的網域，並前往啟用 Azure AD 網域服務。

> [AZURE.WARNING] 您已啟用服務之後，您無法移動到不同的虛擬網路網域服務。


## <a name="network-security-groups-and-subnet-design"></a>網路安全性群組與子網路設計
[網路安全性群組 (NSG)](../virtual-network/virtual-networks-nsg.md)包含允許或拒絕網路流量至您 VM 中執行個體虛擬網路存取控制清單 (ACL) 規則清單。 NSGs 可與子網路或子網路中的個別 VM 執行個體相關聯。 NSG 與子網路相關聯時，ACL 規則套用至子網路中的所有 VM 執行個體。 此外，可限制個別 vm 流量進一步建立關聯，直接對該 VM NSG。

![建議的子網路設計](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>選擇 [子網路的最佳作法
- Azure 虛擬網路中的**獨立專用的子網路**上部署 Azure AD 網域服務。

- 不用於 NSGs 固定的子網路，您受管理的網域。 如果您必須將 NSGs 套用至固定的子網路，請確定您**不會封鎖服務，並管理您的網域所需的連接埠**。

- 不過度限制可用的受管理的網域的專屬子網路中的 IP 位址的數目。 這項限制防止服務兩個網域控制站可供您受管理的網域。

- 虛擬網路的 [**請不要在閘道器子網路中的 Azure AD 網域服務**。


> [AZURE.WARNING] 當您建立關聯 NSG Azure AD 網域服務的網路啟用時，您可能會干擾服務及管理網域的 Microsoft 的能力。 此外，會中斷您 Azure AD 租用戶與您受管理的網域之間的同步處理。 **SLA 不適用於的部署位置 NSG 已套用該區塊 Azure AD 網域服務更新與管理您的網域。**


### <a name="ports-required-for-azure-ad-domain-services"></a>Azure AD 網域服務所需的連接埠
下列連接埠所需的服務的 Azure AD 網域服務與維護您受管理的網域。 請確定下列連接埠子網路中，您已啟用您受管理的網域不會受到封鎖。

| 連接埠號碼 | 用途 |
|---|---|
| 443 | Azure AD 租用戶與同步處理 |
| 3389 | 管理您網域的 |
| 5986 | 管理您網域的 |
| 636 | 安全的 LDAP (LDAPS) 存取您受管理的網域 |



## <a name="network-connectivity"></a>網路連線
您可以只在單一傳統虛擬網路 Azure 中啟用 Azure AD 網域服務管理的網域。 不支援使用 Azure 資源管理員所建立的虛擬網路。


### <a name="scenarios-for-connecting-azure-networks"></a>連接 Azure 網路的案例
連線 Azure 虛擬的網路使用的受管理的網域，在任一下列部署案例︰

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>使用多個 Azure 傳統虛擬網路中的受管理的網域
您可以在其中您已啟用 Azure AD 網域服務 Azure 傳統虛擬網路連線傳統其他 Azure 虛擬網路。 這個 VPN 連線，可讓您使用其他虛擬網路中部署您工作負載的受管理的網域。

![傳統的虛擬網路連線](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>使用資源管理員為基礎的虛擬網路中的受管理的網域
您可以在其中您已啟用 Azure AD 網域服務 Azure 傳統虛擬網路連線資源管理員為基礎的虛擬網路。 此連線，可讓您使用資源管理員為基礎的虛擬網路中部署您工作負載的受管理的網域。

![資源管理員傳統的虛擬網路連線能力](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>網路連線選項

- **使用網站-VPN 連線的 VNet-VNet 連線**︰ 虛擬網路連線到另一個虛擬網路 (VNet-VNet) 很類似虛擬網路連線至內部部署網站位置。 這兩種連線類型使用 VPN 閘道器提供使用 IPsec/IKE 的安全通道。

    ![使用 VPN 閘道器的虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [詳細資訊-使用 VPN 閘道器的虛擬網路連線](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **使用虛擬網路對等的 VNet-VNet 連線**︰ 虛擬網路對等是透過 Azure 骨幹網路連線相同的區域中的兩個虛擬網路的機制。 一旦 peered，兩個虛擬網路會有所有連線進行。 仍可管理為不同的資源，但這些虛擬網路中的虛擬機器可以彼此直接使用私用的 IP 位址。

    ![使用 [對等的虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [對等的詳細資訊的虛擬網路](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>相關的內容

- [Azure 虛擬網路對等](../virtual-network/virtual-network-peering-overview.md)

- [傳統的部署模型 VNet-VNet 連線的設定](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Azure 網路安全性群組](../virtual-network/virtual-networks-nsg.md)
