
<properties
    pageTitle="疑難排解動態群組的成員資格 |Microsoft Azure"
    description="動態 Azure AD 中群組的成員資格的疑難排解秘訣。"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>疑難排解動態群組的成員資格

**我群組設定規則，但沒有成員資格] 群組中更新**<br/>請確認 [**啟用] 群組中的管理作業委派**] 設定設定為 [**是**，在 [**設定**] 索引標籤。 只有當您接收 Azure Active Directory 進階版授權指派的使用者身分登入，您會看到這項設定。 驗證規則的使用者屬性的值︰ 是否有符合規則的使用者？

**我要設定規則，但現在會移除規則的現有的成員**<br/>這是預期的行為。 當您啟用或變更規則時，會移除現有群組的成員。 傳回規則評估的使用者會新增為成員的群組。     

**我沒有看到成員資格變更時新增或變更為] 規則，為什麼未立即嗎？**<br/>專用的成員資格評估定期完成的非同步背景的程序。 多久程序會取決於您的目錄中的使用者數] 及 [群組建立規則的結果的大小。 一般而言，目錄小的數字的使用者會看到群組成員資格變更小於稍候幾分鐘。 使用大量使用者的目錄可能需要 30 分鐘或更長的時間，填入。

這些文章提供 Azure Active Directory 的其他資訊。

* [管理資源的存取權與 Azure Active Directory 群組](active-directory-manage-groups.md)
* [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
* [Azure Active Directory 是什麼？](active-directory-whatis.md)
* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
