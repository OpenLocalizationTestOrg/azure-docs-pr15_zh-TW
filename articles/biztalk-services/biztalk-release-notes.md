<properties
    pageTitle="Azure BizTalk 服務版本資訊 |Microsoft Azure BizTalk 服務"
    description="Azure Biztalk 列出的已知的問題" 
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="release-notes-for-azure-biztalk-services"></a>Azure BizTalk 服務的版本資訊

Microsoft Azure BizTalk 服務的版本資訊包含在這個版本中的已知的問題。

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>新功能的 BizTalk 服務 11 月更新
* 您可以 BizTalk Services 入口網站中啟用其餘加密。 請參閱[啟用加密 BizTalk Services 入口網站中的其餘部分](https://msdn.microsoft.com/library/azure/dn874052.aspx)。

## <a name="update-history"></a>更新歷程記錄

### <a name="october-update"></a>年 10 月更新

* 支援組織的帳戶︰  
 * **案例**︰ 註冊 BizTalk 服務部署使用 Microsoft 帳戶 (例如user@live.com)。 在這個案例中，只有 Microsoft 帳戶使用者可以管理 BizTalk 服務 Biztalk 入口網站。 無法使用組織帳戶。  
 * **案例**︰ 註冊 BizTalk 服務部署 Azure Active Directory 中使用組織帳戶 (例如user@fabrikam.com或user@contoso.com)。 在這個案例中，只有在相同組織中的 Azure Active Directory 使用者可以管理 BizTalk 服務 Biztalk 入口網站。 無法使用 Microsoft 帳戶。  
* 當您在 Azure 傳統入口網站中建立 BizTalk 服務時，您會自動註冊 BizTalk Services 入口網站中。
 * **案例**︰ 您登入 Azure 傳統的入口網站中，建立 BizTalk 服務，，然後選取 [**管理**] 的第一次。 Biztalk 入口網站開啟時，BizTalk 服務自動登錄，且已準備好您的部署。  
 請參閱[登錄及更新 BizTalk Services 入口網站上的 BizTalk 服務部署](https://msdn.microsoft.com/library/azure/hh689837.aspx)。  

### <a name="august-14-update"></a>14 年 8 月更新
* 合約和橋耦合 – 交易合作夥伴合約和橋現在分離在 BizTalk Services 入口網站中。 您現在建立合約和橋分開，並在執行階段橋解析為協議根據 EDI 訊息中的值。 請參閱[建立 Azure BizTalk 服務合約](https://msdn.microsoft.com/library/azure/hh689908.aspx)、[建立使用 BizTalk Services 入口網站 EDI 橋](https://msdn.microsoft.com/library/azure/dn793986.aspx)、[建立使用 BizTalk Services 入口網站 AS2 橋](https://msdn.microsoft.com/library/azure/dn793993.aspx)、 和[橋如何解決在執行階段合約？](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* 若要建立範本合約選項會停用。  
* 傳送端合約，您現在可以指定每個結構描述的不同的分隔符號集。 此設定會傳送側合約的通訊協定設定] 底下所指定的。 如需詳細資訊，請參閱[建立並 X12 中 Azure BizTalk 服務合約](https://msdn.microsoft.com/library/azure/hh689847.aspx)並[建立中 Azure Biztalk EDIFACT 合約](https://msdn.microsoft.com/library/azure/dn606267.aspx)。 針對相同的目的，兩個新的項目也會新增至 TPM OM API。 請參閱[X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx)和[EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx)。  
* 標準 XSD 建構，包括衍生的類型，現在支援。 請參閱[使用標準 XSD 建構您地圖](https://msdn.microsoft.com/library/azure/dn793987.aspx)，並[使用衍生類型中對應的案例與範例](https://msdn.microsoft.com/library/azure/dn793997.aspx)。  
* AS2 支援的訊息簽章的新麥克風演算法與新的加密演算法。 請參閱[建立中 Azure Biztalk AS2 合約](https://msdn.microsoft.com/library/azure/hh689890.aspx)。  
## <a name="know-issues"></a>瞭解問題

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>連線問題之後 BizTalk Services 入口網站更新

  如果您有時 BizTalk 服務升級為服務復原變更] 中，開啟 [BizTalk Services 入口網站，您可能面對 BizTalk Services 入口網站的連線發生問題。  
  因應措施，您可能會重新啟動瀏覽器、 刪除瀏覽器快取，或在私人模式中啟動入口網站。  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE 無法找出成品，如果您按一下 [錯誤] 或 [Biztalk 專案中的警告
安裝 Visual Studio 2012 更新 3 RC 1 可以修正這個問題。  

### <a name="custom-binding-project-reference"></a>自訂繫結 project 參考
請考慮下列情況使用 Biztalk 專案中的 Visual Studio 方案︰  
* 在相同的 Visual Studio 解決方案中，有 Biztalk 專案及自訂繫結專案。 BizTalk 服務專案具有此自訂繫結專案檔案的參考。
* BizTalk 服務專案具有自訂繫結/行為 DLL 的參考。

「 建置 」 方案 Visual Studio 中的順利。 然後您 」 重建 」 或者 「 清除 」 方案。 之後，當您重建或清除，就會發生下列錯誤︰  
  無法將檔案複製<Path to DLL>以 「 bin\Debug\FileName.dll 」。 程序無法存取檔案 'bin\Debug\FileName.dll'，因為它由其他處理程序。  

#### <a name="workaround"></a>因應措施
* 已安裝的[Visual Studio 2012 更新 3](https://www.microsoft.com/download/details.aspx?id=39305) ，如果您有下列兩個選項︰

  * 重新啟動 Visual Studio 中，或

  * 重新啟動解決方案。 然後，執行只建置解決方案。  

* 未安裝[Visual Studio 2012 更新 3](https://www.microsoft.com/download/details.aspx?id=39305) ，如果開啟工作管理員]、 按一下 [處理程序] 索引標籤，按一下 MSBuild.exe 程序，，然後按一下 [結束處理程序] 按鈕。  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>路由至 BasicHttpRelay 端點如果就不支援從橋及 BizTalk Services 入口網站無法列印的字元升階為 HTTP 標頭

如果您升級屬性的一部分使用非列印字元的郵件時，無法使用 BasicHttpRelay 繫結的轉送目的地路由這些郵件。 此外，升級的內容，可供使用追蹤的一部分是 URL 編碼的二進位大型物件及取消編碼的目的地。  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>即使未傳送非同步 MDN 選項非同步傳送 MDN  
如果您選取 [**傳送非同步 MDN**核取方塊，指定要傳送非同步 MDN 的 URL，請考慮這種情況-，然後取消核取 [**傳送非同步 MDN**核取方塊，MDN 仍會傳送至指定的 URL，還未選取 [傳送非同步 MDNs] 選項。  
因應措施，您必須先取消核取 [**傳送非同步 MDN**核取方塊清除指定的 URL，然後再部署 [AS2 合約。  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>除了有效的交換空白字元會導致空的郵件傳送到暫停端點  
如果有空白字元超出 iea 的區段區段，請解譯器會將它視為目前交換結尾，並查看下一組空白字元為下一封郵件。 由於這不是有效的交換，您可能會看到一個成功的訊息會傳送至路由目的地，然後一個空白的訊息會傳送暫停結束點。  
### <a name="tracking-in-biztalk-services-portal"></a>在 [BizTalk Services 入口網站中追蹤  
追蹤事件會擷取 EDI 郵件處理以及任何關聯性。 如果郵件無法外部通訊協定階段，則會顯示為成功追蹤。 在此情況下，請參閱錯誤詳細資料的 [**詳細資料**] 欄中**追蹤**底下的 [記錄] 區段。
X12 接收及傳送設定 ([建立並 X12 中 Azure BizTalk 服務合約](https://msdn.microsoft.com/library/azure/hh689847.aspx)) 提供通訊協定階段的資訊。  

### <a name="update-agreement"></a>更新合約  
BizTalk Services 入口網站可讓您修改的身分識別辨識協議是設定。 這可能會導致 inconsistence 屬性。 例如，沒有使用 ZZ:1234567 和 ZZ:7654321 辨識合約。 在 BizTalk Services 入口網站的設定檔設定]，您可以變更為 01:ChangedValue ZZ:1234567。 您開啟合約和 01:ChangedValue 會顯示，而不是 ZZ:1234567。
若要修改的身分識別的辨識符號，刪除合約、 合作夥伴設定檔中更新**身分識別**，然後重新建立合約。  
> AZURE。警告此問題會影響 X12 和 AS2。  

### <a name="as2-attachments"></a>AS2 附件  
附件的郵件中不支援的 AS2 傳送或接收。 具體來說，附件會予以忽略，為一般 AS2 訊息處理郵件本文中。  
### <a name="resources-remembering-path"></a>資源︰ 記住路徑  
**資源**時，[] 對話方塊視窗可能不記得先前用來將資源新增的路徑。 若要記得先前使用過的路徑，請嘗試將 BizTalk Services 入口網站的網站新增至 Internet Explorer 中的 [**信任的網站**。  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>如果您重新命名的橋實體名稱，並關閉專案即可，但不儲存變更，然後再開啟實體造成錯誤
請考慮下列順序情況︰  
* BizTalk 服務專案中新增橋 （例如 XML 單向橋）  

* 重新命名橋指定實體名稱屬性的值。 這會重新命名您指定的名稱相關聯的.bridgeconfig 檔案。  

* 關閉 （以關閉索引標籤的 [Visual Studio） 的.bcs 檔案，而不儲存所做的變更。  

* 從方案總管再次開啟.bcs 檔案。  
您會注意到相關聯的.bridgeconfig 檔案具有您指定的新名稱，而設計介面上的實體名稱仍舊的名稱。 如果您嘗試開啟 [橋設定]，請按兩下橋元件，您會收到下列錯誤︰  
  「<old name>「 實體的相關檔案'<old name>.bridgeconfig 」 不存在  
若要避免這種情況下，請確定您重新命名 BizTalk 服務專案中的項目後，儲存變更。  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>即使已經從 Visual Studio 專案排除成品 BizTalk 服務專案建置完成
考慮 BizTalk 服務專案的情況，您可在其中新增成品 （例如，XSD 檔案），包含的成品中橋設定 （例如，藉由指定其為邀請郵件類型），，然後再排除 Visual Studio 專案。 在這種情況下，建立專案會不提供任何錯誤，只要刪除的成品可用磁碟空間，其中包含 Visual Studio 專案中的相同位置。
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk 服務專案不會檢查結構描述可用性時設定橋
在 BizTalk 服務專案中，新增到專案的結構描述匯入另一個結構描述，如果 BizTalk 服務專案不會檢查是否匯入的結構描述會新增至專案。 如果您嘗試建立這類專案，未取得任何建置錯誤。
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>字元集 utf-8 一律是 XML 要求回覆橋回應訊息
這個版本中，從 XML 要求回覆橋回應訊息的字元集一定會設定為 utf-8。
### <a name="user-defined-datatypes"></a>使用者定義的資料類型
BizTalk 介面卡套件卡內 BizTalk 介面卡服務功能可以利用介面卡作業的使用者定義的資料。
使用時的使用者定義資料，將檔案複製 (.dll) 磁碟機︰ \Program Files\Microsoft BizTalk 介面卡 Service\BAServiceRuntime\bin\ 或至全域組件快取 (GAC) 主控 BizTalk 介面卡服務的伺服器上。 否則，用戶端上可能會發生下列錯誤︰  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] 建議使用 GACUtil.exe 全域的組件快取安裝檔案。 如何使用此工具和 Visual Studio 命令列選項 GACUtil.exe 的文件。  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>重新啟動 BizTalk 介面卡服務的網站
安裝**BizTalk 介面卡服務執行階段***建立* *BizTalk 介面卡服務**包含 IIS 網站* *BAService* *應用程式。**BAService** 應用程式在內部使用轉送繫結擴充至雲端的內部部署服務端點的範圍。 為服務裝載於內部部署，對應的轉送端點註冊服務匯流排只在內部部署服務啟動時。  

如果您停止並啟動應用程式時，自動啟動應用程式的設定不正確。 因此**BAService**停止時，您必須一律重新啟動**BizTalk 介面卡服務**網站改為。 不啟動或停止**BAService**應用程式。
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>特殊字元不應該用於地址和實體 LOB 元件名稱
您不應使用特殊字元的地址和實體 LOB 元件的名稱。 如果這麼做，您會收到錯誤時部署 BizTalk 服務專案。 針對特定字元 '%'，BizTalk 介面卡服務網站可能會進入停止狀態，則必須以手動方式啟動它。
### <a name="test-map-with-get-context-property"></a>測試取得內容屬性對應
如果轉換包含**取得內容屬性**對應作業，將會失敗**測試對應**。 暫時解決這個問題，請**取得內容屬性**對應作業取代字串串連地圖作業包含空的資料。 這會填入目標結構描述，讓您測試其他轉換功能。
### <a name="test-map-property-does-not-display"></a>測試對應屬性不會顯示
**測試對應**內容不會顯示在 Visual Studio 中。 如果不同時固定 [**屬性**] 視窗與 [**方案總管**] 視窗，會發生這項目。 若要解決這個問題，固定**屬性**和**方案總管]**視窗。  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>重新設定格式的日期時間] 下拉式清單會呈現灰色
當 DateTime 重新格式化地圖作業會新增到設計介面，並設定時，[格式] 下拉式清單可能會呈現灰色。 如果電腦的 [顯示設定**中 – 為 125%** ] 或 [ **Larger – 150%**，也可能會發生。 若要解決，請將顯示為**較 – 100%（預設）**使用以下步驟︰  
1. 開啟**[控制台**]，然後按一下 [**外觀及個人化**]。
2. 按一下 [**顯示**]。
3. 按一下**小 – 100%（預設值）** ，然後按一下 [**套用**]。

如預期般，現在應該可以使用 [**格式**] 下拉式清單。
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>在 [BizTalk Services 入口網站中的重複合約
請考慮下列情況︰
1. 建立使用交易的合作夥伴管理 OM API 合約。
2. 開啟合約 BizTalk Services 入口網站中兩個不同的索引標籤中。
3. 部署合約中兩個索引標籤。
4. 如此一來，兩個合約部署結果 BizTalk Services 入口網站中的重複項目

**因應措施**。 開啟任何一個重複合約的 BizTalk Services 入口網站中，然後解除部署。  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>即使已更新憑證市集中的成品橋不要使用更新的憑證
請考慮以下情況︰  

**情況 1︰ 使用指紋為基礎的憑證設定安全性訊息傳輸從橋服務端點**  
請考慮您使用指紋為基礎的憑證 BizTalk 服務專案中的案例。 您使用相同的名稱，但不同的指紋，更新 BizTalk Services 入口網站中的憑證，但不是會相應地更新 BizTalk 服務專案。 在這種情況下，可能會繼續橋處理郵件，因為頻道快取中可能仍到較舊的憑證資料。 之後，就會失敗訊息處理。  

**因應措施**︰ 更新 BizTalk 服務專案中的憑證，並重新部署專案。  

**案例 2︰ 使用名稱為基礎的行為來識別保護從橋服務端點的郵件傳輸的憑證**

請考慮案例中，使用名稱為基礎的行為來識別 BizTalk 服務專案中的憑證。 您更新 BizTalk Services 入口網站中的憑證，但不是會相應地更新 BizTalk 服務專案。 在這種情況下，可能會繼續橋處理郵件，因為頻道快取中可能仍到較舊的憑證資料。 之後，就會失敗訊息處理。  

**因應措施**︰ 更新 BizTalk 服務專案中的憑證，並重新部署專案。  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>橋繼續處理郵件，即使是 SQL 資料庫為離線
Biztalk 橋繼續程序的郵件時，即使 Microsoft Azure SQL 資料庫 （此儲存了執行的資訊，例如部署的成品和管線），為離線。 這是因為 BizTalk 服務使用的快取的成品和橋設定。
如果您不想橋處理任何訊息，SQL 資料庫離線時，您可以使用 BizTalk 服務 PowerShell cmdlet，以停止或暫停 BizTalk 服務。 請參閱[Azure BizTalk 服務管理範例](http://go.microsoft.com/fwlink/p/?LinkID=329019)的 Windows PowerShell cmdlet 來管理作業。  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>讀取 XML 訊息的橋樑自訂程式碼元件內包含一個額外的 BOM 字元
請考慮要讀取內的橋樑自訂程式碼的 XML 郵件的情況。 如果您是使用.NET API System.Text.Encoding.UTF8.GetString(bytes) 額外的 BOM 字元會包含在輸出中郵件的開頭。 因此，如果您不想要包含的額外的 BOM 字元的輸出，您必須使用```System.IO.StreamReader().ReadToEnd()```。
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>將郵件傳送給使用 WCF 橋不會縮放
使用 WCF 橋的郵件不會縮放。 如果您想要調整的用戶端，您應該改用 HttpWebRequest。
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>升級︰ 從 BizTalk 服務預覽一般可用性 (GA) 若要在升級後的權杖提供者錯誤
沒有 EDI 或 AS2 合約與作用中的批次。 當 BizTalk 服務升級從預覽 GA 時，可能會發生下列動作︰
* 錯誤︰ 權杖提供者無法提供安全性權杖。 傳回訊息的權杖提供者︰ 無法解析遠端名稱。

* 批次工作都會被取消。

**因應措施**︰ 之後 BizTalk 服務會更新以一般顯示狀態 (GA)、 重新部署授權合約。  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>升級︰ 工具箱顯示舊橋圖示升級 BizTalk 服務 SDK 之後
升級舊版 BizTalk 服務 SDK，有舊的圖示代表橋之後, 工具箱持續顯示橋舊的圖示。 不過，如果您新增橋 BizTalk 服務專案設計工具介面，介面會顯示新的圖示。  

**因應措施**。 您可以刪除下的.tbd 檔案以解決此問題<system drive>: \Users\<使用者 > \AppData\Local\Microsoft\VisualStudio\11.0。  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>升級︰ 從預覽 GA BizTalk 入口網站更新可能會顯示錯誤指出 EDI 功能無法使用
如果您登入 BizTalk Services 入口網站 BizTalk 服務從 Preview 升級為 GA 時，您可能會收到下列錯誤，在入口網站︰  

此功能無法使用此版本的 Microsoft Azure BizTalk 服務的一部分。 若要使用這些功能切換至適當的版本。  

**解析度**︰ 登出從入口網站、 關閉及開啟瀏覽器，然後登入入口網站。  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>升級︰ 新增追蹤資料不會顯示 BizTalk 服務升級為 GA 之後
假設您有部署 BizTalk 服務預覽訂閱 XML 橋情況。 您傳送郵件給橋，BizTalk Services 入口網站上使用對應的追蹤資料。 現在，如果 BizTalk Services 入口網站及 Biztalk runtime 位元升級至 GA，您將訊息傳送至相同的橋端點部署較舊版本，追蹤資料不會顯示在升級後所傳送的郵件。  

### <a name="pipelines-vs-bridges"></a>管線 v/s 橋
在此文件，會交換使用的字詞 '管線 」 及 「 橋 」。 同時基本上表示相同的動作，也就是，郵件處理單位部署 BizTalk 服務]。  

### <a name="concepts"></a>概念  

[BizTalk 服務](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
