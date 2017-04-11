<properties 
   pageTitle="疑難排解指南-分析 azure 行動互動" 
   description="疑難排解問題分析、 監控、 分割，以及儀表板中 Azure 行動互動" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>分析、 監控、 分割，以及儀表板問題的疑難排解指南

以下是可能的問題可能會遇到的 Azure 行動的互動方式收集您的應用程式、 裝置與使用者的相關資訊。

## <a name="missingdelayed-information"></a>延遲遺失的資訊

### <a name="issue"></a>問題
- 資訊會出現在分析、 分割或儀表板中延遲。
- 從 [監控遺失的資訊。
- 從分析、 分割或儀表板遺失的資訊。
- 正中分割限制。

### <a name="causes"></a>原因

- 您可以使用狀況分析 API，監視器 API，以查看的任何資料，您是否缺少 UI 區段 API 是透過 Api 可見。
- 如果 Azure 行動互動 SDK 不正確地整合至您的應用程式然後將無法看到分析、 分割、 監控或儀表板中的資訊。
- 區段無法變更後所建立，區段只能 」 複製 」 （複製） 或 「 損毀] （刪除）。 區段只能包含 10 的準則。
- 若要測試缺少的資訊，從 [監視的最佳方式是設定測試裝置、 解除安裝及/或重新安裝測試裝置上的應用程式。
- 分析、 分割或儀表板會重新整理資訊每 24 小時。
- 直到 24 小時，即使該區段根據先前的資訊在建立之後，可能不會顯示新的區段中的資訊。
- 篩選分析資料在 ui 上的會顯示所有應用程式 （例如 「 當機 」 做為篩選依據名稱會顯示從版本 1 和 2 版應用程式） 版本此類型的範例。
- 分析期間根據使用者的裝置設定]，從日期，其電話有設定不正確的日期的使用者無法顯示這些錯誤的期間。
- 當您使用的按鈕來 「 測試 」 記錄資料沒有伺服器端推入、 資料只會為實數推入行銷活動記錄。

## <a name="cant-locate-items-in-ui"></a>使用者介面中找不到項目

### <a name="issue"></a>問題
- 無法建立根據特定內建的區段，或自訂應用程式資訊標記準則。
- 內建於特定找不到或無法自訂應用程式資訊標記分析、 監控或儀表板中的準則。
- 無法解譯分析、 監控、 分割或儀表板中的資料。

### <a name="causes"></a>原因

- 部分內建的項目，而應用程式資訊標籤只可做為推入準則，但可能無法新增至區段或可見分析、 監控，或儀表板。 
- 內建的項目以及應用程式資訊標記無法新增至一個區段，您必須安裝程式清單中每個行銷活動的準則針對選取目標執行相同的功能目標根據一個區段。
- 請參閱 Azure 行動互動 UI 如需詳細說明的分析、 監控、 分割，以及儀表板各節中的快顯功能表，以及如何資訊。

## <a name="crash-troubleshooting"></a>當機疑難排解

### <a name="issue"></a>問題
- 分析、 監控或儀表板中出現的應用程式當機。

### <a name="causes"></a>原因

- 若要疑難排解應用程式當機分析、 監控或儀表板中看到確定檢查 SDK 的舊版本的已知問題的版本資訊。
- 若要進一步疑難排解應用程式當機執行事件的測試裝置以搭配您的應用程式安裝及設定您的裝置識別碼 Azure 行動互動 UI 」 監視-事件 」 一節中的外觀。 接著執行的事件，導致您的應用程式當機和查閱 Azure 行動互動 UI 的 「 監控-當機 」 一節中的其他資訊。 

 
