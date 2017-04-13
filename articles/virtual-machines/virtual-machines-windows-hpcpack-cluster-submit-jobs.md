<properties
 pageTitle="送出工作 HPC 套件叢集 Azure 中 |Microsoft Azure"
 description="瞭解如何將內部部署電腦設定為提交到 HPC 套件叢集 Azure 中的工作"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>送出 HPC 工作從內部部署電腦 Azure 中部署 HPC 套件叢集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

設定內部部署的用戶端電腦提交到[Microsoft HPC 套件](https://technet.microsoft.com/library/cc514029)叢集 Azure 中的工作。 本文將說明如何設定叢集 Azure 中透過 HTTPS 提交工作的本機電腦上使用用戶端工具。 如此一來，多個叢集使用者可以提交雲端 HPC 套件叢集，但是不會直接連接到主節點 VM 或存取 Azure 訂閱的工作。

![提交到 Azure 工作][jobsubmit]

## <a name="prerequisites"></a>必要條件

* **Azure VM 部署 HPC 套件標頭節點**-我們建議您部署標頭節點和叢集使用自動化的工具，例如[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)或[Azure PowerShell 指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)。 您需要的主節點的 [DNS 名稱] 和 [叢集管理員認證以完成這份文件中的步驟。

* **用戶端電腦**-您需要執行 HPC 套件用戶端工具 （請參閱[系統需求](https://technet.microsoft.com/library/dn535781.aspx)） 的 Windows 或 Windows Server 的用戶端電腦。 如果您只想要使用 HPC 套件入口網站或 REST API 來提交工作，您可以使用您所選擇的任何用戶端電腦。

* 從[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=328024)使用**HPC 套件安裝媒體**-安裝的 HPC 套件用戶端工具 HPC 套件 (HPC 套件 2012 R2) 的最新版本的可用的安裝套件。 請確定您下載同一版本的標頭節點 VM 已安裝的 HPC 套件。

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>步驟 1︰ 在安裝及設定 web 元件主節點

若要啟用透過 HTTPS 提交到叢集工作的其餘部分介面，確保 HPC 套件 web 元件設定在 HPC 套件。 如果沒有安裝，第一次安裝 web 元件執行 HpcWebComponents.msi 安裝檔。 然後，設定元件執行 HPC PowerShell 指令碼**設定 HPCWebComponents.ps1**。

詳細的程序，請參閱[安裝 Microsoft HPC 套件 Web 元件](http://technet.microsoft.com/library/hh314627.aspx)。

>[AZURE.TIP] 特定的 Azure 快速入門範本 HPC 套件的安裝，並自動設定 web 元件。 如果您使用[HPC 套件 IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)來建立叢集時，您可以選擇性地安裝並設定 web 元件部署的一部分。

**安裝 web 元件**

1. 連線到主節點 VM 使用叢集管理員認證。

2. 從 [HPC 套件的安裝程式] 資料夾中，請在執行 HpcWebComponents.msi。

3. 依照安裝 web 元件精靈中的步驟

**若要設定 web 元件**

1. 在主節點，請以系統管理員身分開始 HPC PowerShell。

2. 若要變更目錄設定指令碼的位置，輸入以下命令︰

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. 若要設定其他介面，並啟動 HPC Web 服務，輸入以下命令︰

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. 當畫面提示您選取憑證，請選擇 [對應至公用 DNS 名稱的主節點的憑證]。 例如，如果您部署標頭節點 VM 使用傳統的部署模型的憑證名稱看起來像 CN =&lt;*HeadNodeDnsName*&gt;。 cloudapp.net。 如果您使用的資源管理員部署模型的憑證名稱看起來像 CN =&lt;*HeadNodeDnsName*&gt;。&lt;*地區*&gt;。 cloudapp.azure.com。

    >[AZURE.NOTE] 您選取這個憑證稍後從內部部署電腦主節點提交工作。 沒有選取，或設定憑證的對應到 Active Directory 網域中的主節點的電腦名稱 (例如，CN =*MyHPCHeadNode.HpcAzure.local*)。

5. 若要設定入口網站的工作送出，輸入以下命令︰

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. 指令碼完成後，請停止並重新啟動 HPC 工作排程器服務，輸入下列命令︰

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>步驟 2︰ 在內部部署電腦上安裝的 HPC 套件用戶端工具

如果您想要在您電腦上安裝的 HPC 套件用戶端工具，請從[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=328024)下載 HPC 套件的安裝檔案 （完整安裝）。 當您開始在安裝時，選擇 [**套件 HPC 用戶端工具**的 [設定] 選項。

若要使用 HPC 套件用戶端工具來提交到主節點 VM 的工作，您需要從主節點匯出憑證，並將其安裝於用戶端電腦。 憑證必須在。CER 格式。

**若要從主節點匯出憑證**

1. 在主節點，新增憑證嵌入式管理單元至本機電腦帳戶的 Microsoft 管理主控台。 若要新增嵌入式管理單元的步驟，請參閱[新增憑證嵌入式管理單元至 MMC](https://technet.microsoft.com/library/cc754431.aspx)。

2. 主控台樹狀目錄中，展開 [**本機電腦的憑證 –** > **個人**，然後按一下 [**憑證**。

3. 找出您設定的 HPC 套件 web 元件的憑證[步驟 1︰ 安裝及設定 web 元件在](#step-1:-install-and-configure-the-web-components-on-the-head-node)(例如，CN =&lt;*HeadNodeDnsName*&gt;。 cloudapp.net)。

4. 憑證，以滑鼠右鍵按一下，然後按一下 [**全部任務** > **匯出**。

5. 在憑證匯出精靈] 中，按一下 [**下一步**，，並確保已選取 [**否，不會匯出私密金鑰**。

6. 按照其餘步驟中 DER 編碼二進位 X.509 憑證匯出精靈] 的 (。CER) 格式。


**若要匯入的用戶端電腦上的憑證**


1. 複製您匯入至用戶端電腦上的資料夾從主節點的憑證。

2. 用戶端電腦上，執行 certmgr.msc。

3. 在 [認證管理員] 中，展開**憑證-目前使用者** > **受信任的根憑證授權單位**，以滑鼠右鍵按一下 [**憑證**]，然後按一下 [**所有工作** > **匯入**。

4. 在憑證匯入精靈中，按一下 [**下一步**，然後遵循步驟來匯入的憑證，您從主節點匯出至受信任的根憑證授權單位存放區。



>[AZURE.TIP] 您可能會看到安全性警告，因為用戶端電腦無法辨識的憑證授權單位，在。 供測試之用，您可以略過此警告，並完成憑證匯入。

## <a name="step-3-run-test-jobs-on-the-cluster"></a>步驟 3︰ 執行測試工作叢集上

若要驗證您的設定，請嘗試內部部署電腦上 Azure 中叢集上執行的工作。 例如，您可以使用 HPC 套件 GUI 工具] 或 [命令列命令提交到叢集的工作。 您也可以使用的 web 式入口網站提交工作。


**若要在用戶端電腦上執行工作送出] 命令**


1. 用戶端電腦上安裝的 HPC 套件用戶端工具，啟動 [命令提示字元。

2. 輸入 [範例] 命令。 例如，若要叢集上所有的工作] 清單中，輸入類似下列一個動作，根據主節點的完整 DNS 名稱命令︰

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    或
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] 使用完整 DNS 名稱的主節點，而不是 IP 位址，排程器 URL 中。 如果您指定的 IP 位址時，會出現錯誤類似 「 的伺服器憑證需要讓有效的信任鏈結或放置在受信任的根存放區 」。

3. 出現提示時，輸入使用者名稱 (在表單中&lt;DomainName&gt;\\&lt;使用者名稱&gt;) 和密碼 HPC 叢集系統管理員或另一個叢集使用者在您的設定。 您可以選擇儲存在本機的更多工作作業認證。

    工作清單隨即出現。


**若要在用戶端電腦上使用 HPC 工作管理員**

1. 如果您沒有先前儲存叢集使用者的網域認證，提交工作時，您可以新增認證管理員] 中的認證。

    。 在用戶端電腦上的 [控制台] 啟動 [認證管理員]。

    b。 按一下 [ **Windows 認證** > **新增一般認證**。

    c。 指定的網際網路位址 (例如，https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler 或 https://&lt;HeadNodeDnsName&gt;。&lt;地區&gt;.cloudapp.azure.com/HpcScheduler)，及使用者名稱 (&lt;DomainName&gt;\\&lt;使用者名稱&gt;) 和密碼叢集系統管理員或另一個叢集使用者在您的設定。

2. 用戶端電腦上，啟動 [HPC 工作管理員]。

3. 在 [**選取不對節點**] 對話方塊中，輸入 [Azure 中的主節點的 URL (例如，https://&lt;HeadNodeDnsName&gt;。 cloudapp.net 或 https://&lt;HeadNodeDnsName&gt;。&lt;地區&gt;。 cloudapp.azure.com)。

    HPC 工作管理員會開啟，並在顯示的工作清單。

**若要使用的標頭節點上執行的入口網站**

1. 開始在網頁瀏覽器的用戶端電腦上，然後輸入下列地址，根據主節點的完整 DNS 名稱︰

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    或
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. 在出現的 [安全性] 對話方塊中輸入 HPC 叢集系統管理員的網域認證。 （您也可以新增其他叢集使用者在不同的角色。 請參閱[管理叢集使用者](https://technet.microsoft.com/library/ff919335.aspx)）。

    入口網站會開啟 [工作清單] 檢視。

3. 若要提交會傳回字串"Hello World 」 的範例工作，按一下 [在左側導覽中的 [**新工作**]。

4. 在 [**新工作**] 頁面上**送出頁面**] 下的 [ **HelloWorld**。 [工作送出] 頁面隨即出現。

5. 按一下 [**送出**]。 如果出現提示，提供 HPC 叢集系統管理員的網域認證。 工作會送出，，作業識別碼會出現在 [**我的工作**] 頁面上。

6. 若要檢視您上傳的工作結果，請按一下工作識別]，然後按一下 [**檢視工作**檢視命令輸出 （在 [**輸出**）。

## <a name="next-steps"></a>後續步驟

* 您也可以送到 Azure 叢集[HPC 套件 REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)的工作。

* 如果您想要提交叢集工作從 Linux 用戶端，請參閱 Python 樣本[HPC 套件 2012 R2 SDK 和範例程式碼](https://www.microsoft.com/download/details.aspx?id=41633)。


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
