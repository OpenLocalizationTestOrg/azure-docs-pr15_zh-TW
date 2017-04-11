<properties
    pageTitle="Azure AD Connect 狀況版本歷程記錄"
    description="這份文件描述 Azure AD 連線健康狀況和什麼已包含在這些發行版本。"
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
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect 狀況︰ 版本版本歷程記錄

Azure Active Directory 小組定期更新 Azure AD 連線健康狀況的新功能與功能。 本文將列出的版本和已發行的功能。

## <a name="october-2016"></a>2016 年 10 月
**代理程式的更新︰**
- Azure AD 連線狀況代理程式 AD fs\(版本 2.6.408.0\)
    1. 在 [驗證要求偵測用戶端 IP 位址的改良功能
    2. 修正錯誤相關的警示
- Azure AD 連線狀況代理程式 AD ds （版本 2.6.408.0）
    1. 通知的相關錯誤修正。
- Azure AD 連線狀況代理程式的同步處理 （版本 2.6.353.0） 與 Azure AD Connect 版本 1.1.281.0 發行
    1. 提供同步處理錯誤報告所需的資料
    2. 通知的相關錯誤修正

**新的預覽功能︰**
- 同步處理錯誤報告的 Azure AD 連線

**新的功能︰**
- Azure AD 連線狀況 AD fs-IP 位址] 欄位中會有不正確的使用者名稱與密碼的前 50 個使用者的相關報告。

## <a name="july-2016"></a>2016 年 7 月

**新的預覽功能︰**

- [Azure AD Connect AD ds 的狀況](active-directory-aadconnect-health-adds.md)。


## <a name="january-2016"></a>2016 年 1 月


**代理程式的更新︰**

- Azure AD 連線狀況代理程式 AD fs （版本 2.6.91.1512）


**新的功能︰**

- [測試連線工具 Azure AD 連線健康情況代理程式](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>2015 年 11 月


**新的功能︰**

- 支援[角色存取控制](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**新的預覽功能︰**

- [Azure AD 連線狀況的同步處理](active-directory-aadconnect-health-sync.md)。

**解決的問題︰**

- 代理程式進行之註冊時所看到的錯誤修正錯誤。

## <a name="september-2015"></a>2015 年 9 月

**新的功能︰**

- AD fs 錯誤的使用者名稱的密碼報表
- 若要設定未驗證 HTTP Proxy 的支援
- 若要設定伺服器核心代理程式的支援
- 通知 AD fs 的改良功能
- AD fs 連線和資料的增強功能 Azure AD 連線健康情況代理程式上傳。


**解決的問題︰**

- AD FS 錯誤類型，使用方式的深入見解修正錯誤。


## <a name="june-2015"></a>2015 年 6 月

**初始 AD FS 和 AD FS Proxy Azure AD 連線狀況的版本。**

**新的功能︰**

- 監視電子郵件通知與 AD FS 和 AD FS Proxy 伺服器的通知。
- 輕鬆存取 AD FS 拓撲與 AD FS 效能計數器中的模式。
- 依應用程式、 驗證方法等要求網路位置 AD FS 伺服器成功 token 邀請中的趨勢。
- 依應用程式錯誤類型等的 AD FS 伺服器上失敗要求的趨勢。
- 使用 Azure AD 全域管理員認證的簡單代理程式部署。  




## <a name="next-steps"></a>後續步驟
進一步瞭解[監控您的內部部署識別基礎結構及同步處理服務雲端中](active-directory-aadconnect-health.md)。
