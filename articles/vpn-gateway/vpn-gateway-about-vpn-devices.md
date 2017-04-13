<properties 
   pageTitle="Azure 虛擬網路網站-VPN 閘道連線的 VPN 和裝置有關 |Microsoft Azure"
   description="本文討論 VPN 裝置和 IPsec 參數 S2S VPN 閘道器的連線，並包含連結的設定指示及範例。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>連線至網站 VPN 閘道器的 VPN 和裝置有關

VPN 裝置，才能設定網站的網站 」 (S2S) VPN 連線。 網站的連線可以用來建立混合式解決方案，或隨時安全連線內部網路和虛擬網路之間。 本文將說明相容 VPN 裝置和設定參數。 

>[AZURE.NOTE] 設定網站-連線-在公開 IPv4 IP 位址時，必須要有 vpn 才能裝置。                                                                                                                                                                               

如果您的裝置沒有出現[驗證 VPN 裝置](#devicetable)資料表中，請參閱本文的[非驗證 VPN 裝置](#additionaldevices)一節。 則 Azure 可能仍在您的裝置。 如需 VPN 裝置支援，請連絡您裝置的製造商。

**若要檢視資料表時，請注意的項目︰**

- 已為靜態和動態路由術語變更。 您可能會遇到這兩個字詞。 沒有任何功能的變更，只包含名稱會變更。
    - 靜態路由 = PolicyBased
    - 動態路由 = RouteBased
- 除非另行註明，高效能 VPN 閘道和 RouteBased VPN 閘道器的規格都相同。 例如，以致的 VPN 裝置與 RouteBased VPN 閘道相容的也會相容於 Azure 高效能 VPN 閘道器。 


## <a name="devicetable"></a>已驗證的 VPN 裝置 

我們確認標準 VPN 裝置合作關係中的一組與裝置廠商。 下列清單中所包含的裝置系列中的所有裝置應該都使用 Azure VPN 閘道器。 請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)以驗證您需要建立您想要設定的方案的閘道器的類型。 

若要協助設定 VPN 裝置，請參閱會對應到適當的裝置系列中的連結。 如需 VPN 裝置支援，請連絡您裝置的製造商。



