<properties
    pageTitle="什麼 Microsoft Azure Active Directory 授權？ |Microsoft Azure"
    description="Microsoft Azure Active Directory 授權的描述、 其運作方式、 如何開始使用及最佳作法，包括 Office 365、 Microsoft Intune 和 Azure Active Directory 進階版及基本的版本"
    services="active-directory"
      keywords="Azure AD 授權"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>什麼 Microsoft Azure Active Directory 授權？

##<a name="description"></a>描述
Azure Active Directory (Azure AD) 做為 Microsoft 的身分識別的服務 (IDaaS) 解決方案與平台。 Azure AD 提供的功能和技術範圍是從 Azure AD 免費，使用任何 Microsoft 服務，例如 Office 365、 Dynamics、 Microsoft Intune 和 Azure 的版本號碼 （Azure AD 不會產生任何使用費用在此模式下），將 Azure AD 支付版本，例如企業版行動性套件 (EMS)、 Azure AD 進階版及基本，以及 Azure 多重因素驗證 (MFA)。 許多 Microsoft 線上服務，例如最 Azure AD 支付版本傳遞到每位使用者權利就和在 Office 365、 Microsoft Intune 和 Azure AD 中。 在這種情況下，服務購買表示有一或多個訂閱，每個訂閱您的租用戶中包含的測試購買授權數。 每位使用者權利是透過授權的指派，建立使用者和產品，讓使用者的服務元件和使用預付授權的其中一個之間的連結來達成。

