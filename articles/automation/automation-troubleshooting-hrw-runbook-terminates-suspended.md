<properties
   pageTitle="混合式 Runbook 工作者︰ 狀態為 [已擱置 」 下方的結束 runbook 工作 |Microsoft Azure"
   description="發生上述任何症狀原因與解決方案的混合式 Runbook 工作者工作終止錯誤。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>混合式 Runbook 工作者︰ 狀態為 [已擱置 」 下方的結束 runbook 工作

## <a name="summary"></a>摘要

暫停您 runbook，在嘗試執行三次。 有可能會干擾無法順利完成 runbook 條件和相關的錯誤訊息不包含任何表示原因的詳細資訊。 本文提供混合式 Runbook 工作者 runbook 執行失敗的相關問題的疑難排解步驟。

如果本文並未解決您 Azure 的問題，請造訪 Azure 論壇，在[MSDN 和堆疊溢位](https://azure.microsoft.com/support/forums/)。 您可以將您的問題張貼在這些論壇，或者[@AzureSupportTwitter 上](https://twitter.com/AzureSupport)。 此外，您可以選取[Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [**取得支援**的檔案 Azure 支援要求。

## <a name="symptom"></a>徵狀

Runbook 執行失敗，傳回的錯誤，「 工作動作 」 啟動 「 無法執行，因為意外停止程序。 工作動作嘗試 3 次。 」


## <a name="cause"></a>原因

有錯誤的幾個可能的原因︰ 

  1. 混合式工作者是 proxy 或防火牆後
  2. 電腦執行混合式工作者有小於最低硬體[需求](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) 
  3. Runbooks 無法驗證使用本機資源


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>原因 1︰ 混合式 Runbook 工作者位於 proxy 或防火牆

電腦執行混合式 Runbook 工作者是防火牆或 proxy 伺服器，輸出網路存取可能不允許或正確設定。

### <a name="solution"></a>解決方案

確認電腦有的輸出存取 *。 cloudapp.net 連接埠 443、 9354，和 30000 30199 上的。 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>原因 2︰ 電腦有小於最小的硬體需求

執行混合式 Runbook 工作者電腦應符合最低硬體需求之前指定其主控這項功能。 否則，根據的其他背景處理程序和競爭執行期間所致 runbooks 資源使用狀況，電腦會成為超過利用，會造成 runbook 工作延遲或逾時。 

### <a name="solution"></a>解決方案 

首先請確認指定要執行混合式 Runbook 工作者功能的電腦符合最低硬體需求。  如果是這樣，監控 CPU 和記憶體使用率，以確定任何混合式 Runbook 背景工作處理程序的效能與 Windows 之間的關聯。  如果沒有超出記憶體或 CPU 壓力，可能需要升級或新增額外的處理器，或增加地址資源瓶頸並解決錯誤的記憶體。 或者，選取不同的計算資源時工作量需求表示增加是必要的最低需求及縮放比例支援。         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>原因 3︰ 使用本機資源 Runbooks 無法進行驗證

### <a name="solution"></a>解決方案

核取對應的事件*Win32 結束，[4294967295] 的程式碼的程序*的描述與**Microsoft SMA**事件記錄檔。  這項錯誤的原因是，您還沒有設定驗證，在您 runbooks 或指定混合式工作群組的執行方式認證。  請檢閱[Runbook 權限](automation-hybrid-runbook-worker.md#runbook-permissions)，以確認您已經為您 runbooks 正確設定驗證。  


 

