<properties 
   pageTitle="如何使用虛擬網路中的公用 IP 位址"
   description="瞭解如何設定虛擬網路使用的公用 IP 位址"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>虛擬網路 (VNet) 中的公用 IP 位址空間

虛擬網路 (VNets) 可以包含公開及私密金鑰 （RFC 1918 地址區塊） IP 位址空間。 當您新增的公用 IP 位址範圍時，它將會視為私人 VNet IP 位址空間，才可 VNet，互連式 VNets，且從您內部部署的位置的一部分。

下圖顯示包含公開及私密金鑰 IP 通訊錄空格 VNet。

![概念性的公用 IP](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>我要如何新增公用的 IP 位址範圍？

您可以新增私用的 IP 位址範圍; 以相同的方式新增公用的 IP 位址範圍使用*netcfg*檔案]，或是新增[Azure 入口網站](http://portal.azure.com)中的設定。 當您建立您的 VNet，或您可以返回並將其新增之後，您可以新增公用的 IP 位址範圍。 下列範例顯示在同一個 VNet 中設定的公開及私密金鑰 IP 位址空間。

![在入口網站中的公用 IP 位址](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>是否有任何限制？

有一些不允許的 IP 位址範圍︰

- 224.0.0.0/4 （多點傳送）

- 255.255.255.255/32 （廣播）

- 127.0.0.0/8 （迴路）

- 169.254.0.0/16 （本機連結）

- 168.63.129.16/32 (內部 DNS)

## <a name="next-steps"></a>後續步驟

[如何管理 VNet 所使用的 DNS 伺服器](virtual-networks-manage-dns-in-vnet.md)