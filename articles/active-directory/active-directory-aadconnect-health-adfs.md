
<properties
    pageTitle="使用 Azure AD 連線健康狀況與 AD FS |Microsoft Azure"
    description="這是 Azure AD 連線健康狀況] 頁面來監控您的內部部署的方式 AD FS 基礎結構。"
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>使用 Azure AD 連線健康狀況與 AD FS
下列文件是特定監視 AD FS 基礎結構 Azure AD 連線健康情況。 監控 Azure AD Connect （同步處理） Azure AD 連線健康情況的詳細資訊，請參閱[使用 Azure AD 連線狀況的同步處理](active-directory-aadconnect-health-sync.md)。 此外，如監控 Active Directory 網域服務 Azure AD 連線健康情況的詳細資訊，請參閱[使用 Azure AD 連線健康狀況與 AD DS](active-directory-aadconnect-health-adds.md)。

## <a name="alerts-for-ad-fs"></a>AD fs 的通知
Azure AD 連線狀況通知] 區段中，可讓您的作用中的通知清單。 每個通知包含相關資訊、 解決步驟和相關的文件的連結。

您可以按兩下作用中或解決通知，以開啟新的刀其他相關資訊，您可以採取以解決的通知及連結至相關文件的步驟。 您也可以解決過去的通知上檢視歷程記錄的資料。

