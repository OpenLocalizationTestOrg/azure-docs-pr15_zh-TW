<properties
   pageTitle="ExpressRoute 客戶路由器設定範例 |Microsoft Azure"
   description="本頁面提供 Cisco 和 Juniper 路由器路由器 config 範例。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>若要設定及管理路由路由器設定範例

本頁面提供 Cisco IOS XE 和 Juniper MX 數列路由器介面和路由設定範例。 這些是為範例，如僅限的指引，，必須已經無法使用。 您可以使用您得到適當的設定，為您的網路的廠商。 

>[AZURE.IMPORTANT] 在此頁面中的範例被要完全的指引。 您必須使用您的廠商銷售 / 技術小組和網路小組得到適當的設定，以符合您的需求。 Microsoft 將不支援此頁面中所列的設定的相關問題。 您必須連絡您的裝置廠商支援問題。

路由器設定範例下套用至所有 peerings。 如需詳細資訊路由檢閱[ExpressRoute peerings](expressroute-circuit-peerings.md)和[ExpressRoute 路由需求](expressroute-routing.md)。

## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS XE 基礎路由器

本節中的範例適用於任何路由器執行 IOS XE OS 系列。

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1.設定介面和子介面

您需要每次您連線至 Microsoft 每個路由器在對等的子介面。 Sub 介面可以辨識的虛擬區域網路 ID 或堆疊的對虛擬區域網路識別碼和 IP 位址。

#### <a name="dot1q-interface-definition"></a>Dot1Q 介面定義

這個範例提供子介面定義子介面單一的虛擬區域網路識別碼。 虛擬區域網路 ID 是唯一的每個對等。 您 IPv4 位址的最後一個八位元一律為奇數頁的數字。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>QinQ 介面定義

這個範例中提供的兩個虛擬區域網路的子介面的子介面定義。 外部虛擬區域網路識別碼 （s 標籤），如果使用保持不變過所有 peerings。 內部虛擬區域網路識別碼 （c 標記） 都是唯一的每個對等。 您 IPv4 位址的最後一個八位元一律為奇數頁的數字。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2.設定 eBGP 工作階段

您必須設定與 Microsoft 的每個對等的 BGP 工作階段。 下列範例可讓您設定與 Microsoft BGP 工作階段。 如果您使用的您子介面 IPv4 位址 a.b.c.d，BGP 相鄰 (Microsoft) 的 IP 位址會 a.b.c.d+1。 BGP 相鄰的 IPv4 位址的最後一個八位元一律為偶數。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3.設定前置詞會通知透過 BGP 工作階段

您可以設定通知給 Microsoft 選取前置詞路由器。 您可以使用下列範例。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4.路線的地圖

您可以使用路由地圖和前置字元清單篩選首碼傳播到您的網路。 您可以使用下列範例，以完成的工作。 請確定您有適當的前置字元清單設定。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX 數列路由器 

本節中的範例適用於任何 Juniper MX 數列路由器。

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1.設定介面和子介面

#### <a name="dot1q-interface-definition"></a>Dot1Q 介面定義

這個範例提供子介面定義子介面單一的虛擬區域網路識別碼。 虛擬區域網路 ID 是唯一的每個對等。 您 IPv4 位址的最後一個八位元一律為奇數頁的數字。

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>QinQ 介面定義

這個範例中提供的兩個虛擬區域網路的子介面的子介面定義。 外部虛擬區域網路識別碼 （s 標籤），如果使用保持不變過所有 peerings。 內部虛擬區域網路識別碼 （c 標記） 都是唯一的每個對等。 您 IPv4 位址的最後一個八位元一律為奇數頁的數字。

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2.設定 eBGP 工作階段

您必須設定與 Microsoft 的每個對等的 BGP 工作階段。 下列範例可讓您設定與 Microsoft BGP 工作階段。 如果您使用的您子介面 IPv4 位址 a.b.c.d，BGP 相鄰 (Microsoft) 的 IP 位址會 a.b.c.d+1。 BGP 相鄰的 IPv4 位址的最後一個八位元一律為偶數。

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3.設定前置詞會通知透過 BGP 工作階段

您可以設定通知給 Microsoft 選取前置詞路由器。 您可以使用下列範例。

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4.路線的地圖

您可以使用路由地圖和前置字元清單篩選首碼傳播到您的網路。 您可以使用下列範例，以完成的工作。 請確定您有適當的前置字元清單設定。

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>後續步驟

請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)，如需詳細資訊。
