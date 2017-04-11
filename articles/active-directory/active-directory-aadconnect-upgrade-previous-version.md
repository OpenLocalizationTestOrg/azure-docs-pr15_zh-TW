<properties
   pageTitle="Azure AD Connect: 從舊版升級 |Microsoft Azure"
   description="說明升級至最新版本的 Azure Active Directory 連線，包括就地升級和迴旋移轉的不同方法。"
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
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect︰ 從舊版本中的升級至最新
本主題即說明您可以使用您的 Azure AD Connect 安裝升級至最新版本的不同方法。 我們建議您保留自己目前的 Azure AD Connect 版本。 當您進行設定，獲得超乎變更，也會使用[迴旋移轉](#swing-migration)所述的步驟。

如果您想要從 DirSync 升級，請參閱[從 Azure AD 同步處理工具 (DirSync) 升級](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)改為。

有一些不同的策略升級 Azure AD Connect。

方法 | 描述
--- | ---
[自動升級](active-directory-aadconnect-feature-automatic-upgrade.md) | 使用快速安裝的客戶，這是最簡單的方法。
[就地升級](#in-place-upgrade) | 如果您有單一伺服器，升級安裝就地相同的伺服器上。
[迴旋移轉](#swing-migration) | 有兩個伺服器，您可以準備的其中一個新的版本或設定伺服器，並變更作用中的伺服器，當您準備好。

必要的權限，請參閱[升級所需的權限](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)。

## <a name="in-place-upgrade"></a>就地升級
從 Azure AD Sync 或 Azure AD Connect 移動適合就地升級。 運作 DirSync 或 FIM-A + Azure AD 連接器的解決方案。

這個方法是慣用的當您只有單一伺服器和小於大約 100000 物件。 如果有現成的同步處理規則的任何變更，請在升級後會發生的完整匯入和完整的同步處理。 如此一來，可確保新的設定套用至現有的所有物件，系統中。 這可能需要幾個小時的物件數目，根據範圍中的同步處理引擎。 標準 delta 同步處理排程器，每隔 30 分鐘，預設會暫停，但密碼同步處理持續。 您可以考慮執行就地升級期間週末。 如果不有現成的設定與新 Azure AD Connect 版本的任何變更，則會改為開始標準 delta 匯入同步。  
![就地升級](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

如果您有現成的同步處理規則進行變更，這些會回到設定在升級的預設設定。 若要確保升級之間保留您的設定，請確定所做的變更，[變更預設設定的最佳作法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)所述。

## <a name="swing-migration"></a>旋轉移轉
如果您有複雜的部署或很多個物件時，可能適合執行就地升級即時系統上。 這可能的一些客戶需要天，在這段時間沒有 delta 變更會處理。 當您計劃大幅變更您的設定，而且您想要試試看這些推入至雲端之前，也會使用這個方法。

這些案例的建議的方法是使用迴旋移轉。 您需要兩個 （至少） 伺服器、 一個為作用中和臨時伺服器。 動態伺服器 （在下圖中的實心藍色線） 負責作用中的生產負載。 暫存伺服器 （在下圖中的虛線紫色線） 已準備好使用新的版本或設定，並且完全準備好時，此伺服器由作用中。 上一個作用中的伺服器，現在使用的舊版本或安裝，設定所做的暫存的伺服器，而升級。

兩個伺服器可以使用不同版本。 例如，作用中的伺服器想要解除委任可以使用 Azure AD Sync，與新的臨時伺服器，可以使用 Azure AD Connect。 如果您使用迴旋移轉開發新的設定，最好的兩個伺服器上有相同的版本。  
![執行伺服器](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

附註︰ 它曾經某些客戶想要有三個或四個伺服器的這種情況。 當暫存的伺服器升級時，表示您沒有若[損毀修復](active-directory-aadconnectsync-operations.md#disaster-recovery)備份伺服器。 有三個或四個伺服器，一組的新版本的主要/備用伺服器可以準備，確保一律可接手暫存伺服器。

這些步驟也可以從 Azure AD Sync 或 FIM-A + Azure AD 連接器方案移動。 這些步驟無效的目錄同步，但 DirSync 的步驟相同迴旋 （也稱為平行部署） 的移轉方法，請參閱[升級 Azure Active Directory 同步處理 (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)。

### <a name="swing-migration-steps"></a>迴旋移轉步驟

1. 如果您在兩個伺服器上使用 Azure AD Connect，並且計劃只執行設定變更，請確定您使用中的伺服器和臨時伺服器兩者都使用相同的版本。 方便稍後比較的差異。 如果您從 Azure AD Sync 升級，這些伺服器會使用不同版本。 如果您要從較舊的 Azure AD Connect 版本升級，最好開始同一版本的兩個伺服器，但不是必要。
2. 如果您所做的一些自訂設定，且您暫存的伺服器不是它，請按照下[移動從暫存伺服器作用中的自訂設定](#move-custom-configuration-from-active-to-staging-server)的步驟。
3. 如果您從舊版 Azure AD Connect 升級，請為最新版本升級臨時伺服器。 如果您從 Azure AD Sync 移動，然後執行伺服器上安裝 Azure AD Connect。
4. 可讓您執行的伺服器上執行完整匯入及完整的同步處理，同步處理引擎。
5. 驗證的新的設定不會造成任何非預期的變更，使用 [**驗證**下的步驟驗證[伺服器的設定](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)。 如果內容為預期、 正確執行匯入及同步處理，並不驗證資料看起來不錯，直到。 可以連結的主題中找到這些步驟。
6. 切換為作用中的伺服器暫存的伺服器。 這是最後一個步驟，**切換作用中的伺服器**驗證[伺服器的設定](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)。
7. 如果您要升級 Azure AD Connect，升級的伺服器立即執行最新版本的模式。 若要取得資料與升級設定，請遵循與之前相同的步驟。 如果您從 Azure AD Sync 升級，您現在可以關閉，然後解除委任舊的伺服器。

### <a name="move-custom-configuration-from-active-to-staging-server"></a>將自訂設定從作用中移至暫存的伺服器
如果您已經設定變更作用中的伺服器，必須先確定相同的變更會套用至臨時伺服器。

建立自訂的同步處理規則可以使用 PowerShell 來移動。 其他變更必須套用兩個系統上相同的方式，並不會移轉。

項目，您必須確定已設定兩個伺服器上相同的方式︰

- 連線至相同樹系。
- 任何網域和 OU 篩選。
- 相同選用的功能，例如密碼同步處理及密碼回寫。

**移動同步處理規則**  
若要將自訂的同步處理規則，請執行下列動作︰

1. 開啟 [作用中的伺服器上的 [**同步處理規則編輯器**]。
2. 選取您的自訂規則。 按一下 [**匯出**]。 如此會開啟 [記事本] 視窗。 PS1 副檔名儲存暫存檔案。 這樣就 PowerShell 指令碼。 Ps1 將檔案複製到暫存的伺服器。  
![同步處理規則匯出](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. 連接器 GUID 臨時伺服器上的不同，而且必須變更。 若要取得 GUID、 啟動**同步處理規則編輯器**、 選取其中一個現成的規則，代表相同的連線的系統，按一下 [**匯出**]。 從暫存伺服器 GUID 取代 GUID PS1 檔案中。
4. 在 PowerShell 提示中，執行 PS1 檔案。 這會建立自訂的同步處理規則臨時伺服器上。
5. 如果您有多個自訂的規則，對所有的自訂規則。

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