![Azure AD Connect 狀況入口網站](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>AD FS 的使用狀況分析
Azure AD 連線狀況使用狀況分析分析同盟伺服器驗證的流量。 您可以按兩下使用狀況分析方塊中，以開啟 [使用狀況分析刀，它會顯示多個度量和群組。

>[AZURE.NOTE] 若要使用 AD FS 的使用狀況分析，您必須確定已啟用 [AD FS 稽核。 如需詳細資訊，請參閱[AD fs 啟用稽核](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs)。

![Azure AD Connect 狀況入口網站](./media/active-directory-aadconnect-health/report1.png)

若要選取其他指標，指定時間範圍，或若要變更的群組，請使用狀況分析圖表上按一下滑鼠右鍵，然後選取 [編輯圖表。 然後您可以指定的時間範圍、 選取不同的度量，然後變更群組。 您可以檢視的驗證流量根據不同的 「 指標 」，並群組每個公制使用下表所述的相關 「 群組依據 」 參數︰

| 公制 | 群組依據 | 哪些群組表示，以及為何會有幫助？ |
| ------ | -------- | -------------------------------------------- |
| 要求總計︰ 的總數處理同盟服務要求 | 所有 | 顯示不包含群組來要求總數的計數。 |
|  | 應用程式 | 群組依據目標信賴要求總數。 若要瞭解哪些應用程式所收到的總流量多少百分比這個群組適合。 |
|  | 伺服器 | 群組，處理要求伺服器上的總要求。 此群組對於載入分配的總流量。 |
|  | 工作地點加入 | 群組要求總數根據是否都來自加入工作地點的裝置 （已知）。 若要瞭解您的資源使用無法辨識的身分識別基礎結構的裝置來存取此群組適合。 |
|  | 驗證方法 | 群組依據驗證時所使用的驗證方法要求總數。 此群組對於會用於驗證的一般驗證方法。 以下是可能的驗證方法 <ol> <li>Windows 整合式的驗證 (Windows)</li> <li>表單架構驗證 （表單）</li> <li>SSO （單一登入）</li> <li>X509 憑證驗證 （憑證）</li> <br>如果同盟伺服器收到要求 SSO cookie，該要求會計 SSO （單一登入）。 在這種情況下，如果 cookie 有效，使用者無法要求您提供的認證，並取得順暢存取應用程式。 這是常見如果您有多個依賴的廠商所保護之同盟伺服器。 |
|  | 網路位置 | 群組中所有的要求，根據使用者的網路位置。 可以是 [內部網路或外部網路。 此群組會很有幫助的流量百分比來自與外部網路。 |
| 總失敗要求︰ 總數無法處理的同盟服務要求。 <br> （此公制是只提供的 Windows Server 2012 R2 AD FS）| 錯誤類型 | 會顯示根據預先定義的錯誤類型的錯誤的數目。 此群組對於常見的錯誤。 <ul><li>不正確的使用者名稱或密碼︰ 不正確的使用者名稱或密碼的錯誤。</li> <li>「 外部網路鎖定 」: 由於已鎖定，從外部網路的使用者傳來的已收到要求的失敗次數 </li><li> 「 過期密碼 」: 失敗，因為使用者登入過期的密碼。</li><li>「 停用帳戶 」: 失敗，因為使用者停用的帳戶的記錄。</li><li>「 裝置驗證 」: 失敗，因為使用者進行驗證使用的裝置驗證失敗。</li><li>「 使用者憑證驗證]: 失敗，因為由於無效的憑證驗證失敗的使用者。</li><li>「 MFA 」: 失敗，因為使用者無法使用多重因素驗證進行驗證。</li><li>「 其他認證 」: 「 發行授權 」: 失敗，因為授權失敗。</li><li>「 發佈委派 」: 發佈委派錯誤而失敗。</li><li>「 權杖接受 」: 失敗，因為 ADFS 拒絕從協力廠商身分識別提供者的 token。</li><li>「 通訊協定]: 通訊協定錯誤而失敗。</li><li>「 未知 」: 找出所有。 任何其他失敗不符合定義的類別。</li> |
|  | 伺服器 | 群組依據伺服器錯誤。 此群組對於錯誤通訊伺服器。 平均分配可能的伺服器處於錯誤狀態指示器。 |
|  | 網路位置 | 群組依據要求 （內部網路與外部網路） 的網路位置的錯誤。 此群組對於失敗的要求的類型。 |
|  | 應用程式 | 群組依據的目標應用程式 （信賴） 失敗。 此群組對於目標應用程式會看到錯誤的大部分的數字。 |
| 使用者計數︰ 平均數目重複使用系統中的使用者 | 所有 | 此公制提供使用者使用同盟 service，在所選的時間量的平均數目的計數。 使用者未分組。 <br>平均取決於已選取的時間量。 |
|  | 應用程式 | 群組依據的目標應用程式 （信賴） 使用者的平均數目。 此群組對於多少使用者使用的哪一個應用程式。 |


## <a name="performance-monitoring-for-ad-fs"></a>效能監視 AD fs
Azure AD 連線狀況效能監視提供指標監控的資訊。 選取 [監控] 方塊中，開啟新的刀指標的詳細資訊。


![Azure AD Connect 狀況入口網站](./media/active-directory-aadconnect-health/perf1.png)


選取刀頂端的 [篩選] 選項，您可以篩選伺服器，請參閱個別伺服器的指標。 若要變更度量，以滑鼠右鍵按一下下監控刀的監控圖表，然後選取 [編輯圖表。 從開啟的新刀，您可以從下拉式清單中選取 [其他指標然後指定檢視的效能資料的時間範圍。

## <a name="reports-for-ad-fs"></a>AD fs 的報表
Azure AD 連線狀況提供的活動和 AD FS 的效能相關報告。 這些報表協助掌握活動 AD FS 伺服器上的系統管理員。

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>前 50 個使用者的使用者名稱與密碼登入失敗

驗證失敗的要求 AD FS 伺服器上的常見原因是使用不正確的認證，也就是錯誤的使用者名稱或密碼要求。 通常是因為因為複雜的密碼、 忘記密碼或拼字錯誤的使用者。

但還有其他可能會導致非預期的數字的要求，例如處理您的 AD FS server 的理由︰ 應用程式的快取的使用者認證與認證過期或惡意使用者嘗試登入的一系列的已知的密碼的帳戶。 這兩個範例是有效的原因會導致在邀請中超載。

Azure AD 連線狀況的 ADFS 提供有關使用失敗的登入，因為不正確的使用者名稱或密碼的前 50 個使用者的報表。 此報表透過處理伺服器陣列中的所有 AD FS 伺服器所產生的稽核事件

![Azure AD Connect 狀況入口網站](./media/active-directory-aadconnect-health-adfs/report1a.png)

此報表內中，您可以輕鬆存取下列設備組件的資訊︰

- 總的 # 個失敗的要求與錯誤使用者名稱與密碼過去 30 天
- 每日不正確的使用者名稱與密碼登入失敗的使用者的平均數目。


按一下此部分會帶您到主報表刀提供其他詳細資料。 此刀包含趨勢的資訊可協助建立有關錯誤的使用者名稱與密碼要求的比較基準圖形。 此外，會提供的失敗次數的前 50 使用者清單。

該圖表會提供下列資訊︰

- 由於錯誤每日為基礎的使用者名稱/密碼登入失敗總數目。
- 無法登入每日為基礎的唯一使用者總數目。
- 用戶端 IP 位址的最後一個要求

![Azure AD Connect 狀況入口網站](./media/active-directory-aadconnect-health-adfs/report3a.png)

報表提供下列資訊︰

| 報表項目 | 描述
| ------ | -------- |
|使用者識別碼| 顯示所用的使用者識別碼。 此值是使用者輸入的內容，在某些情況下是錯誤的使用者識別碼使用。|
|失敗的次數| 顯示總 # 的失敗次數的特定的使用者識別碼。 資料表與最失敗次數以遞減順序排序。|
|上次失敗| 上次失敗發生時，會顯示的時間戳記。
|上次失敗 IP | 顯示從最新錯誤的要求的用戶端 IP 位址。|



>[AZURE.NOTE] 此報表會自動更新後的新資訊每兩個小時收集在該時間內。 如此一來，過去兩個小時內的登入可能不會包含在報表中。



## <a name="related-links"></a>相關的連結

* [Azure AD Connect 健康狀況](active-directory-aadconnect-health.md)
* [Azure AD Connect 狀況代理程式的安裝](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect 狀況作業](active-directory-aadconnect-health-operations.md)
* [使用同步處理 Azure AD 連線狀況](active-directory-aadconnect-health-sync.md)
* [使用 Azure AD 連線健康狀況與 AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect 狀況常見問題集](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect 狀況版本歷程記錄](active-directory-aadconnect-health-version-history.md)