[現在請嘗試 Azure AD 進階版。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Azure AD 管理入口網站是 Azure 傳統入口網站。 同時使用 Azure AD 不需要任何 Azure 購買，存取此入口網站需要作用中的 Azure 訂閱或[Azure 試用訂閱](https://azure.microsoft.com/pricing/free-trial/)。

Azure AD 服務功能的概觀，請參閱[什麼是 Azure AD](active-directory-whatis.md)。
[深入瞭解 Azure AD 服務層級](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Azure 支付另存新檔您執行的訂閱有不同︰ 時也表示您的目錄，這些訂閱啟用建立 Azure 資源，並將它們對應到您的付款方法。 在此情況下會與訂閱相關聯沒有授權數量。 授與他們操作對應至訂閱的 Azure 資源的權限來達成與訂閱，管理訂閱資源的使用者的存取權的使用者的關聯。


##<a name="how-does-azure-ad-licensing-work"></a>Azure AD 授權運作方式為何？

授權型 （權益型） 的 Azure AD 啟動 Azure AD 目錄/服務租用戶的訂閱服務的工作。 啟用訂閱後可以管理目錄/服務的系統管理員的服務功能，並授權的使用者使用。

當您購買或企業版行動性套件，Azure AD 進階版，或 Azure AD 基本啟動時，您的目錄會更新訂閱，包括其有效期間和預付的授權。 您訂閱的資訊，包括狀態、 下一步生命週期事件，以及指定或可用的授權數是透過 Azure 傳統的入口網站，在特定的目錄中的 [授權] 索引標籤。 這也是最佳的位置，來管理您的授權指派。

每個訂閱包含一個或多個服務方案，每個對應的服務類型; 包含功能層級例如，Azure AD Azure MFA、 Microsoft Intune、 Exchange Online 或 SharePoint Online。 Azure AD 授權管理不需要層級管理的服務方案。 這是不同的 Office 365 仰賴管理隨附的服務存取此進階的組態模式。 Azure AD 依賴的服務設定，若要啟用的功能及管理個別權限。

一般而言，Azure AD 訂閱資訊是透過 Azure 傳統入口網站中，針對特定的目錄 [授權] 索引標籤下的方式管理。 Azure AD 訂閱，Azure AD 進階版，但不顯示在 Office 入口網站中。

> [AZURE.IMPORTANT] Azure AD 進階版及基本，以及企業版行動性套件訂閱會受限於其能夠目錄/租用戶。 訂閱無法分割目錄之間，或用來贊成其他目錄中的使用者。 目錄之間移動訂閱時，有可能，但需要提交支援票證或取消及重新購買若是直接購買。

> 當購買 Azure AD 或企業版行動性套件，透過大量授權的訂閱啟動會發生這種情形自動合約時包含其他 Microsoft 線上服務，例如 Office 365。

支付 Azure AD 功能橫跨強大的目錄。 範例包括︰
- 群組指派應用程式]，在您要管理的特定應用程式啟用。
- 進階，且 [目錄設定] 下，或特定群組內使用此自助群組管理功能。
- 進階安全性報告位於 [報表] 索引標籤
- 雲端應用程式探索會出現在 [身分識別 Azure 入口網站中。

###<a name="assigning-licenses"></a>指派授權
取得訂閱是您所要設定付費的功能，使用 Azure AD 支付功能需要發送正確的個別使用者的授權。 一般而言，誰應有存取權，或透過 Azure AD 受管理的人員所支付功能任何使用者必須被指派授權。 授權的指派是使用者與付費的服務，例如 Azure AD 進階版、 基本、 或企業版行動性套件之間的對應。

管理您的目錄中的哪些使用者應有授權很簡單。 它可完成建立工作分派規則，透過 Azure AD 管理入口網站指派給群組，或直接對經由入口網站、 PowerShell 或 Api 正確的個別使用者指派授權。 指派授權給群組時，所有的群組成員會被指派授權。 如果您新增或移除群組中，它們會指派或移除適當授權的使用者。 群組工作分派可以利用您可以使用任何群組管理，以及與應用程式的 [群組依據工作分派的一致。 使用此方法，您可以設定規則，例如您的目錄中的所有使用者會自動被都指派、 確保適當的工作標題中的每個人都有授權或甚至代理人決定要在組織中其他管理員。

群組為基礎的授權指派] 中，使用任何遺漏的使用位置的使用者將期間工作分派繼承目錄的位置。 此位置可以隨時變更由系統管理員。 在位置自動化的作業失敗，因為錯誤的情況下，在該授權類型] 下的使用者資訊會反映的狀態。

##<a name="getting-started-with-azure-ad-licensing"></a>快速入門 Azure AD 授權

快速入門 Azure AD 很簡單。您隨時可以建立註冊免費的試用 Azure 至的目錄。 [更多深入瞭解如何為組織註冊](sign-up-organization.md)。 下列步驟可協助您確保您的目錄，最適合對齊其他 Microsoft 服務，您可能會分成或打算使用，與您取得該服務的目標。

以下是幾個最佳作法︰
- 如果您已使用任何 Microsoft 的組織的服務，您已經有 Azure AD 目錄。 在此情況下，您應該要繼續使用相同的目錄用於其他服務，讓核心身分識別管理，包括佈建和混合式 SSO，可以利用服務之間。 您的使用者會有單一登入體驗，並將因服務之間的更豐富的功能。 因此，如果您決定購買支付適用於您的工作人員服務 Azure AD，建議您使用相同的目錄，若要這麼做。
- 如果您打算使用 Azure AD 的一組不同的使用者 （合作夥伴、 客戶等），或如果您想要評估 Azure AD 服務，並且想要執行這項作業隔離的生產服務，或如果您正在尋找設定沙箱環境服務，我們建議您先建立新的目錄，透過 Azure Azure 傳統入口網站。 [深入瞭解建立新 Azure 傳統入口網站中的 Azure AD 目錄](active-directory-licensing-directory-independence.md)。 將會建立新的目錄，以防止為全域管理員權限與外部使用者使用您的帳戶。 當您使用此帳戶 Azure 傳統入口網站登入時，您可以看見此目錄並存取所有的目錄管理工作。 我們建議您在適當的權限管理其他 Microsoft 服務 （那些無法存取透過 Azure 傳統入口網站） 建立本機帳戶。 [深入瞭解建立 Azure AD 中的使用者帳戶](active-directory-create-users.md)。

> [AZURE.NOTE] Azure AD 支援 」 外部使用者，「 Azure AD 建立使用 Microsoft 帳戶 (MSA) 或從另一個目錄 Azure AD 身分識別的執行個體中的使用者帳戶。 雖然我們忙碌延伸到所有的 Microsoft 的組織服務，此功能目前這些帳戶中不支援部分服務的體驗。例如，Office 365 管理入口網站目前不支援這些使用者。 如此一來，與 Microsoft 帳戶的外部使用者將無法存取 Office 365 管理入口網站，而會忽略來自其他 Azure AD 目錄的外部使用者。 在第二個案例中，只有使用者的本機帳戶，Azure AD 或 Office 365 目錄的位置原先建立使用者，就會透過下列體驗。

如同，Azure AD 會有不同的付費的版本。 這些版本有其購買可用性有些微差異︰


| 產品   | EA/VL     | 開啟  |   CSP |   MPN 使用權利  |   直接購買 | 試用版 |
|---|---|---|---|---|---|---|
| 企業版行動性套件 |   X | X | X | X |  |      X |
| Azure AD 進階版  | X | X | X |   | X | X |
| Azure AD Basic    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>選取一或多個授權試用版
 在所有的情況下，您可以選取您要在 [授權] 索引標籤，在目錄中的特定試用啟動 Azure AD 進階版或企業版行動性套件的試用訂閱。 [試用版包含 100 授權的 30 天訂閱。

![Azure Active Directory 試用版授權方案](./media/active-directory-licensing-what-is/trial_plans.png)

![企業版行動性套件試用版授權方案](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![作用中的試用版授權方案](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>指派授權
啟用訂閱後，您應該將授權指派給自己，並重新整理瀏覽器，確保您會看到您所有的功能。 接下來就是將授權指派給需要存取或中所包含的使用者所支付 Azure AD 功能。 如上所述我們在 [指派授權] 中，執行此動作的最佳方式是找出 [代表所要的對象] 群組，並將其指派給授權。如此一來，新增或移除群組在其生命週期的使用者會指派給或移除授權。

若要指派給群組或個別使用者的授權，請選取您想要指派，然後按一下 [命令列中的 [**指派**的授權方案。

![作用中的試用版授權方案](./media/active-directory-licensing-what-is/assign_licenses.png)

一次在 [選取計劃的 [工作分派] 對話方塊中，您可以選取使用者，並將其新增至右側的 [**指派**] 欄。 您可以瀏覽使用者清單或搜尋特定的人使用尋找放大鏡右上方使用者格線。 若要指定群組，請從 [**顯示**] 功能表中選取 「 群組 」，，然後按一下 [重新整理所顯示的工作分派右側的 [檢查] 按鈕。

![將授權指派給群組](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

您現在可以搜尋或瀏覽群組，然後將他們新增到**指定**的資料行中，以相同的方式。 您可以使用這些指派使用者和群組在單一作業的組合。 若要完成的工作分派的程序，請按一下右下角的頁面中的 [檢查] 按鈕。

![授權工作分派的進度資訊](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

群組指派時，其成員在 30 分鐘內，但通常在 1 到 2 分鐘內繼承授權。

工作分派錯誤發生期間 Azure AD 授權的指派，但很少。 可能的工作分派錯誤受限於︰
- 工作分派衝突的使用者先前已指派授權與目前的授權不相容。 在此情況下，新的授權指派會要求移除的上一個項目。
- 超出可用的授權的指派] 群組中的使用者數目超過可用的授權，當使用者的工作分派的狀態會反映失敗，因為缺少的授權指派。

###<a name="view-assigned-licenses"></a>檢視指派的授權

[摘要] 檢視的已指派的授權，包括使用、 分派下, 一步訂閱週期事件會顯示在 [**授權**] 索引標籤。

![檢視已指派的授權數目](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

指定的使用者和群組的詳細的清單，包括工作分派的狀態和路徑 （直接或繼承自一或多個群組），就能使用瀏覽到授權計劃。

![授權指派的授權計劃的詳細資料顯示方式](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

移除授權，是很簡單為分派給他們。 如果直接指派給使用者，或針對指定的群組，您可以選取授權類型、 選取**移除**，將使用者或群組新增至 [移除] 清單中，並確認動作來移除授權。 或者，您可以開啟授權類型、 選取特定使用者或群組，或命令列上點選 [**移除**。 若要結束群組授權的使用者的繼承，只要從群組移除使用者。

###<a name="extending-trials"></a>延伸試用版

試用副檔名的客戶可為自助透過 Office 365 入口網站。 客戶管理員可以瀏覽至[Office 入口網站](https://portal.office.com/#Billing)（存取取決於 Office 入口網站的權限），然後選取您 Azure AD 進階版的試用版。 按一下 [**延長試用期**] 連結，然後依照指示操作。 您需要輸入信用卡，但不是會被收費。

![延伸授權試用 Office 入口網站中](./media/active-directory-licensing-what-is/extend_license_trial.png)

客戶也可以要求試用副檔名為所提交支援要求。 客戶管理員可以瀏覽至 Office 365 入口網站[支援頁面](http://aka.ms/extendAADtrial)（存取取決於 Office 支援頁面的權限）。 在此頁面上選取 [」 訂閱與試用版] 底下的功能及下徵狀的 「 試用版問題 」。 最後，請在各種情況下輸入資訊

![延伸使用支援要求授權試用版](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>後續步驟

現在您可能可以設定並使用一些 Azure AD 進階功能。

- [自助密碼重設](active-directory-manage-passwords.md)
- [自助群組管理](active-directory-accessmanagement-self-service-group-management.md)
- [Azure AD Connect 健康情況](active-directory-aadconnect-health.md)
- [群組指派的應用程式](active-directory-manage-groups.md)
- [Azure 多重因素驗證](../multi-factor-authentication/multi-factor-authentication.md)
- [直接購買 Azure AD 進階版授權](http://aka.ms/buyaadp)
