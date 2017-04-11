<properties 
    pageTitle="建立及還原備份，Biztalk |Microsoft Azure" 
    description="Biztalk 包含備份與還原。 瞭解如何建立及還原的備份，並判斷哪些取得備份。 MABS WABS" 
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


# <a name="biztalk-services-backup-and-restore"></a>Biztalk︰ 備份與還原

Azure Biztalk 包括備份與還原的功能。 本主題說明如何使用備份與還原 BizTalk 服務使用 Azure 傳統入口網站。

您也可以備份使用[BizTalk 服務 REST API](http://go.microsoft.com/fwlink/p/?LinkID=325584)BizTalk 服務。 

> [AZURE.NOTE] 混合式連線不備份，無論版本。 您必須重新建立混合式連線。

## <a name="before-you-begin"></a>開始之前

- 備份與還原可能無法使用的所有版本。 請參閱[Biztalk︰ 版本圖表](biztalk-editions-feature-chart.md)。

- 使用 Azure 傳統入口網站，您就可以建立在指定備份，或建立排定的備份。 

- 相同 BizTalk 服務或新的 BizTalk 服務，就可以還原備份的內容。 若要還原 BizTalk 服務使用相同的名稱，必須先刪除現有的 BizTalk 服務，而且名稱必須使用。 刪除 BizTalk 服務之後，可能需要較長的時間超過希望能夠使用相同的名稱。 如果您不能等候能夠使用相同的名稱，然後還原至新的 BizTalk 服務。

- BizTalk 服務可還原至相同的版本或更新的版本。 還原 BizTalk 服務至較低的版本，從時備份，不支援。

    例如，使用基本版備份可還原至進階版。 使用進階版備份無法還原至標準版。

- 若要維持連續性控制項數字的備份 EDI 控制項數字。 如果郵件處理上次備份後，還原備份內容可能會導致重複控制項數字。

- 如果以批次有作用中的郵件，處理批次**之前**執行備份。 時 （如需或排程） 建立備份，以批次的郵件永遠不儲存。 

    **如果在備份的作用中的郵件，以批次，這些訊息尚未備份，因此會遺失。**

- 可省略︰ BizTalk Services 入口網站中停止任何管理作業。


## <a name="create-a-backup"></a>建立備份

備份隨時都可以採取，並由您完全控制。 本節列出的步驟來建立備份使用 Azure 傳統入口網站，包括︰

[在 [指定備份](#backupnow)

[排程備份](#backupschedule)

#### <a name="backupnow"></a>在 [指定備份
1. 在 Azure 傳統的入口網站中，選取 [ **BizTalk 服務**]，然後選取您想要的 BizTalk 服務備份。
2. 在 [**儀表板**] 索引標籤中，選取 [在頁面底部的 [**備份**]。
3. 輸入備份的名稱。 例如，輸入*myBizTalkService*BU*日期*。
4. 選擇 [blob 儲存體帳戶，然後選取核取記號，若要開始備份]。

備份完成後，您輸入的備份名稱與容器會建立在儲存帳戶。 此容器包含 BizTalk 服務備份設定。

#### <a name="backupschedule"></a>排程備份

1. 在 Azure 傳統的入口網站中，選取**BizTalk 服務**、 選取您想要安排備份，BizTalk 服務名稱，然後選取 [**設定**] 索引標籤。
2. 將**備份狀態**設定為 [**自動]**。 
3. 選取的**儲存空間帳戶**儲存備份，請輸入建立備份，**頻率**，以及要保留備份 （**保留天**）︰

    ![][AutomaticBU]

    **備忘稿**   
    - **保留天**，保留期限必須大於備份的頻率。
    - 選取 [**永遠保留至少有一個備份**，即使是過去的保留期限。
    

4. 選取 [**儲存**]。


排定的備份工作執行時，它建立 （若要儲存備份資料） 容器中您所輸入的儲存空間帳戶。 容器的名稱會命名*BizTalk 服務名稱日期時間*。 

如果 [BizTalk 服務儀表板顯示狀態為 [**失敗**︰

![最後一個排程的備份狀態][BackupStatus] 

連結會開啟管理服務作業記錄，協助進行疑難排解。 請參閱[Biztalk︰ 使用作業記錄進行疑難排解](http://go.microsoft.com/fwlink/p/?LinkId=391211)。

## <a name="restore"></a>還原

從 Azure 傳統入口網站或[還原 BizTalk 服務 REST API](http://go.microsoft.com/fwlink/p/?LinkID=325582)，您可以還原備份。 本節會列出還原使用傳統的入口網站的步驟。

#### <a name="before-restoring-a-backup"></a>還原備份前

- 您可以輸入新追蹤、 封存以及監控儲存時還原 BizTalk 服務。

- 還原相同 EDI 執行階段資料。 EDI 執行階段備份 」 會將控制項數字。 還原的控制項數字是從備份的時間。 如果郵件處理上次備份後，還原備份內容可能會導致重複控制項數字。

#### <a name="restore-a-backup"></a>還原的備份

1. 在 Azure 傳統的入口網站中，選取 [**新增** > **應用程式服務** > **BizTalk 服務** > **還原**︰

    ![還原的備份][Restore]

2. 在**備份的 URL**，請選取資料夾圖示，然後展開儲存 BizTalk 服務設定備份 Azure 儲存體帳戶。 展開容器，然後在右窗格中，選取對應.txt 檔案備份。 
<br/><br/>
選取 [**開啟**]。

3. 在 [**還原 BizTalk 服務**] 頁面上輸入**BizTalk 服務名稱**並確認**網域 URL**、 **Edition**和**區域**還原 BizTalk 服務。 追蹤資料庫中**建立新的 SQL 資料庫執行個體**︰

    ![][RestoreBizTalkService]

    選取下一個箭號。

4.  檢查 SQL 資料庫的名稱，輸入該伺服器的實體伺服器位置將會建立 SQL 資料庫及使用者名稱與密碼。


    如果您想要設定 SQL 資料庫版本，大小和其他內容，選取 [**設定進階資料庫設定**。 

    選取下一個箭號。

5. 建立新的儲存空間帳戶，或輸入 BizTalk 服務的現有的儲存空間帳戶。

7. 選取核取記號，以開始還原。

還原已順利完成之後，新的 BizTalk 服務會列在 Azure 傳統入口網站中的 [BizTalk 服務] 頁面上已擱置狀態中。



### <a name="postrestore"></a>還原備份後

BizTalk 服務一律會還原為 [**已擱置**狀態。 在這個狀態，您可以變更任何設定功能，包括新的環境之前︰

- 如果您建立 BizTalk 服務應用程式使用 Azure BizTalk 服務 SDK，您可能需要更新那些應用程式使用還原環境中的存取控制 (ACS) 認證。

- 您還原 BizTalk 服務複寫現有 BizTalk 服務環境。 在此情況下，如果有合約設定原始 Biztalk 入口網站中使用來源 FTP 資料夾，您可能需要更新的合約新還原環境中使用不同的來源 FTP 資料夾中。 否則，可能會想要將相同的郵件的兩個不同合約。

- 如果您還原有多個 BizTalk 服務環境，請確定您鎖定 Visual Studio 應用程式、 PowerShell cmdlet，REST Api 或交易合作夥伴管理 OM Api 中正確的環境。

- 最好在新還原 BizTalk 服務環境中設定自動的備份。

若要啟動 BizTalk 服務 Azure 傳統入口網站中，選取 [還原的 BizTalk 服務並選取任務列中的**履歷表**。 



## <a name="what-gets-backed-up"></a>什麼會備份

建立備份檔案時，會備份下列項目︰

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>備份的項目</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk Services 入口網站</strong></td>
</tr> 
<tr>
<td>設定和執行階段</td> 
<td>
<ul>
<li>合作夥伴與設定檔詳細資料</li>
<li>合作夥伴合約</li>
<li>部署自訂的組件</li>
<li>部署橋</li>
<li>憑證</li>
<li>部署的轉換</li>
<li>管線</li>
<li>建立並儲存 BizTalk Services 入口網站中的範本</li>
<li>X12 ST01 和 GS01 對應</li>
<li>控制數字 (EDI)</li>
<li>AS2 訊息麥克風值</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Azure BizTalk 服務</strong></td>
</tr> 
<tr>
<td>SSL 憑證</td> 
<td>
<ul>
<li>SSL 憑證資料</li>
<li>SSL 憑證密碼</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk 服務設定</td> 
<td>
<ul>
<li>計算比例單位</li>
<li>版本</li>
<li>產品版本</li>
<li>地區/資料中心</li>
<li>Access 控制項服務 (ACS) 命名空間和索引鍵</li>
<li>追蹤資料庫連線字串</li>
<li>封存儲存帳戶的連線字串</li>
<li>監控儲存帳戶的連線字串</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>其他項目</strong></td>
</tr> 
<tr>
<td>追蹤資料庫</td> 
<td>建立 BizTalk 服務時，會輸入追蹤資料庫的詳細資訊，包括 Azure SQL 資料庫伺服器及追蹤資料庫的名稱。 追蹤資料庫會不會自動備份。
<br/><br/>
<strong>重要</strong><br/>
如果追蹤資料庫會刪除已復原的資料庫的需求，必須有先前的備份。 如果備份不存在，追蹤資料庫及其資料無法復原。 在此情況下，建立新的追蹤資料庫使用相同的資料庫名稱。 建議您地理複寫。</td>
</tr> 
</table>

## <a name="next"></a>下一步

若要建立 Azure Biztalk Azure 傳統入口網站中，移至[Biztalk︰ 佈建使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)。 若要開始建立應用程式，請移至[Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

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

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
