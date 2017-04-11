<properties
   pageTitle="ExpressRoute 客戶路由器設定範例 |Microsoft Azure"
   description="本頁面提供 Cisco 和 Juniper 路由器路由器設定範例。"
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

# <a name="router-configuration-samples-to-setup-and-manage-nat"></a>若要設定及管理 NAT 路由器設定範例

本頁面提供 Cisco ASA 和 Juniper SRX 數列路由器 NAT 設定範例。 這些是為範例，如僅限的指引，，必須已經無法使用。 您可以使用您得到適當的設定，為您的網路的廠商。 

>[AZURE.IMPORTANT] 在此頁面中的範例被要完全的指引。 您必須使用您的廠商銷售 / 技術小組和網路小組得到適當的設定，以符合您的需求。 Microsoft 將不支援此頁面中所列的設定的相關問題。 您必須連絡您的裝置廠商支援問題。

Azure 公用與 Microsoft peerings 適用於下列路由器設定範例。 您必須設定 NAT 的 Azure 私人對等。 如需詳細資訊，檢閱[ExpressRoute peerings](expressroute-circuit-peerings.md)與[ExpressRoute NAT 需求](expressroute-nat.md)。

**附註︰**您必須連線至網際網路和 ExpressRoute 使用不同的 NAT IP 集區。 使用相同的 NAT IP 集區網際網路和 ExpressRoute 會產生非對稱式路由，失去連線。

## <a name="cisco-asa-firewalls"></a>Cisco ASA 防火牆

### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Microsoft 客戶網路流量潘設定

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>從 Microsoft 客戶網路的流量潘設定

#### <a name="interfaces-and-direction"></a>介面和方向︰
    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

#### <a name="configuration"></a>設定︰
NAT 區︰

    object network outbound-PAT
        host <NAT-IP>

目標伺服器︰

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

客戶 IP 位址的物件群組

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>
    
    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT 命令︰

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX 數列路由器 

### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1.建立叢集多餘乙太網路介面

    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2.建立兩個安全性區域

 - 信任的內部網路和信任區域的區域，對邊緣路由器的外部網路
 - 將適當的介面指派給區域
 - 允許服務的介面


    安全性 {區域 {安全性區域信任 {主機連入-流量 {系統服務 {偵測 （ping）;                 } {bgp; 的通訊協定                 }} 介面 {reth0.100;             }} 安全性區域信任 {主機連入-流量 {系統服務 {偵測 （ping）;                 } {bgp; 的通訊協定                 }} 介面 {reth1.100;             }          }      }  }


### <a name="3-create-security-policies-between-zones"></a>3.建立區域之間的安全性原則
 
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4.設定 NAT 原則
 - 建立兩個 NAT 集區。 其中一個將會用來 NAT 流量輸出至 Microsoft 及其他 Microsoft 的客戶。
 - 建立規則以 NAT 分別流量

        security {
            nat {
                source {
                    pool SNAT-To-ExpressRoute {
                        routing-instance {
                            External-ExpressRoute;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    pool SNAT-From-ExpressRoute {
                        routing-instance {
                            Internal;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    rule-set Outbound_NAT {
                        from routing-instance Internal;
                        to routing-instance External-ExpressRoute;
                        rule SNAT-Out {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-To-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                    rule-set Inbound-NAT {
                        from routing-instance External-ExpressRoute;
                        to routing-instance Internal;
                        rule SNAT-In {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-From-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }


### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5。 設定通知中每個方向的選擇性首碼 BGP

請參閱在[路由設定範例](expressroute-config-samples-routing.md)] 頁面上的範例。

### <a name="6-create-policies"></a>6。 建立原則

    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>後續步驟

請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)，如需詳細資訊。
