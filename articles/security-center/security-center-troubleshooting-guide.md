<properties
   pageTitle="Azure 資訊安全中心疑難排解指南 |Microsoft Azure"
   description="這份文件，有助於在 Azure 資訊安全中心的問題進行疑難排解。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Azure 資訊安全中心疑難排解指南
本指南適用於資訊技術 (IT) 專業人員與資訊安全性分析師雲端系統管理員的組織使用 Azure 資訊安全中心，而且必須以疑難排解資訊安全中心相關問題。

## <a name="troubleshooting-guide"></a>疑難排解指南
本文說明如何疑難排解資訊安全中心相關問題。 大部分的疑難排解資訊安全中心完成會在失敗的元件的[稽核記錄](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/)記錄的第一次尋找進行。 透過稽核記錄，您可以判斷︰

- 已執行的作業
- 使用者發起的租用戶作業
- 發生作業
- 作業的狀態
- 也許可以協助您其他屬性的值研究作業

稽核記錄包含您的資源執行所有寫入作業 （放入，文章，刪除），但不包括讀取的作業 （取得）。

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>疑難排解 Windows 中監控代理程式的安裝

監控代理程式的資訊安全中心用來執行資料集合。 啟用資料收集與代理程式的安裝正確在目標電腦之後，這些處理程序應該執行中︰

- ASMAgentLauncher.exe-Azure 監控代理程式 
- ASMMonitoringAgent.exe-Azure 安全性監視副檔名
- ASMSoftwareScanner.exe – Azure 掃描管理員

Azure 安全性監視副檔名為掃描各種安全性相關設定，並從虛擬機器收集安全性記錄。 掃描管理員作為修補程式掃描器。

如果順利執行安裝，您應該會看到類似以下的目標 VM 稽核記錄中的項目︰

![稽核記錄](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

您也可以閱讀代理程式的記錄，位於*%systemdrive%\windowsazure\logs* ，取得安裝程序的詳細資訊 (範例︰ C:\WindowsAzure\Logs)。

> [AZURE.NOTE] 如果行為異常 Azure 安全性中心代理程式，您必須重新啟動目標 VM，因為沒有停止並啟動代理程式命令。

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>疑難排解在 Linux 監控代理程式的安裝
疑難排解 VM 代理程式安裝在 Linux 系統時，您應該確認副檔名為已下載至/var/文件庫/waagent /。 您可以執行下列命令，以確認已安裝︰

`cat /var/log/waagent.log` 

其他記錄檔，您可以檢閱疑難排解用途是︰ 

- /var/log/mdsd.err
- / var/記錄/azure /

使用系統中您應該會看到 mdsd 程序的連線 TCP 29130 上。 這是與 mdsd 程序通訊系統。 您可以執行下列命令以進行驗證這種行為︰

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>連絡 Microsoft 支援服務

可以使用本文中，您也可以找到其他記載資訊安全中心公用[論壇，](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)在提供的指導方針識別的一些問題。 但是如果您需要進一步疑難排解，您可以開啟新的支援要求使用 Azure 入口網站，如下所示︰ 

![Microsoft 支援服務](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>另請參閱

在此文件中，您學到如何設定在 Azure 資訊安全中心的安全性原則。 若要進一步瞭解 Azure 資訊安全中心 」 的資訊，請參閱下列各項︰

- [Azure 安全性中心規劃及作業指南](security-center-planning-and-operations-guide.md)，瞭解如何規劃及瞭解採用 Azure 資訊安全中心的設計考量。
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)，瞭解如何管理和回應的安全性警告
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循
