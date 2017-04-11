<properties
   pageTitle="連接器版本版本歷程記錄 |Microsoft Azure"
   description="本主題列出所有版本的連接線 Forefront 身分識別管理員 (FIM) 和 Microsoft 身分識別管理員 (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>連接器版本版本歷程記錄
經常更新 Forefront 身分識別管理員 (FIM) 與 Microsoft 身分識別管理員 (MIM) 的連接器。

>[AZURE.NOTE]
本主題位於 FIM-A 和 MIM 只。 Azure AD Connect 上不支援這些連接器。

本主題列出所有版本的已發行的連接器。

相關的連結︰

- [下載最新的連接器](http://go.microsoft.com/fwlink/?LinkId=717495)
- [一般 LDAP 連接器](active-directory-aadconnectsync-connector-genericldap.md)參考文件
- [一般的 SQL 連接器](active-directory-aadconnectsync-connector-genericsql.md)參考文件
- [Web 服務連接器](http://go.microsoft.com/fwlink/?LinkID=226245)參考文件
- [PowerShell 連接器](active-directory-aadconnectsync-connector-powershell.md)參考文件
- [Lotus Domino 連接器](active-directory-aadconnectsync-connector-domino.md)參考文件

## <a name="111170"></a>1.1.117.0
發行︰ 2016 年 3 月

**新的連接器**  
初始[一般 SQL 連接器](active-directory-aadconnectsync-connector-genericsql.md)的版本。

**新的功能︰**

- 一般 LDAP 連接器︰
    - 加入與 Isode delta 匯入的支援。
- Web 服務連接器︰
    - 更新 csEntryChangeResult 活動和 setImportErrorCode 活動，可讓物件層級回到同步處理引擎會傳回錯誤。
    - 更新 SAP6 和 SAP6User 範本使用新的 「 物件層級錯誤功能。
- Lotus Domino 連接器︰
    - 匯出]，您需要的通訊錄每一個發證者。 您現在可以輕鬆地管理使用所有 certifiers 相同的密碼。

**解決的問題︰**

- 一般 LDAP 連接器︰
    - IBM 役 ds，有些參照屬性偵測不正確。
    - 為 delta 匯入期間開啟 LDAP，被截斷的開頭和結尾的字串的空白字元。
    - Novell 和 NetIQ，移動物件，Ou/容器之間並同時匯出重新命名物件失敗。
- Web 服務連接器︰
    - 如果 web 服務的同一個繫結的多個結束點，然後連接器並未不正確探索這些結束點。
- Lotus Domino 連接器︰
    - 匯出到郵件中的資料庫的全名屬性無效。
    - 匯出新增，並從群組移除成員只會匯出新增的成員。
    - 如果備忘稿文件是不正確 (設定為 false 屬性 isValid)，然後連接器失敗。

## <a name="older-releases"></a>較舊的版本
2016 年 3 月之前連接線發行支援主題。

**一般 LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) 1.0.0597，2015 年 9 月
- [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549、 2015 年 3 月
- [KB3031009](https://support.microsoft.com/kb/3031009) 1.0.0534，2015 年 1 月
- [KB3008177](https://support.microsoft.com/kb/3008177) 1.0.0419，2014 年 9 月
- [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082、 2014 年 3 月

**Web 服務**

- [KB3008178](https://support.microsoft.com/kb/3008178) 1.0.0419，2014 年 9 月

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) 1.0.0419，2014 年 9 月

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) 1.0.0597，2015 年 9 月
- [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549、 2015 年 3 月
- [KB2977286](https://support.microsoft.com/kb/2977286) 5.3.0712，2014 年 8 月
- [KB2932635](https://support.microsoft.com/kb/2932635) 5.3.1003，2014 年 2 月  
- [KB2899874](https://support.microsoft.com/kb/2899874) 5.3.0721，2013 年 10 月
- [KB2875551](https://support.microsoft.com/kb/2875551) 5.3.0534，2013 年 8 月

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
