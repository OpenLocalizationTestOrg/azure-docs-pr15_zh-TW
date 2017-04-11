<properties
   pageTitle="保護 Azure 資訊安全中心中的 SQL Azure 服務 |Microsoft Azure"
   description="此文件地址，建議在 Azure 資訊安全中心可協助您保護 SQL Azure 服務，並保持留存安全性原則。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>保護 Azure 資訊安全中心中的 SQL Azure 服務

Azure 資訊安全中心分析 Azure 資源的安全性狀態。 當資訊安全中心] 會識別潛在安全性漏洞時，它會建立引導您進行必要的控制項的程序的建議。  建議適用於 Azure 的資源類型︰ 虛擬機器 (Vm)，網路、 SQL 和應用程式。

本文適用於 SQL Azure 服務的建議。  Azure SQL 建議服務中心周圍啟用稽核 Azure SQL server 和資料庫並啟用 SQL 資料庫加密。  使用下的表的參考，可協助您瞭解可用的 SQL 服務建議，以及每個當您將它套用會進行的工作。

## <a name="available-sql-service-recommendations"></a>使用 SQL 服務建議

|建議|描述|
|-----|-----|
|[啟用稽核 SQL server](security-center-enable-auditing-on-sql-servers.md)|建議您開啟稽核 Azure SQL server （SQL Azure 服務; 不包含 SQL 虛擬機器上執行）。|
|[啟用稽核 SQL 資料庫](security-center-enable-auditing-on-sql-databases.md)|建議您開啟稽核 Azure SQL 資料庫 （SQL Azure 服務; 不包含 SQL 虛擬機器上執行）。|
|[啟用 [SQL 資料庫上的 [透明資料加密](security-center-enable-transparent-data-encryption.md)|建議您啟用加密 SQL 資料庫 （SQL Azure 服務）。|

## <a name="see-also"></a>另請參閱

若要進一步瞭解套用至其他 Azure 的資源類型的建議，請參閱下列各項︰

- [保護您的虛擬機器中 Azure 資訊安全中心](security-center-virtual-machine-recommendations.md)
- [保護您的應用程式在 Azure 資訊安全中心](security-center-application-recommendations.md)
- [保護您的網路中 Azure 資訊安全中心](security-center-network-recommendations.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
