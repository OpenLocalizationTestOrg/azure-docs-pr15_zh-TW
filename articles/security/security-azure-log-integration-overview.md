<properties
   pageTitle="Microsoft Azure 記錄整合簡介 |Microsoft Azure"
   description="深入了解 Azure 記錄整合、 其主要的功能及其運作方式。"
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Microsoft Azure 記錄整合 （預覽版本） 簡介

深入了解 Azure 記錄整合、 其主要的功能及其運作方式。

## <a name="overview"></a>概觀

為服務 (PaaS) 和裝載於 Azure 服務 (IaaS) 基礎結構的平台安全性記錄檔中產生大量的資料。 這些記錄包含可提供智慧及原則衝突、 內部和外部威脅、 法規遵循問題和網路、 主機和使用者活動中的異常功能強大的深入見解的重要資訊。

Azure 記錄整合可讓您從 Azure 資源的原始記錄整合內部部署安全性資訊及事件管理 (SIEM) 系統。 Azure 記錄整合會從您的 Windows *(WAD)*收集 Azure 診斷虛擬機器，以及診斷從合作夥伴解決方案例如 Web 應用程式防火牆 (WAF)。 此整合提供整合儀表板的所有的資產，內部部署或雲端，以便您可以彙總、 相互關聯、 分析和安全性事件的提醒。

![Azure 記錄整合][1]

## <a name="what-logs-can-i-integrate"></a>可以整合哪些記錄？

Azure 產生每個 Azure 服務的更多的記錄。 這些記錄被分為兩種主要類型︰

- **控制/管理記錄**，讓完善的 Azure 資源管理員建立、 更新及刪除作業。 Azure 稽核記錄是這種記錄類型的範例。
- **資料平面記錄**，讓完善引發屬於 Azure 資源的使用方式的事件。 此類型的記錄檔的範例 Windows 事件系統、 安全性]，而記錄虛擬機器中的應用程式。

Azure 記錄整合目前支援整合 Azure 稽核記錄、 虛擬機器記錄，以及 Azure 資訊安全中心的通知。

如果您有 Azure 記錄整合的相關問題，請傳送電子郵件[AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>後續步驟

在此文件中，您已簡介 Azure 記錄整合。 若要瞭解 Azure 記錄整合和的支援的記錄類型的詳細資訊，請參閱下列各項︰

- [Microsoft Azure 記錄整合 Azure 記錄 （預覽版本）](https://www.microsoft.com/download/details.aspx?id=53324) – 下載詳細資料、 系統需求和 Azure 記錄整合的安裝指示的中心。
- [Azure 記錄整合快速入門](security-azure-log-integration-get-started.md)-本教學課程中引導您安裝的 Azure 記錄整合，以及整合的 Azure 儲存體] Azure 稽核記錄，以及資訊安全中心警示的記錄。
- [合作夥伴設定步驟](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)– 此部落格文章說明如何設定使用合作夥伴解決方案 Splunk HP 討論 ArcSight，與 IBM QRadar Azure 記錄整合。
- [Azure 記錄整合常見問題集 (faq)](security-azure-log-integration-faq.md) -此常見問題集解答關於 Azure 記錄整合的問題。
- [Azure 的拷貝資訊安全中心警示登整合](../security-center/security-center-integrating-alerts-with-log-integration.md)– 這份文件會顯示您如何同步處理資訊安全中心的通知，以及收集 Azure 診斷和 Azure 稽核記錄，以與記錄分析或 SIEM 解決方案的虛擬機器安全性事件。
- [Azure 診斷和 Azure 稽核記錄的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)-此部落格文章會向您介紹 Azure 稽核記錄和其他功能，協助您獲得深入見解 Azure 資源的作業。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