| **供應商**                      | **裝置家族**                                        | **最小的作業系統版本**                             | **PolicyBased**                                                                                                                                                                                                             | **RouteBased**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 同盟的 Telesis                  | AR 數列 VPN 路由器                                    | 2.9.2                                              | 即將推出                                                                                                                                                                                                                                          | 不相容                                                                                                                                                                                               |
| Barracuda 網路國廣兌        | Barracuda NextGen 防火牆 F 數列             | PolicyBased: 5.4.3，RouteBased: 6.2.0  | [設定指示](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [設定指示](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda 網路國廣兌        |  Barracuda NextGen 防火牆 X 數列                 | Barracuda 防火牆 6.5 | [Barracuda 防火牆](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | 不相容                                                                                                                                                                                               |
| 錦緞                         | Vyatta 5400 vRouter                                      | 虛擬路由器 6.6R3 GA                            | [設定指示](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | 不相容                                                                                                                                                                                               |
| 檢查點                     | 安全性閘道器                                         | R75.40 R75.40VS                                     | [設定指示](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [設定指示](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Cisco 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | 不相容                                                                                                                                                                                               |
| Cisco                           | ASR                                                      | IOS 15.1 (PolicyBased)，IOS 15.2 (RouteBased)                | [Cisco 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Cisco 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (PolicyBased)，IOS 15.1 （RouteBased *）               | [Cisco 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Cisco 範例 *](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | NetScaler MPX，SDX，VPX      |10.1 及以上                                           | [整合指示](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | 不相容                                                                                                                                                                                               |
| Dell SonicWALL                  | TZ 系列，NSA 系列，SuperMassive 系列，E 課程 NSA 數列 | SonicOS 5.8.x， [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)， [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [指示-SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)[指示-SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [指示-SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)[指示-SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | 大 IP 數列                                 |           12.0                                            | [設定指示](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [設定指示](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [設定指示](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [設定指示](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| 網際網路計劃日本 (IIJ) | SEIL 數列                                              | SEIL / 4.60 SEIL/B1 4.60 SEIL x86 3.20 X            | [設定指示](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | 不相容                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (PolicyBased)，JunOS 11.4 (RouteBased)            | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | J 數列                                                 | JunOS 10.4r9 (PolicyBased)，JunOS 11.4 (RouteBased)          | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 （PolicyBased 和 RouteBased）                  | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 （PolicyBased 和 RouteBased）                  | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Juniper 範例](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | 路由及遠端存取服務                        | Windows Server 2012                                | 不相容                                                                                                                                                                                                                                       | [Microsoft 範例](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| 開啟系統 AG | 任務控制項安全性閘道器 | N/A | [安裝指南](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [安裝指南](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | （即將推出）                                                                                                                                                                                                                                        | 不相容                                                                                                                                                                                               |
| Palo 琴奧圖網路              | 執行全景 OS 的所有裝置     | 全景 OS 6.1.5 或更新版本 (PolicyBased) 全景 OS 7.0.5 或更新版本 (RouteBased)       | [設定指示]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [設定指示](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| Watchguard                      | 所有                                                      | Fireware XTM v11.x                                 | [設定指示](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | 不相容                                                                                                                                                                                               |

(*)ISR 7200 數列路由器只支援 PolicyBased Vpn。

## <a name="additionaldevices"></a>非驗證 VPN 裝置

如果您沒有看到驗證 VPN 裝置資料表中列出您的裝置，它仍可能會使用網站的連線。 確認您要有 vpn 才能裝置符合[VPN 閘道器的相關](vpn-gateway-about-vpngateways.md#gateway-requirements)文章的閘道器需求一節所述的最低需求。 會議的最低需求的裝置應該也使用也要有 vpn 才能閘道器。 取得其他支援與設定指示，請連絡您的裝置製造商。


## <a name="editing-device-configuration-samples"></a>編輯裝置設定範例

下載提供的 VPN 裝置設定範例之後，必須取代部分的值，以反映您的環境的設定。 

**若要編輯的範例︰**

1. 開啟使用記事本的範例。 
1. 搜尋及取代所有 <*文字*> 字串與適用於您的環境的值。 請務必包含 < 和 >。 指定名稱時，所選的名稱必須是唯一的。 如果命令無法運作，請諮詢您裝置的製造商說明文件。

| **範例文字**                | **若要變更**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | 此物件您所選的名稱。 範例︰ myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | 此物件您所選的名稱。 範例︰ myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | 此物件您所選的名稱。 範例︰ myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | 此物件您所選的名稱。 範例︰ myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | 此物件您所選的名稱。 範例︰ myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | 指定範圍。 範例︰ 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | 指定子網路遮罩。 範例︰ 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | 指定內部部署的範圍。 範例︰ 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | 指定內部部署的子網路遮罩。 範例︰ 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | 這項資訊至虛擬網路特定且位於管理入口網站**閘道器 IP**位址。 |
| &lt;SP_PresharedKey&gt;                | 這項資訊是特定虛擬網路，並且位於 [管理] 入口網站管理鍵。          |



## <a name="ipsec-parameters"></a>IPsec 參數

>[AZURE.NOTE] 雖然 Azure VPN 閘道的支援下表所列的值，目前沒有方法讓您可以指定或選取 [從 Azure VPN 閘道器的 [特定的組合。 您必須指定的內部部署 VPN 裝置從任何限制式。 此外，您也必須在 1350年鉗 MSS。

### <a name="ike-phase-1-setup"></a>IKE 階段 1 設定

| **屬性**                                       | **PolicyBased** | **RouteBased 和標準或高效能 VPN 閘道器** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE 版本                                        | IKEv1                          | IKEv2                                                            |
| 時間時] 群組                               | 群組 2 （1024 位元）             | 群組 2 （1024 位元）                                               |
| 驗證方法                              | 預先共用索引鍵                 | 預先共用索引鍵                                                   |
| 加密演算法                              | AES256 AES128 3DES             | AES256 3DES                                                      |
| 雜湊演算法                                  | SHA1(SHA128)                   | SHA1(SHA128) SHA2(SHA256)                                                     |
| 階段 1 安全性關聯性 (SA) 存留時間 （時間） | 28800 秒                 | 10,800 （秒)                                                   |


### <a name="ike-phase-2-setup"></a>IKE 階段 2 設定

| **屬性**                                                             | **PolicyBased**                 | **RouteBased 和標準或高效能 VPN 閘道器**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE 版本                                                              | IKEv1                                          | IKEv2                                                              |
| 雜湊演算法                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| 階段 2 安全性關聯性 (SA) 存留時間 （時間）                        | 3600 秒                                  | 3600 秒                                                                  |
| 階段 2 安全性關聯 (SA) 為單位的週期 （處理量）                  | 102,400,000 KB                                 | -                                                                  |
| IPsec 索加密與驗證提供 （順序喜好設定） | 1.ESP-AES256 2。 ESP AES128 3。 ESP 3DES 4。 N/A | 請參閱*RouteBased 閘道器 IPsec 安全性關聯 (SA) 提供*（下方） |
| 完美轉寄密碼]                                            | 無                                             | 沒有 （*）|
| 死等偵測                                                      | 不支援                                  | 支援                                                          |

(*)Azure 閘道 IKE 回應可以接受 PFS DH 群組 1、 2、 5、 14、 24。

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>提供 RouteBased 閘道器 IPsec 安全性關聯 (SA)

下表列出 IPsec 索加密，並提供驗證。 優惠所列的呈現或是接受優惠喜好設定的順序。

| **IPsec SA 加密和驗證優惠** | **Azure 閘道啟動器**                               | **為回應 azure 閘道器**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | 使用與 null HMAC ESP AES_128 AH SHA1                      |
| 5                                                 | 使用與 null HMAC ESP AES_256 AH SHA1                      | 使用 ESP 3_DES 與 null HMAC 的 AH SHA1                        |
| 6                                                 | 使用與 null HMAC ESP AES_128 AH SHA1                      | 使用 ESP 3_DES null HMAC，不建議的存留時間使用的 AH MD5 |
| 7                                                 | 使用 ESP 3_DES 與 null HMAC 的 AH SHA1                        | ESP 3_DES SHA1，沒有生命週期與 AH SHA1                    |
| 8                                                 | 使用 ESP 3_DES null HMAC，不建議的存留時間使用的 AH MD5 | ESP 3_DES MD5，沒有生命週期與 AH MD5                     |
| 9                                                 | ESP 3_DES SHA1，沒有生命週期與 AH SHA1                    | ESP DES MD5                                                  |
| 10                                                | ESP 3_DES MD5，沒有生命週期與 AH MD5                     | ESP DES SHA1，沒有存留時間                                   |
| 11                                                | ESP DES MD5                                                  | 喔 SHA1 與 ESP DES null HMAC，沒有存留時間建議        |
| 12                                                | ESP DES SHA1，沒有存留時間                                   | 喔 MD5 與 ESP DES null HMAC，沒有存留時間建議        |
| 13                                                | 喔 SHA1 與 ESP DES null HMAC，沒有存留時間建議        | ESP DES SHA1，沒有生命週期與 AH SHA1                      |
| 14                                                | 喔 MD5 與 ESP DES null HMAC，沒有存留時間建議        | ESP DES MD5，沒有生命週期與 AH MD5                       |
| 15                                                | ESP DES SHA1，沒有生命週期與 AH SHA1                      | ESP SHA，沒有存留時間                                        |
| 16                                                | ESP DES MD5，沒有生命週期與 AH MD5                       | ESP MD5，沒有存留時間                                        |
| 17                                                | -                                                            | 喔 SHA 沒有存留時間                                         |
| 18                                                | -                                                            | AH MD5，沒有存留時間                                        |


- 您可以指定 IPsec ESP NULL 加密與 RouteBased 與高效能 VPN 閘道器。 Null 根據的加密不提供保護資料在傳輸時會，並只時應使用最大輸出量和最小值的延遲是必要。  用戶端可以選擇使用這個在 VNet-VNet 通訊的情況下，或是加密套用其他地方方案中。

- 跨內部部署連線到網際網路，使用預設 Azure VPN 閘道器設定加密和雜湊演算法列上方資料表中，以確保您要徑通訊的安全性。





