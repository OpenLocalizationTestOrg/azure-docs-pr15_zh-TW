大部分的時間驗證錯誤導致不正確或不一致的設定的設定。 以下是一些特定的建議檢查事項。

* 請確定您並未錯失 [**儲存**] 按鈕。 這通常是簡易待辦事項]，然後結果是，您會在入口網站頁面上尋找正確的值，但它們是尚未儲存 Azure 環境或 Azure AD 應用程式中。
* Azure 入口網站的**應用程式設定**刀中設定的設定，請確定正確的 API 應用程式或 web 應用程式，已選取時輸入的設定。  也請確定設定已輸入為**應用程式設定**] 和 [不**連接字串**，因此類似的兩個章節的格式。
* JavaScript 前端驗證，下載] 以確認資訊清單`oauth2AllowImplicitFlow`成功已變更為`true`。
* 確認您使用的是 HTTPS，無論您設定的 Url:

    * 在 [程式碼的專案
    * 在 [CORS
    * Azure 環境的應用程式設定為每個 API 應用程式和 web 應用程式中
    * Azure AD 應用程式設定。
    
    請注意，如果您是從入口網站複製 API 應用程式的 URL，其通常`http://`，您必須手動將其變更為`https://`。

* 請確定已成功部署程式碼中的任何變更。 例如，在多重專案解決方案可能是變更專案的程式碼，並不小心選擇其中一個其他人時要部署變更。
* 請確定您在瀏覽器中，不 HTTP Url 中移至 HTTPS Url。 根據預設，Visual Studio 建立發佈含 HTTP Url 的設定檔，這是什麼瀏覽器開啟後部署專案。
* JavaScript 前端驗證，請確定 CORS 已正確設定上 JavaScript 程式碼呼叫的 API 應用程式。 如果有相關問題是 CORS 相關的疑問，請嘗試 「 * 」 為允許的原點的 URL。 
* JavaScript 前端結尾，開啟您的瀏覽器開發人員工具主控台] 索引標籤以取得詳細的錯誤資訊，並檢查 HTTP 要求在網路上。 不過，您可能會誤導主控台錯誤訊息。 如果您收到訊息，指出 CORS 錯誤，驗證頁面時，可能是真正的問題。 您可以檢查是否這是執行驗證暫時暫時停用應用程式，即可大小寫。
* .NET API 應用程式，請確定您錯誤訊息中取得詳細資訊，盡可能設定[為 [關閉 customErrors 模式](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview)。
* .NET API 應用程式，啟動[遠端偵錯工作階段](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)中，並檢查變數傳遞給可使用 ADAL 取得承載者權杖，程式碼或檢查宣告針對預期的服務本金識別碼的程式碼的值 請注意，您的程式碼，可以尋找意外這種方式可以選擇從許多不同來源的設定值。 例如，如果您打錯`ida:ClientId`為`ida:ClientID`程式碼時 Azure 應用程式服務環境設定，可能會收到`ida:ClientId`Web.config 檔案，並忽略 Azure 應用程式服務設定從尋找的值。 
* 如果項目不在標準的 Internet Explorer 視窗中的現有記錄集可能干擾。嘗試 InPrivate 及嘗試 Chrome 或 Firefox。
