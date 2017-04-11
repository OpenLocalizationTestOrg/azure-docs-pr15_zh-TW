<properties
    pageTitle="群組原則] 和 [MDM 設定 |Microsoft Azure"
    description="提供有關群組原則] 和 [行動裝置資訊應在公司所擁有的裝置的管理 (MDM) 設定。 這些原則會套用至整個使用者的裝置。"
    services="active-directory"
    keywords="什麼是群組原則和企業版狀態漫遊、 企業狀態漫遊、 windows 雲端 MDM 設定"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>群組原則] 和 [MDM 設定

只在公司所擁有的裝置上使用這些群組原則和行動裝置管理 (MDM) 設定，因為這些原則會套用到整個使用者的裝置。 套用 MDM 原則停用 [個人設定同步處理，使用者擁有的裝置會產生負面影響該裝置使用。 此外，在裝置上的其他使用者帳戶也會影響原則。

想要管理漫遊個人 （未受管理） 裝置的企業可以啟用或停用漫遊，而不是使用群組原則] 或 [MDM 使用 Azure 入口網站
下表描述可使用的原則設定。

## <a name="mdm-settings"></a>設定 MDM
MDM 原則設定套用至 Windows 10 和 Windows 10 行動裝置。  Windows 10 行動裝置支援存在僅適用於 Microsoft 帳戶以漫遊透過使用者的 OneDrive 帳戶。  請參閱 「 裝置和結束點 」 一節，如哪些裝置可支援 Azure AD 基礎同步處理的詳細資訊。

| 名稱                               | 描述                                                          |
|------------------------------------|----------------------------------------------------------------------|
| 允許 Microsoft 帳戶的連線 | 允許使用者進行驗證在裝置上使用 Microsoft 帳戶 |
| 允許同步處理我的設定             | 可讓使用者漫遊 Windows 設定與應用程式的資料。停用此原則] 會停用同步處理，以及在行動裝置上的備份                  |

## <a name="group-policy-settings"></a>群組原則設定
群組原則設定套用至 Windows 10 裝置連接到 Active Directory 網域。 表格也包含舊版設定，會顯示管理同步設定，但是的無法運作的企業狀態漫遊 Windows 10 版會標 「 不要使用 「 中描述。

| 名稱                                | 描述 |
|-------------------------------------|-------------|
| 帳戶︰ 區塊 Microsoft 帳戶  |此原則設定防止使用者新增新的 Microsoft 帳戶，這台電腦上|
| 無法同步處理                         |防止使用者漫遊 Windows 設定和應用程式的資料|
| 無法同步處理個人化             |停用同步處理的佈景主題] 群組|
| 無法同步處理瀏覽器設定        |停用同步處理的 Internet Explorer 群組|
| 無法同步處理的密碼               |停用同步處理的密碼] 群組|
| 無法同步處理其他 Windows 設定  |停用同步處理的其他視窗設定] 群組|
| 無法同步處理桌面個人化 |不使用。沒有作用|
| 不在計量付費連線同步處理  |停用漫遊在計量付費連線]，例如行動電話 3g|
| 無法同步處理應用程式                    |不使用。沒有作用|
|無法同步處理應用程式設定             |停用漫遊的應用程式的資料|
|無法同步處理開始設定           |不使用。沒有作用|


## <a name="related-topics"></a>相關的主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [啟用企業漫遊 Azure Active Directory 中的狀態](active-directory-windows-enterprise-state-roaming-enable.md)
- [設定和資料漫遊常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
- [在 Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
