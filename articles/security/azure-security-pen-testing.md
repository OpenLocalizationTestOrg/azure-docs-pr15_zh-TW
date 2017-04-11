<properties
   pageTitle="畫筆測試 |Microsoft Azure"
   description="文件提供的滲透測試 (pentest) 程序概觀，以及如何執行 pentest 針對您執行 Azure 基礎結構中的應用程式。"
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>畫筆測試

好的項目，瞭解如何使用 Microsoft Azure 應用程式測試和部署是您不需要制定開發、 測試和部署應用程式的內部部署基礎結構。 所有的基礎結構是由 Microsoft Azure 平台服務的處理。 您不必擔心 requisitioning、 取得，並 「 軌道和堆疊 」 內部部署之硬體。

這是絕佳 –，但您仍需要請確定您執行一般的安全性到期的注意。 您需要執行的是滲透測試 Azure 中的部署的應用程式。

您可能已知道 Microsoft 執行[滲透測試我們 Azure 環境](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)。 這可協助我們改善我們的平台，並引導我們的動作，以改善的安全性控制項、 介紹新的安全性控制項，並改善我們的安全性程序。

我們不畫筆測試您的應用程式，但我們是否知道您會想要與需要執行畫筆測試您的應用程式。 這會是好的結果，是因為您強化您的應用程式的安全性，當您讓整個 Azure 生態更安全。

當您畫筆測試您的應用程式時，看起來像是攻擊給我們。 我們[持續監控](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx)攻擊模式，會啟動事件回應程序，我們需要如果。 它不會協助您，這樣不會有幫助我們我們觸發事件的回應，因為您自己到期注意畫筆測試。

如何？

當您準備好要畫筆測試 Azure 裝載於應用程式時，您需要讓我們知道。 我們知道您即將所執行的特定測試，我們就不會不慎您關閉 （例如封鎖您正在測試的 IP 位址），只要您測試符合 Azure 畫筆測試條款與條件。
您可以執行的標準測試包括︰

- 測試您的結束點，以發掘[開啟 Web 應用程式的安全性專案 (OWASP) 前 10 個弱點](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [模糊測試](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)您的端點
- [連接埠掃描](https://en.wikipedia.org/wiki/Port_scanner)您的端點

您無法執行的其中一種是測試的任何一種[拒絕服務 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack)攻擊。 這包含起始 DoS 攻擊，或執行可能會決定、 示範或模擬 DoS 攻擊的任何類型的相關的測試。

您已經準備好開始使用畫筆測試裝載於 Microsoft Azure 應用程式？ 如果是，然後標題上移至[滲透測試概觀](https://security-forms.azure.com/penetration-testing/terms)] 頁面 （然後按一下 [建立] 頁面底部的 [測試要求] 按鈕。 您也可以找到手寫筆測試條款與條件和很有幫助連結報告 Azure 或任何其他 Microsoft 服務相關的安全性問題的方式的詳細資訊。
