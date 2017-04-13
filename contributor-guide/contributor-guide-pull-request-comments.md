# <a name="pull-request-comment-automation"></a>擷取要求註解自動化

我們使用註解自動化中擷取要求註解允許參與者並指派驅動提取要求的標籤作者檢閱程序。

| 註解 | 功能 | 顯示狀態|
| -------- |-------------|-------------|
|#登出功能 | 當文件的作者類型**#sign 關閉**註解中的註解資料流時，指派**準備至合併列印**標籤。 | 公用與私人|
|#登出功能 | 如果未列出的作者嘗試使用**#sign 關閉**註解的公用提取要求簽署者為參與者，郵件會寫入提取要求，指出標籤，可以指定僅由作者。 | 公用 |
|#保留關閉 | 如果您輸入**#hold 關閉**提取要求註解中，便會移除**準備至合併列印**標籤-您改變主意或發生錯誤。 在私人 repo，這會指派**合併不執行列印**標籤。 | 公用與私人 |
| #請關閉 | 作者可以將其關閉，如果您決定不要有變更合併提取要求的註解資料流中輸入**#please 關閉**註解。 | 公用 |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>疑難排解登有其專屬公用 repo 中

關閉自動化公用 repo 登是允許作者只登入。 可能需要一些手動的例外狀況的處理︰

- **文件作者**︰ 若要使用公用存放庫註解自動化，您實際 GitHub 帳戶必須確實符合文件中繼資料中所列的 GitHub 帳戶。 您的帳戶的大小寫的重要性。 如果您已封鎖來自此問題，因此登入，傳送郵件給 azdocprs 別名。

- **其他員工**︰ 如果您封鎖自動化的員工簽署代表作者，連絡 azdocprs 與提取要求] 連結。 指出誰後--上相同的產品小組的 PMs、 撰寫小組成員，及撰寫小組管理員的同事也會被視為受信任的來源。



## <a name="related"></a>相關

- [擷取要求禮儀及 Microsoft 參與者的最佳作法](contributor-guide-pull-request-etiquette.md)

- [品質準則提取要求檢閱](contributor-guide-pr-criteria.md)