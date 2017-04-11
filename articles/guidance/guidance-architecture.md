
<properties
   pageTitle="Azure 指南 |模式和實務 |Microsoft Azure"
   description="Azure 參考架構"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Azure 參考架構

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_此內容是在作用中的開發。很有用，因此我們會讓預覽。我們值您的意見反應。_

案例中，來排列我們參考架構與群組在一起的多個相關架構。
每個個別架構提供建議的作法和給定的步驟，以及意建議的可執行元件。
有許多架構的漸進;建立有較少的需求的上一個架構的上方。

## <a name="designing-your-infrastructure-for-resiliency"></a>設計恢復基礎結構

本系列始於建議作法最佳 VM 設定，且達成 「 容錯移轉的多區域部署。

- [Azure 上執行的 Windows VM](guidance-compute-single-vm.md)
- [Azure 上執行 Linux VM](guidance-compute-single-vm-linux.md)
- [執行多個 Vm 延展性及可用性](guidance-compute-multi-vm.md)
- [執行 Windows Vm N 層架構](guidance-compute-n-tier-vm.md)
- [執行 Linux Vm N 層架構](guidance-compute-n-tier-vm-linux.md)
- [執行 Windows Vm 在多個區域內的可用性](guidance-compute-multiple-datacenters.md)
- [執行 Linux Vm 在多個區域內的可用性](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>您的內部部署網路連線到 Azure

本系列會啟動展示您現有的網路連線到 Azure 的方式。 然後將其展開以涵蓋可用性和安全性需求。

- [執行混合式網路架構與 Azure 及內部部署 VPN](guidance-hybrid-network-vpn.md)
- [執行混合式網路架構與 Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
- [實作高度可用的混合式網路架構](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>保護您的混合式網路

本系列涵蓋在 Azure 中建立 DMZ，來自內部部署資料中心及網際網路安全連線至經過驗證的作法。

- [實作 DMZ 之間 Azure 及內部部署資料中心](guidance-iaas-ra-secure-vnet-hybrid.md)
- [實作 Azure 與網際網路之間 DMZ](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>提供的身分識別服務

本系列會啟動示範如何使用提供 Azure 中的使用者驗證的 Azure Active Directory。 然後將其展開以涵蓋延伸至 Azure 相加基礎結構，並使用 ADFS 委派複雜的案例。

- [實作 Azure Active Directory](./guidance-identity-aad.md)
- [延伸至 Azure 的 Active Directory 目錄服務 （新增）](./guidance-identity-adds-extend-domain.md)
- [建立 Azure Active Directory 目錄服務 （新增） 資源樹系](./guidance-identity-adds-resource-forest.md)
- [實作 Azure Active Directory Federation Services (ADFS)](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>架構使用 Azure PaaS 擴充 web 應用程式

本系列涵蓋建構可調整和高度可用的 web 應用程式的建議。 

- [基本的 web 應用程式](guidance-web-apps-basic.md)
- [改善擴充 web 應用程式](guidance-web-apps-scalability.md)
- [Web 應用程式的可用性](guidance-web-apps-multi-region.md)
