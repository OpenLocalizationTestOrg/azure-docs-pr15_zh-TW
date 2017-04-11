<properties 
    pageTitle="疑難排解 BizTalk 服務使用作業記錄 |Microsoft Azure" 
    description="疑難排解 BizTalk 服務使用的作業的記錄。 MABS WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>使用作業記錄進行疑難排解的 BizTalk 服務︰

## <a name="what-are-the-operation-logs"></a>什麼是作業記錄
作業的記錄是管理服務的功能可讓您檢視 Azure 服務，包括 BizTalk 服務上執行的作業的歷史記錄 Azure 傳統入口網站提供。 這可讓您檢視記錄管理作業早 180 天 BizTalk 服務訂閱相關的資料。

> [AZURE.NOTE]此功能僅擷取 BizTalk 服務，例如啟動服務時，管理作業的記錄備份最，依此類推。 不管資料是否執行從 Azure 傳統入口網站，或使用[BizTalk 服務 REST Api](http://msdn.microsoft.com/library/azure/dn232347.aspx)來追蹤這類的作業。 使用管理服務所追蹤之作業的完整清單，請參閱[作業追蹤使用 Azure 管理服務](#bizops)。<br/><br/>
不會擷取活動相關 BizTalk 服務執行階段 （例如郵件處理橋，等等。） 的記錄。 若要檢視這些記錄，請使用從 Biztalk 入口網站的 [追蹤] 檢視。 如需詳細資訊，請參閱[追蹤郵件](http://msdn.microsoft.com/library/azure/hh949805.aspx)。

## <a name="view-biztalk-services-operation-logs"></a>檢視 BizTalk 服務作業的記錄檔
1. 在 Azure 傳統的入口網站中，選取 [**管理服務**，然後選取 [**作業的記錄檔**] 索引標籤。
2. 您可以篩選根據不同的參數，例如訂閱、 日期範圍、 服務類型 （例如 BizTalk 服務）、 服務名稱或狀態 （成功、 失敗） 作業的記錄。
3. 選取核取記號，若要檢視篩選的清單。 下圖顯示有關 testbiztalkservice 活動︰ ![檢視作業的記錄檔][ViewLogs] 
4. 若要檢視更多有關特定的作業，請選取該列，並按一下底部的工作列中的 [**詳細資料**。


## <a name="bizops"></a>追蹤使用 Azure 管理服務的作業
下表列出會追蹤使用 Azure 管理服務的作業︰

作業名稱 | 任務
--- | ---
CreateBizTalkService | 若要建立新的 BizTalk 服務的作業
DeleteBizTalkService | 若要刪除 BizTalk 服務的作業
RestartBizTalkService | 若要重新啟動 BizTalk 服務的作業
StartBizTalkService | 若要啟動 BizTalk 服務的作業
StopBizTalkService | 若要停止 BizTalk 服務的作業
DisableBizTalkService | 若要停用 BizTalk 服務的作業
EnableBizTalkService | 若要啟用 BizTalk 服務的作業
BackupBizTalkService | 若要備份 BizTalk 服務的作業
RestoreBizTalkService | 從指定備份還原 BizTalk 服務的作業
SuspendBizTalkService | 若要暫停 BizTalk 服務的作業
ResumeBizTalkService | 若要繼續 BizTalk 服務的作業
ScaleBizTalkService | 若要向上或向下縮放 BizTalk 服務的作業
ConfigUpdateBizTalkService | 若要更新的 BizTalk 服務設定的作業
ServiceUpdateBizTalkService | 若要升級或降級 BizTalk 服務的其他版本的作業
PurgeBackupBizTalkService | 若要清除備份 BizTalk 服務外保留期間的作業


## <a name="see-also"></a>另請參閱
- [備份 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [從備份還原 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Biztalk︰ 開發人員、 基本、 標準和進階版圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Biztalk︰ 佈建使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Biztalk︰ 佈建狀態圖表](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Biztalk︰ 節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Biztalk︰ 發行者名稱和發行者鍵](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [我要如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
