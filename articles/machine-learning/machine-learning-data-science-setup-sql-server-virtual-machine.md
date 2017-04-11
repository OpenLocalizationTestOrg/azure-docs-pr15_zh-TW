<properties
    pageTitle="設定 [SQL Server 虛擬機器 IPython 筆記本伺服器 |Microsoft Azure"
    description="設定設定資料科學虛擬機器 SQL Server 與 IPython 伺服器。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev" 
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Azure SQL Server 虛擬機器設為進階分析 IPython 筆記本伺服器

本主題說明如何佈建和設定 SQL Server 的虛擬機器作為雲端為基礎的資料科學環境的一部分。 設定 Windows 虛擬機器是支援工具，例如 IPython 筆記本、 Azure 儲存檔案總管和 AzCopy，以及其他公用程式適合資料科學專案。 Azure 儲存檔案總管] 及 [AzCopy，例如，提供方便的方式，將資料上傳至 Azure blob 儲存體從本機電腦，或將其從 blob 儲存體下載至您的本機電腦。

Azure 虛擬機器庫包含包含 Microsoft SQL Server 的數個圖像。 選取適合您資料的需求的 SQL Server VM 圖像。 建議的圖像是︰

- SQL Server 2012 SP2 企業版中的資料大小的小型企業版
- SQL Server 2012 SP2 企業最佳化 DataWarehousing 負載大很大的資料大小

 > [AZURE.NOTE] SQL Server 2012 SP2 企業圖像**不包含資料磁碟**。 您必須新增及/或附加一或多個虛擬硬碟儲存您的資料。 當您建立 Azure 虛擬機器時，有一個對應至 C 磁碟機的作業系統的磁碟及對應至 D 磁碟機暫時磁碟。 儲存的資料使用 D 磁碟機。 顧名思義，它會提供暫時存放區。 因為它不會位於 Azure 儲存體中提供任何重複或備份。


##<a name="Provision"></a>連線至 Azure 傳統入口網站，並提供的 SQL Server 虛擬機器

1.  [Azure 傳統入口網站](http://manage.windowsazure.com/)使用您的帳戶登入。
    如果您沒有 Azure 帳戶，請造訪[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

2.  Azure 傳統入口網站上，在左下方的網頁上，按一下 [ **+] 新增**、 按一下**計算**，按一下**虛擬機器**、，然後按一下 [**從圖庫**。

3.  在**建立虛擬機器**頁面上，選取包含您資料的需求，所根據的 SQL Server 的虛擬機器圖像，然後按一下右下方的頁面] 的下一個箭號。 Azure 上支援的 SQL Server 圖像上最新的資訊，請參閱[開始使用 SQL Server Azure 虛擬機器中的商務連絡人](http://go.microsoft.com/fwlink/p/?LinkId=294720)主題的[SQL Server Azure 虛擬機器中的商務連絡人](http://go.microsoft.com/fwlink/p/?LinkId=294719)文件設定。

    ![選取 [SQL Server VM][1]

4.  在 [第一個**虛擬機器設定**] 頁面上提供下列資訊︰

    -   提供的**虛擬機器名稱**。
    -   在 [**新的使用者名稱**] 方塊中，輸入唯一的使用者名稱 VM 本機系統管理員帳戶。
    -   在 [**新密碼**] 方塊中，輸入強式密碼。 如需詳細資訊，請參閱[強式密碼](http://msdn.microsoft.com/library/ms161962.aspx)。
    -   在 [**確認密碼**] 方塊中，重新輸入密碼。
    -   從下拉式清單中選取適當的**大小**。

     > [AZURE.NOTE] 期間佈建指定的虛擬機器大小︰ A2 則建議使用生產工作負載的最小的大小。 使用 SQL Server 企業版時，建議使用最小虛擬機器為 A3。 選取 A3 或更新版本時使用 SQL Server 企業版。 使用 SQL Server 2012 或 2014年企業最佳化的交易工作負載的圖像時，請選取 [A4]。
使用 SQL Server 2012 或 2014年企業最佳化的資料倉儲負載圖像時，請選取 [A7]。 選取的大小限制您可以設定的資料磁碟數目。 最新可用的虛擬機器大小和數字的資料可附加至虛擬機器的磁碟的詳細資訊，請參閱[Azure 虛擬機器大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。 價格資訊，請參閱[虛擬 Macines 價格](https://azure.microsoft.com/pricing/details/virtual-machines/)。

    按一下右下方以繼續進行下一個箭號。

    ![VM 設定][2]

5.  在第二個**虛擬機器設定**頁面上，設定網路、 儲存及可用性的資源︰

    -   在**雲端服務**] 方塊中，選擇 [**建立新的雲端服務**。
    -   在 [**雲端服務的 DNS 名稱**] 方塊中，提供您選擇的 DNS 名稱的第一個部分，讓它完成格式**TESTNAME.cloudapp.net**中的名稱
    -   在 [**地區/相關性群組/虛擬網路**] 方塊中，選取會裝載於此虛擬圖像的區域。
    -   在**儲存帳戶**中，選取現有的儲存空間帳戶或選取自動產生的項目。
    -   在**可用的設定**] 方塊中，選取 [ **（無）**。
    -   閱讀並接受價格資訊。

6.  在 [**結束點**] 區段中，按一下 [**名稱**] 底下的空白下拉式清單中並選取**MSSQL**然後輸入資料庫引擎 (**1433年**預設的執行個體) 的執行個體的連接埠號碼。

7.  您的 SQL Server VM 也可以做為 IPython 筆記本伺服器，會設定為稍後的步驟。
    新增新的結束點，以指定要用於 IPython 筆記本伺服器的連接埠。 在 [**名稱**] 欄中輸入名稱，請選取您的公用的連接埠的選擇和 9999 私人的連接埠的連接埠號碼。

    按一下右下方以繼續進行下一個箭號。

    ![選取 MSSQL 和 IPython 連接埠][3]

8.  接受預設**安裝 VM 代理程式**的選項核取，然後按一下右下角的精靈完成 VM 佈建程序中的核取記號。

    `![VM 最後的選項][4]

9.  請等候 Azure 準備您的虛擬機器。 預期的虛擬機器狀態]，繼續進行︰

    -   開始 （佈建）
    -   停止
    -   開始 （佈建）
    -   執行 （佈建）
    -   執行


##<a name="RemoteDesktop"></a>開啟使用遠端桌面] 和 [完成安裝的虛擬機器

1.  佈建完成後，請按一下您要移至儀表板頁面的虛擬機器的名稱。 在頁面底部，按一下 [**連線**]。

2.  選擇 [開啟 rpd 檔案使用 Windows 遠端桌面程式 (`%windir%\system32\mstsc.exe`)。

3.  在 [ **Windows 安全性**] 對話方塊中，提供您在先前的步驟所指定的本機系統管理員帳戶的密碼。
    （您可能會要求驗證認證的虛擬機器。）

4.  第一次登入此虛擬機器，完成，包括您的桌面，Windows 更新與 Windows 初始設定工作 (sysprep) 完成設定可能需要一些處理程序。 Windows sysprep 完成之後，SQL Server 安裝程式就會完成設定工作。 完成時，這些工作可能會導致幾分鐘的延遲。 `SELECT @@SERVERNAME`SQL Server Management Studio 中可能不是在 [開始] 畫面上可見，直到 SQL Server 安裝完成之後，可能會傳回正確的名稱。

一旦您連線到 Windows 遠端桌面的虛擬機器，虛擬機器運作很像其他任何電腦。 以正常方式，以連線至預設的執行個體的 SQL Server，與 SQL Server Management Studio （虛擬機器上執行）。


##<a name="InstallIPython"></a>安裝 IPython 筆記本和其他支援的工具

若要設定新的 SQL Server VM 做為 IPython 筆記本伺服器，並安裝其他支援的工具，例如 AzCopy、 Azure 儲存檔案總管，實用的資料科學 Python 套件和其他人，特殊的自訂指令碼提供給您。 安裝︰

- 以滑鼠右鍵按一下**Windows [開始**] 圖示，然後按一下 [**命令提示字元 （系統）**
- 複製下列命令，並貼上的命令提示字元。

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- 出現提示時，請 IPython 筆記本伺服器中輸入您所選擇的密碼。
- 自訂指令碼會自動執行多個後安裝程序，包括︰
    + 安裝和設定 IPython 筆記本伺服器
    + 開啟先前建立的結束點的 Windows 防火牆的 TCP 連接埠︰
    + SQL Server 遠端連線
    + IPython 筆記本伺服器遠端連線
    + 擷取範例 IPython 筆記本和 SQL 指令碼
    + 下載並安裝實用的資料科學 Python 套件
    + 下載並安裝 Azure 工具，例如 AzCopy 和 Azure 儲存檔案總管  
<br>
- 您可能會存取及執行任何本機或遠端瀏覽器使用的表單 URL IPython 筆記本`https://<virtual_machine_DNS_name>:<port>`、 連接埠是您選取時佈建虛擬機器 IPython 公用連接埠的位置。
- IPython 筆記本伺服器執行做為背景的服務，並將會重新啟動自動當您重新啟動虛擬機器。

##<a name="Optional"></a>視需要將資料磁碟的連結

如果 VM 圖像不包含資料的磁碟，亦即磁碟非 C 磁碟機 （OS 磁碟） 和 D 磁碟機 （暫時磁碟），您需要新增一或多個資料磁碟，儲存您的資料。 SQL Server 2012 SP2 企業最佳化 DataWarehousing 工作負載的 VM 圖像隨附預先設定的其他磁碟的 SQL Server 資料和記錄檔。

 > [AZURE.NOTE] 儲存的資料使用 D 磁碟機。 顧名思義，它會提供暫時存放區。 因為它不會位於 Azure 儲存體中提供任何重複或備份。

若要附加的其他資料磁碟，請依照下列[附加資料磁碟 Windows 虛擬機器的方式](virtual-machines-windows-classic-attach-disk.md)，將引導您完成其中所述的步驟︰

1. 附加至虛擬機器佈建在先前的步驟中的 [空白磁碟
2. 初始設定的虛擬機器中的新磁碟


##<a name="SSMS"></a>連線至 SQL Server Management Studio 並啟用混合的模式驗證

SQL Server 資料庫引擎無法使用 Windows 驗證網域環境中沒有。 若要從另一部電腦連線至資料庫引擎，設定混合的模式驗證的 SQL Server。 SQL Server 驗證] 和 [Windows 驗證，可讓混合的模式驗證。 SQL 驗證模式，才能內嵌直接從 SQL Server VM 資料庫[Azure 電腦學習 Studio](https://studio.azureml.net)中使用 [匯入資料] 模組中的資料。

1.  在使用遠端桌面連線到虛擬機器，請使用 [Windows**搜尋**] 窗格，然後輸入**SQL Server Management Studio** （簡訊）。 按一下以啟動 SQL Server 管理 Studio (SSMS)。 若要將捷徑新增到 SSMS 在桌面上，以供日後使用。

    ![開始 SSMS][5]

    第一次開啟 Management Studio 中必須建立使用者 Management Studio 環境。 這可能需要幾分鐘的時間。

2.  開啟時，Management Studio 中會呈現**至伺服器的連線**] 對話方塊。 在 [**伺服器名稱**] 方塊中，輸入虛擬機器連線至資料庫引擎物件檔案總管的名稱。
    （而不是虛擬機器名稱您也可以使用**（本機）**或單一句點作為**伺服器名稱**。 選取 [ **Windows 驗證**]，然後保留***您\_VM\_名稱*\\您\_本機\_管理員****使用者名稱**] 方塊中。 按一下 [**連線**]。

    ![連線到伺服器][6]

    <br>

     > [AZURE.TIP] 您可能會變更使用 Windows 登錄鍵變更，或使用 SQL Server Management Studio 的 SQL Server 驗證模式。 若要變更使用登錄鍵變更驗證模式，開始**新的查詢**，然後執行下列指令碼︰

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    若要變更使用 SQL Server 管理 Studio 驗證模式︰

3.  在**SQL Server 管理 Studio 物件總管]**中，以滑鼠右鍵按一下 [SQL Server （虛擬機器名稱） 的執行個體的名稱，然後再按一下 [**內容**。

    ![伺服器內容][7]

4.  在**伺服器驗證**] 下的 [**安全性**] 頁面選取 [ **SQL Server 和 Windows 驗證模式**]，然後按一下**[確定]**。

    ![選取驗證模式][8]

5.  在 [ **SQL Server Management Studio** ] 對話方塊中，按一下**[確定**] 以確認重新啟動 SQL Server 需求。

6.  在 [**物件總管]**中，您的伺服器，以滑鼠右鍵按一下，然後按一下**重新啟動**。 （如果執行 SQL Server 代理程式，就必須也重新啟動。）

    ![重新啟動][9]

7.  在 [ **SQL Server Management Studio** ] 對話方塊中，按一下 [ **]**來符合您想要重新啟動 SQL Server。

##<a name="Logins"></a>建立 SQL Server 驗證登入

若要從另一部電腦連線至資料庫引擎，您必須建立至少有一個 SQL Server 驗證登入。  

您可能會以程式設計方式建立新的 SQL Server 登入，或使用 SQL Server Management Studio。 若要以程式設計方式建立新的系統管理員使用者使用 SQL 驗證，開始**新的查詢**，然後執行下列指令碼。 取代 < 新的使用者名稱\>和 < 新密碼\>與您所選擇的*使用者名稱*和*密碼*。 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


（範例程式碼會關閉原則檢查和密碼過期） 視需要調整密碼原則。 如需有關 SQL Server 登入的詳細資訊，請參閱[建立登入](http://msdn.microsoft.com/library/aa337562.aspx)。  

若要建立新的 SQL Server 登入使用 SQL Server Management Studio 中︰

1.  在**SQL Server 管理 Studio 物件總管]**中，展開您要在其中建立新的登入伺服器執行個體的資料夾。

2.  以滑鼠右鍵按一下 [**安全性**] 資料夾，請指向 [**新增**]，然後選取**登入]**。

    ![新的登入][10]

3.  在 [**登入-新增**] 對話方塊中，在 [**一般**] 頁面中輸入新的使用者**登入名稱**] 方塊中的名稱。

4.  選取 [ **SQL Server 驗證**]。

5.  在 [**密碼**] 方塊中輸入新使用者的密碼。 **確認密碼**] 方塊中再次輸入密碼。

6.  若要強制複雜度和強制密碼原則選項，請選取 [**強制執行密碼原則**（建議使用）。 SQL Server 驗證為選取狀態時，這是預設的選項。

7.  若要強制密碼到期原則選項，請選取 [**強制密碼過期**（建議使用）]。 強制執行密碼原則必須選取 [啟用此核取方塊。 SQL Server 驗證為選取狀態時，這是預設的選項。

8.  若要強制使用者，以建立新密碼登入會使用第一次後，選取 [**使用者必須變更密碼在下次登入時**（建議使用如果此登入是讓其他人使用。 如果登入供自己使用，請勿選取這個選項。）強制執行密碼到期必須選取 [啟用此核取方塊。 SQL Server 驗證為選取狀態時，這是預設的選項。

9.  從 [**預設資料庫**] 清單中，選取 [預設資料庫登入。 **母片**是預設值，這個選項。 如果您尚未建立使用者資料庫，將此設定為**母片**。

10. 在 [**預設語言**] 清單中，將 [**預設**做為的值。

    ![登入屬性][11]

11. 如果這是您所建立的第一個登入，您可能會想要指定此登入以 SQL Server 系統管理員。 如果是這樣，在 [**伺服器角色**] 頁面上，核取**系統管理員**。

    > [AZURE.IMPORTANT] 系統管理員固定的伺服器角色的成員可完成控制資料庫引擎。 基於安全性理由，您應該謹慎限制此角色的成員資格。

    ![系統管理員][12]

12. 按一下 [確定]。

##<a name="DNS"></a>判定虛擬機器 DNS 的名稱

若要從另一部電腦連線至 SQL Server 資料庫引擎，您必須知道虛擬機器網域名稱系統 (DNS) 的名稱。

（這是網際網路用來識別虛擬機器中的名稱。 您可以使用的 IP 位址，但 Azure 移重複或進行的維修作業資源時，可能會變更的 IP 位址。 DNS 名稱就會穩定因為它可以重新導向至新的 IP 位址。）

1.  Azure 傳統入口網站 （或上一個步驟中） 中，選取 [**虛擬機器**。

2.  在**虛擬機器執行個體**] 頁面的 [ **DNS 名稱**] 欄中，找到並複製虛擬機器會出現前面**http://**加上的 DNS 名稱。 （使用者介面可能不會顯示完整的名稱，但您可以以滑鼠右鍵按一下它，並選取 [複製]）。

##<a name="cde"></a>從另一部電腦連線至資料庫引擎

1.  在電腦上連線到網際網路，開啟 [SQL Server Management Studio 中。

2.  **連線到伺服器**或**連線到資料庫引擎**] 對話方塊中，在 [**伺服器名稱**] 方塊中輸入虛擬機器 （決定中的上一個工作） 和公用端點連接埠中數字格式的*DNSName，portnumber*例如**tutorialtestVM.cloudapp.net,57500**DNS 的名稱。

3.  在 [**驗證**] 方塊中，選取 [ **SQL Server 驗證**]。

4.  在 [**登入**] 方塊中，輸入的登入您在舊版的任務建立的名稱。

5.  在 [**密碼**] 方塊中，輸入您在舊版的工作中建立的登入密碼。

6.  按一下 [**連線**]。

##<a name="amlconnect"></a>從 Azure 機器學習連線至資料庫引擎

稍後小組資料科學程序的階段，您會使用[Azure 電腦學習 Studio](https://studio.azureml.net)建立並部署電腦學習模型。 若要直接將 Azure 電腦學習訓練或計分內嵌您的 SQL Server VM 資料庫中的資料，使用 [**匯入資料**] 模組中新的[Azure 電腦學習 Studio](https://studio.azureml.net)實驗。 本主題討論小組資料科學程序的指南連結的更多詳細資料。 簡介資訊，請參閱[什麼是 Azure 電腦學習 Studio？](machine-learning-what-is-ml-studio.md)。

2.  在 [[匯入資料模組](https://msdn.microsoft.com/library/azure/dn905997.aspx)的 [**屬性**] 窗格中，選取 [從**資料來源**] 下拉式清單**Azure SQL 資料庫**。

3.  在 [**資料庫伺服器名稱**] 文字方塊中，輸入`tcp:<DNS name of your virtual machine>,1433`

4.  在**伺服器的使用者帳戶名稱**] 文字方塊中輸入 SQL 使用者名稱。

5.  在**伺服器的使用者帳戶密碼**] 文字方塊中輸入 sql 使用者的密碼。

    ![Azure 毫升匯入資料][13]

##<a name="shutdown"></a>關閉和解除配置在不使用時的虛擬機器

Azure 虛擬機器價格大於為**支付僅適用於您的使用**。 若要確保，您不計費不使用您的虛擬機器時，其位於**已停止 (Deallocated)**狀態。

> [AZURE.NOTE] 關閉虛擬機器從 VM 已停止內 （使用 Windows power 選項），但仍會保留配置。 若要確保您不計費，隨時停止虛擬機器從[Azure 傳統入口網站](http://manage.windowsazure.com/)。 您也可以停止透過 Powershell VM 呼叫 ShutdownRoleOperation 與 「 PostShutdownAction 「 等於 」 StoppedDeallocated 」。

關閉和解除配置虛擬機器︰

1. [Azure 傳統入口網站](http://manage.windowsazure.com/)使用您的帳戶登入。  

2. 從左側的導覽列中選取**虛擬機器**。

3. 在虛擬機器清單中，按一下您的虛擬機器中的名稱，然後移至**儀表板**頁面]。

4. 在頁面底部，按一下 [**關閉**]。

![VM 關閉][15]

虛擬機器可解除配置，但不是刪除。 您可以隨時從 Azure 傳統入口網站，以重新啟動您的虛擬機器。

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>您 Azure SQL Server VM 已可供使用︰ 下一步是什麼？

您的虛擬機器已經準備好在您的資料科學練習中使用。 虛擬機器也是供 Azure 電腦學習與小組資料科學程序 (TDSP) 作為 IPython 筆記本伺服器探索和處理資料，以及搭配其他任務。

資料科學程序中的下一個步驟對應[小組資料科學程序](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中，也可能包括步驟，將資料移到 HDInsight、 [處理程序和範例並準備資料學習 Azure 電腦學習。


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 
