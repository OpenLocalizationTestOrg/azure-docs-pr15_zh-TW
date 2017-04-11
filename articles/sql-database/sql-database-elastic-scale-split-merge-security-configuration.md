<properties 
    pageTitle="分割合併的安全性設定 |Microsoft Azure" 
    description="設定 x409 加密的憑證" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>分割合併的安全性設定  

若要使用的分割/合併服務，您必須正確設定的安全性。 服務是 Microsoft Azure SQL 資料庫的彈性的縮放功能。 如需詳細資訊，請參閱[彈性的小數位數分割及合併服務教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。

## <a name="configuring-certificates"></a>設定憑證

兩種方式設定憑證。 

1. [若要設定的 SSL 憑證](#To-Configure-the-SSL#Certificate)
2. [若要設定用戶端憑證](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>若要取得憑證

從公用憑證授權單位 (Ca)，或從[Windows 憑證服務](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx)，就可以取得憑證。 這些是以取得憑證慣用的方法。

如果無法使用這些選項，您可以產生**自我簽署的憑證**。
 
## <a name="tools-to-generate-certificates"></a>若要產生的憑證的工具

* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>若要執行的工具

* 從開發人員命令提示字元的視覺錄音室，請參閱[Visual Studio 命令提示字元](http://msdn.microsoft.com/library/ms229859.aspx) 

    如果安裝，請移至︰

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* 取得從 WDK [Windows 8.1︰ 下載套件及工具](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>若要設定的 SSL 憑證
加密通訊和項目會驗證伺服器需要 SSL 憑證。 選擇最適合下列三個案例的以及執行它的步驟︰

### <a name="create-a-new-self-signed-certificate"></a>建立新的自我簽署的憑證

1.    [建立自我簽署的憑證](#Create-a-Self-Signed-Certificate)
2.    [建立自我簽署 SSL 憑證 PFX 檔案](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [上傳至雲端服務的 SSL 憑證](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [更新服務設定檔中的 SSL 憑證](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [匯入 SSL 憑證授權單位](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>若要使用現有的憑證的憑證存放區
1. [匯出憑證存放區中的 SSL 憑證](#Export-SSL-Certificate-From-Certificate-Store)
2. [上傳至雲端服務的 SSL 憑證](#Upload-SSL-Certificate-to-Cloud-Service)
3. [更新服務設定檔中的 SSL 憑證](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>若要使用現有的憑證 PFX 檔案中

1. [上傳至雲端服務的 SSL 憑證](#Upload-SSL-Certificate-to-Cloud-Service)
2. [更新服務設定檔中的 SSL 憑證](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>若要設定用戶端憑證
用戶端憑證所需驗證服務的要求。 選擇最適合下列三個案例的以及執行它的步驟︰

### <a name="turn-off-client-certificates"></a>關閉 [用戶端憑證
1.    [關閉 [用戶端憑證驗證](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>發行新的用戶端自我簽署的憑證
1.    [建立自我簽署的憑證授權單位](#Create-a-Self-Signed-Certification-Authority)
2.    [上傳至雲端服務的憑證](#Upload-CA-Certificate-to-Cloud-Service)
3.    [更新服務設定檔中的憑證](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [問題用戶端憑證](#Issue-Client-Certificates)
5.    [建立 PFX 檔案的用戶端憑證](#Create-PFX-files-for-Client-Certificates)
6.    [匯入用戶端憑證](#Import-Client-Certificate)
7.    [複製用戶端憑證憑證碼](#Copy-Client-Certificate-Thumbprints)
8.    [在 [服務設定檔中設定允許的用戶端](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>使用現有的用戶端憑證
1.    [尋找 CA 公開金鑰](#Find-CA-Public Key)
2.    [上傳至雲端服務的憑證](#Upload-CA-certificate-to-cloud-service)
3.    [更新服務設定檔中的憑證](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [複製用戶端憑證憑證碼](#Copy-Client-Certificate-Thumbprints)
5.    [在 [服務設定檔中設定允許的用戶端](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [設定用戶端憑證撤銷檢查](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>允許的 IP 位址

您可以限制為特定的 IP 位址範圍存取服務端點。

## <a name="to-configure-encryption-for-the-store"></a>若要設定加密存放區

需要憑證來加密認證儲存於中繼資料儲存區。 選擇最適合下列三個案例的以及執行它的步驟︰

### <a name="use-a-new-self-signed-certificate"></a>使用新的自我簽署的憑證

1.     [建立自我簽署的憑證](#Create-a-Self-Signed-Certificate)
2.     [建立自我簽署加密憑證 PFX 檔案](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [上傳至雲端服務的加密憑證](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [在 [服務設定檔中的更新加密憑證](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>使用現有的憑證的憑證存放區

1.     [匯出加密憑證從憑證存放區](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [上傳至雲端服務的加密憑證](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [在 [服務設定檔中的更新加密憑證](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>在 [PFX 檔案中使用現有的憑證

1.     [上傳至雲端服務的加密憑證](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [在 [服務設定檔中的更新加密憑證](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>預設的設定

預設的設定拒絕所有存取 HTTP 端點。 這是建議的設定，因為這些端點要求可能會執行資料庫認證等機密資訊。
預設的設定可讓所有的存取權的 HTTPS 端點。 此設定可能進一步限制。

### <a name="changing-the-configuration"></a>變更設定

在中設定群組的存取控制規則套用至和端點**<EndpointAcls>**] 區段中的 [**服務設定檔**。

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

設定 access 控制項] 群組中的規則在<AccessControl name="">服務設定檔的區段。 

網路存取控制清單的文件說明格式。
例如，若要允許只 Ip 範圍 100.100.0.0 至 100.100.255.255 存取 HTTPS 端點，規則如下所示︰

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>拒絕的服務防止

有兩種不同的機制偵測並防止拒絕服務攻擊支援︰

*    限制的遠端主機每一個要求的數字 （預設為關閉）
*    限制存取每個遠端主機率 (在預設)

這些根據 IIS 中的動態 IP 安全中提供的功能。 當變更此設定請注意下列因素︰

* Proxy 和對遠端主機資訊的網路位址轉譯裝置的行為
* 網頁角色中的任何資源的每個要求會被視為 （例如載入指令碼、 圖像等）

## <a name="restricting-number-of-concurrent-accesses"></a>限制並行存取數量

設定此行為的是︰

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

變更 DynamicIpRestrictionDenyByConcurrentRequests 為 true，若要啟用此保護。

## <a name="restricting-rate-of-access"></a>限制存取的工資率

設定此行為的是︰

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>設定被拒絕的要求的回應

下列設定將拒絕邀請回應設定︰

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
請參閱動態 IP 安全性 IIS 中的文件，其他支援的值。

## <a name="operations-for-configuring-service-certificates"></a>設定服務的憑證的作業
本主題適用於 [僅限參考。 請遵循設定步驟所述︰

* 設定 SSL 憑證
* 設定用戶端憑證

## <a name="create-a-self-signed-certificate"></a>建立自我簽署的憑證
執行︰

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

若要自訂︰

*    -n 使用服務的 URL。 萬用字元 (「 CN = *.cloudapp.net 」) 和支援替代名稱 （「 CN=myservice1.cloudapp.net，CN=myservice2.cloudapp.net 」）。
*    使用憑證到期日-e 建立強式密碼，然後指定出現提示時。

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>建立自我簽署的 SSL 憑證 PFX 檔案

執行︰

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

輸入密碼，然後匯出憑證，這些選項︰
* 是的匯出私密金鑰
* 匯出所有的擴充的屬性

## <a name="export-ssl-certificate-from-certificate-store"></a>匯出憑證存放區中的 SSL 憑證

* 尋找憑證
* 按一下 [動作]-> [所有工作]-> [匯出...
* 匯出憑證。使用這些選項的 PFX 檔案︰
    * 是的匯出私密金鑰
    * 如果可以的話憑證路徑中包含所有憑證 * 匯出所有擴充的屬性

## <a name="upload-ssl-certificate-to-cloud-service"></a>上傳至雲端服務的 SSL 憑證

上傳與現有的憑證，或產生。SSL 金鑰組 PFX 檔案︰

* 輸入密碼保護的私人資訊

## <a name="update-ssl-certificate-in-service-configuration-file"></a>更新服務設定檔中的 SSL 憑證

上傳至雲端服務的憑證指紋更新指紋值的服務設定檔中的下列設定︰

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>匯入 SSL 憑證授權單位

遵循下列步驟，在所有帳戶/電腦與服務會通訊︰

* 按兩下。Windows 檔案總管中 CER 檔案
* 在 [憑證] 對話方塊中，按一下 [安裝憑證...
* 將受信任的根憑證授權單位儲存區匯入憑證

## <a name="turn-off-client-certificate-based-authentication"></a>關閉 [用戶端憑證驗證

僅限用戶端憑證驗證受支援，停用，會允許公開存取權的服務端點，除非其他機制位置 （例如 Microsoft Azure 虛擬網路）。

若要關閉此功能的服務設定檔案中 false 變更這些設定︰

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

然後，將 SSL 憑證相同指紋複製 CA 的憑證設定︰

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>建立自我簽署的憑證授權單位
執行下列步驟，以建立自我簽署的憑證以作為憑證授權單位︰

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

若要自訂

*    使用憑證到期日-e


## <a name="find-ca-public-key"></a>尋找 CA 公開金鑰

所有的用戶端憑證必須已受信任的服務的憑證授權單位發出。 尋找公開金鑰憑證授權單位發出用戶端要用來驗證，才能將它上傳至雲端服務的憑證。

如果無法使用公開金鑰檔案，則會匯出的憑證存放區︰

* 尋找憑證
    * 搜尋相同的憑證授權單位核發的用戶端憑證
* 按兩下 [憑證]。
* [憑證] 對話方塊中，選取 [憑證路徑] 索引標籤。
* 按兩下 CA 中的項目路徑。
* 記錄筆記的憑證內容。
* 關閉 [**憑證**] 對話方塊。
* 尋找憑證
    * 搜尋上文所述的憑證授權單位。
* 按一下 [動作]-> [所有工作]-> [匯出...
* 匯出憑證。CER 與這些選項︰
    * **否，不會匯出私密金鑰**
    * 如果可以的話憑證路徑中包含所有的憑證。
    * 匯出所有的擴充的屬性。

## <a name="upload-ca-certificate-to-cloud-service"></a>上傳至雲端服務的憑證

上傳與現有的憑證，或產生。CA 公開金鑰 CER 檔案。

## <a name="update-ca-certificate-in-service-configuration-file"></a>在 [服務設定檔中的更新 CA 憑證

上傳至雲端服務的憑證指紋更新指紋值的服務設定檔中的下列設定︰

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

具有相同指紋更新下列設定值︰

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>發行用戶端憑證

每個服務的存取權限應有發出 his/hers 獨佔使用用戶端憑證，然後應該選擇 his/hers 擁有強式密碼來保護私人索引鍵。 

在同一部電腦自我簽署的憑證產生及儲存位置，必須執行下列步驟︰

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

自訂︰

* 將這個憑證驗證用戶端的識別碼-n
* 使用憑證到期日-e
* MyID.pvk 和 MyID.cer 使用唯一的檔名，此用戶端憑證

這個命令會提示輸入密碼以建立，然後使用一次。 使用強式密碼。

## <a name="create-pfx-files-for-client-certificates"></a>建立 PFX 檔案的用戶端憑證

每個產生的用戶端憑證，請執行︰

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

自訂︰

    MyID.pvk and MyID.cer with the filename for the client certificate

輸入密碼，然後匯出憑證，這些選項︰

* 是的匯出私密金鑰
* 匯出所有的擴充的屬性
* 收件者所發行這個憑證的個別應該選擇匯出密碼

## <a name="import-client-certificate"></a>匯入用戶端憑證

用戶端憑證發行的每個人應該匯入通訊與服務，他會使用機器中的金鑰︰

* 按兩下。Windows 檔案總管中 PFX 檔案
* 將個人匯入憑證儲存至少這個選項︰
    * 包含核取所有擴充的屬性

## <a name="copy-client-certificate-thumbprints"></a>複製用戶端憑證憑證碼
每個對象發行的用戶端憑證必須遵循下列步驟，以取得的 his/hers 指紋便會新增至 [服務設定檔的憑證︰
* 執行 certmgr.exe
* 選取 [個人] 索引標籤
* 按兩下要用於驗證的用戶端憑證
* 在開啟的 [憑證] 對話方塊中，選取 [詳細資料] 索引標籤
* 請確認顯示會顯示所有
* 選取清單] 中名為指紋的欄位
* 複製指紋**刪除不可見的 Unicode 字元前面的第一位數**的值會刪除所有空格

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>在 [服務設定檔中設定允許用戶端

逗點分隔的用戶端憑證允許存取服務的憑證碼清單更新服務設定檔中的下列設定的值︰

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>設定用戶端憑證撤銷檢查

預設的設定不會檢查使用憑證授權單位的用戶端憑證撤銷狀態。 若要開啟檢查項目，如果憑證授權單位發出用戶端憑證的支援這類檢查，請變更下列設定的其中一個 X509RevocationMode 列舉中定義的值︰

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>建立自我簽署的加密憑證 PFX 檔案

加密憑證，請執行︰

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

自訂︰

    MyID.pvk and MyID.cer with the filename for the encryption certificate

輸入密碼，然後匯出憑證，這些選項︰
*    是的匯出私密金鑰
*    匯出所有的擴充的屬性
*    上傳至雲端服務的憑證時，您將需要密碼。

## <a name="export-encryption-certificate-from-certificate-store"></a>匯出加密憑證從憑證存放區

*    尋找憑證
*    按一下 [動作]-> [所有工作]-> [匯出...
*    匯出憑證。使用這些選項的 PFX 檔案︰ 
  *    是的匯出私密金鑰
  *    如果可以的話憑證路徑中包含所有憑證 
*    匯出所有的擴充的屬性

## <a name="upload-encryption-certificate-to-cloud-service"></a>上傳至雲端服務的加密憑證

上傳與現有的憑證，或產生。加密金鑰組 PFX 檔案︰

* 輸入密碼保護的私人資訊

## <a name="update-encryption-certificate-in-service-configuration-file"></a>在 [服務設定檔中的更新加密憑證

上傳至雲端服務的憑證指紋更新指紋值的服務設定檔中的下列設定︰

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>常見的憑證作業

* 設定 SSL 憑證
* 設定用戶端憑證

## <a name="find-certificate"></a>尋找憑證

請遵循下列步驟︰

1. 執行 mmc.exe。
2. 檔案]-> [新增/移除嵌入式管理單元...
3. 選取 [**憑證**]。
4. 按一下 [**新增**]。
5. 選擇憑證存放區位置。
6. 按一下 [**完成**]。
7. 按一下**[確定]**。
8. 展開 [**憑證**]。
9. 展開的憑證存放區節點。
10. 展開的憑證子節點。
11. 在清單中選取憑證。

## <a name="export-certificate"></a>匯出憑證
在**憑證匯出精靈]**中︰

1. 按一下 [**下一步**]。
2. 選取**[是]**，然後**匯出私密金鑰**。
3. 按一下 [**下一步**]。
4. 選取所要的輸出檔案格式。
5. 核取您要的選項。
6. 核取 [**密碼**]。
7. 輸入強式密碼並確認它。
8. 按一下 [**下一步**]。
9. 輸入或瀏覽檔案名稱儲存憑證的位置 (使用。PFX 分機）。
10. 按一下 [**下一步**]。
11. 按一下 [**完成**]。
12. 按一下**[確定]**。

## <a name="import-certificate"></a>匯入憑證

憑證匯入精靈] 中︰

1. 選取的儲存位置。

    * 選取**目前的使用者**，如果僅在目前的使用者執行的程序將存取服務
    * 選取**本機電腦**，如果在這台電腦中的其他處理程序會存取服務
2. 按一下 [**下一步**]。
3. 如果從檔案匯入]，確認檔案路徑。
4. 若要匯入。PFX 檔案︰
    1.     輸入密碼保護私密金鑰
    2.     選取 [匯入選項
5.     選取下列存放區中的 [位置] 憑證
6.     按一下 [**瀏覽**]。
7.     選取您要儲存區。
8.     按一下 [**完成**]。
       
    * 如果選擇信任的根憑證授權單位存放區，按一下**[是]**。
9.     按一下 [所有] 對話方塊視窗上的**[確定**]。

## <a name="upload-certificate"></a>上傳的憑證

在[Azure 入口網站](https://portal.azure.com/)

1. 選取**雲端服務**。
2. 選取的雲端服務。
3. 在頂端的功能表中，按一下 [**憑證**]。
4. 在 [底端列中，按一下 [**上傳**]。
5. 選取憑證檔案。
6. 如果是。PFX 檔案時，請輸入私密金鑰的密碼。
7. 完成之後，新的項目清單中，複製憑證指紋。

## <a name="other-security-considerations"></a>其他的安全性考量
 
使用 HTTPS 結束點時，這份文件中所述的 SSL 設定加密服務和用戶端之間的通訊。 重要的認證以存取資料庫後，通訊中所包含的其他潛在的機密資訊。 請注意︰ 服務仍然存在內部的狀態，包括其內部表格中您所提供的中繼資料儲存在您的 Microsoft Azure 訂閱中的 Microsoft Azure SQL 資料庫中的認證。 該資料庫已定義的下列設定服務設定檔中一部分 (。CSCFG 檔案）︰ 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

加密資料庫中儲存的認證。 不過，最佳做法，確定您的服務部署的 web 和工作者角色會保留最新狀態安全，兩者有權存取的中繼資料資料庫及用來加密及儲存的認證解密的憑證。 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
