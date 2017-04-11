<properties 
    pageTitle="允許在不同的狀態或 BizTalk 服務的狀態中的工作 |Microsoft Azure" 
    description="允許在不同的 MABS 狀態動作/作業︰ 停止、 啟動、 重新啟動、 暫停、 繼續、 刪除、 不按比例縮放、 更新設定的設定，以及支援" 
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



# <a name="biztalk-services-service-state-chart"></a>Biztalk︰ 服務狀態圖表
根據 BizTalk 服務的目前狀態，有可或 BizTalk 服務無法執行的操作。

例如，您佈建 Azure 傳統入口網站中的新 BizTalk 服務。 當順利完成時，BizTalk 服務會處於作用中狀態。 在 [作用中狀態，您可以停止 BizTalk 服務。 如果成功停駐點，BizTalk 服務就會進入停止狀態。 如果停駐點失敗，請 BizTalk 服務就會前往 StopFailed 狀態。 在 [StopFailed 狀態，您可以重新啟動 BizTalk 服務。 如果您嘗試在不允許的作業，例如履歷表 BizTalk 服務發生下列錯誤︰

**不允許作業**

佈建 BizTalk 服務，請移至[Biztalk︰ 佈建使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)。

下表列出作業或可完成的特定狀態 BizTalk 服務時的動作。 ✔ 表示允許在該狀態時這項操作。 空白的項目表示無法在該狀態時執行的作業。

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>啟動、 停止、 重新啟動、 暫停，履歷表，和刪除作業
<table border="1">
<tr>
        <th colspan="15">作業或動作</th>
</tr>

<tr>
        <th rowspan="18">BizTalk 服務狀態</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>開始</th>
        <th>停駐點</th>
        <th>重新啟動</th>
        <th>暫停</th>
        <th>履歷表</th>
        <th>刪除</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>作用中</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停用</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>暫停</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停止</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>服務的更新失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>縮放比例與更新設定備份作業
<table border="1">
<tr>
        <th colspan="15">作業或動作</th>
</tr>

<tr>
        <th rowspan="18">BizTalk 服務狀態</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>縮放比例</th>
        <th>更新設定</th>
        <th>備份</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>作用中</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停用</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>暫停</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停止</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>服務的更新失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>另請參閱
- [Biztalk︰ 佈建使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Biztalk︰ 開發人員、 基本、 標準和進階版圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Biztalk︰ 備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Biztalk︰ 節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Biztalk︰ 發行者名稱和發行者鍵](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [我要如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
