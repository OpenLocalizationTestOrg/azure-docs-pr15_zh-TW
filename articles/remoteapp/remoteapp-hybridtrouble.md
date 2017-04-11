
<properties
    pageTitle="疑難排解建立 RemoteApp 混合式集合 |Microsoft Azure"
    description="瞭解如何疑難排解 RemoteApp 混合式集合建立失敗"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>疑難排解建立 Azure RemoteApp 混合式集合

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

混合式集合裝載於，並將資料儲存 Azure 雲端，但也可讓使用者存取資料和儲存在您的區域網路上的資源。 使用者可以存取應用程式登入同步處理，或與 Azure Active Directory 同盟其公司認證。 您可以部署混合式集合使用現有的 Azure 虛擬網路，或您可以建立新的虛擬網路。 我們建議您建立或子虛擬網路使用的預期未來的成長的 Azure RemoteApp CIDR 範圍夠大。

還沒有建立您的集合，還嗎？ 如需的步驟，請參閱[建立混合式集合](remoteapp-create-hybrid-deployment.md)。

如果您無法順利建立您的集合，或集合中無法運作方式如果您認為應該請查看下列資訊。

## <a name="your-image-is-invalid"></a>無效的圖像 ##
如果您正在等候 Azure 佈建您集合時，您會看到像 「 GoldImageInvalid 」 的訊息，，表示您的範本圖像不符合[定義圖像需求](remoteapp-imagereqs.md)。 因此，請移閱讀這些[需求](remoteapp-imagereqs.md)、 修正圖像，以及嘗試建立您的集合，一次。



## <a name="does-your-vnet-have-network-security-groups-defined"></a>您 VNET 有定義的網路安全性群組嗎？ ##
如果您有網路安全性群組子網路上的定義您使用您的集合，請確定這些[Url 和連接埠](remoteapp-ports.md)是從您子網路中存取。

您可以將其他網路安全性群組新增至您部署更緊密地控制子網路中 Vm 中。

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>使用您自己的 DNS 伺服器？ 其可從您 VNET 子網路嗎？ ##
>[AZURE.NOTE] 您必須確定您 VNET 的 DNS 伺服器一律是設定，而且永遠能夠解決裝載於 VNET 虛擬機器。 此，不要使用 Google DNS。


混合式集合，您會使用您自己的 DNS 伺服器。 您指定其在您的網路設定結構描述中，或透過管理入口網站建立虛擬網路時。 他們會指定容錯移轉的方式 （而不是循環） 的順序會使用 DNS 伺服器。  
請參閱[Vm 和角色執行個體名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，請確定您的 DNS 伺服器設定的 correcly。

請確定可存取您集合的 DNS 伺服器而提供 VNET 子網路所指定此集合。

例如︰

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![定義您的 DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>您目前使用的 Active Directory 網域控制站集合中？ ##
目前只有一個 Active Directory 網域可與 Azure RemoteApp 相關聯。 混合式集合支援有使用 Windows Server Active Directory 部署; DirSync 工具已同步處理的 Azure Active Directory 帳戶具體來說，或是使用密碼同步處理] 選項已同步處理設定的 Active Directory Federation Services (AD FS) 同盟與同步處理。 您需要建立的自訂網域符合網域的 UPN 尾碼您內部部署的網域及設定目錄整合。

如需詳細資訊，請參閱[Azure RemoteApp 設定作用中的目錄](remoteapp-ad.md)。

請確定所提供的網域詳細資料是有效的網域控制站是可從子網路用於 Azure Remote 應用程式中建立的 VM。 也請確定提供的服務帳戶認證有權限的電腦加入提供的網域，可以從 VNET 中提供的 DNS 解析所提供的 AD 名稱。

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>建立您的集合時，您有指定哪些網域名稱並未？ ##

您建立或新增的網域名稱必須是內部網域名稱 （不您 Azure AD 網域名稱），而且必須解析 DNS 格式 (contoso.local)。 例如，您有 Active Directory 內部名稱 (contoso.local)] 和 [Active Directory UPN (contoso.com)-必須使用內部的名稱，當您建立您的集合。
