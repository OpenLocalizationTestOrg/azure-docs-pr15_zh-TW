以下是使用限制式與其他服務限制的 Azure Active Directory 服務。

| 類別 | 限制 |
|---|---|
| 目錄 | 單一使用者只能關聯 20 Azure Active Directory 目錄的最大值。<br />可能的組合的範例︰ <ul> <li>一位使用者建立 20 的目錄。</li><li>單一使用者加入 20 目錄中，為成員。</li><li>一位使用者建立 10 個目錄並稍後新增其他人到 10 個不同的目錄。</li></ul> |  
| 物件 | <ul><li>500000 物件的最大值可在單一目錄中的免費的 Azure Active Directory 版本的使用者。</li><li>非系統管理員使用者可以建立不超過 250 個物件。</li></ul> |
| 結構描述副檔名 | <ul><li>字串類型的副檔名可以有 256 個字元的最大值。 </li><li>二進位類型的副檔名限於 256 個位元組。</li><li>任何單一物件可寫入 100 名的副檔名值 （在所有類型及所有應用程式）。</li><li>只有 「 使用者 」 「 群組 」、 「 TenantDetail 」，「 裝置 」，」 應用程式 」 和 「 ServicePrincipal 「 實體可以延伸 」 字串 」 類型的 「 二進位 」 類型單一值的屬性。</li><li>結構描述的副檔名是只適用於圖表的 API 版本 1.21 預覽。 應用程式必須授與寫入存取權註冊副檔名。</li></ul> |
| 應用程式 | 10 個使用者最多可以是單一應用程式的擁有者。 |
| 群組 | <ul><li>10 個使用者最多可以是一個群組的擁有者。</li><li>任何數目的物件可以是單一 Azure Active Directory 中群組的成員。</li><li>您可以同步處理您的內部部署 Active Directory 從 Azure Active Directory 群組的成員數目為 15 K 成員，使用 Azure Active Directory 的目錄同步處理 (DirSync)。</li><li>您可以同步處理您的內部部署 Active directory 使用 Azure AD Connect 的 Azure Active Directory 群組的成員數目為 50 K 成員。</li></ul> |
| 存取面板 | <ul><li>沒有任何限制的應用程式，可以在每個使用者，Azure AD 進階版或企業版行動性套件指派授權的使用者存取畫面中看到的數目。</li><li>最大值 10 應用程式磚 (範例:] 方塊中，Salesforce 或 Dropbox 上) 可以存取畫面中看到的免費指派授權的使用者或 Azure AD 基本的版本，Azure Active Directory 的每位使用者。 這項限制不適用於系統管理員帳戶。</li></ul> |
| 報表 | 可以檢視或在任何報告中下載 1000 個資料列最大值。 任何其他資料會取至整數。 |
