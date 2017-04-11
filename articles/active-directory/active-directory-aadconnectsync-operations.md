<properties
   pageTitle="Azure AD Connect 同步處理︰ 操作工作及考量 |Microsoft Azure"
   description="本主題說明 Azure AD Connect 同步處理，以及如何準備作業系統這個元件的操作工作。"
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
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure AD Connect 同步處理︰ 操作工作和考量
本主題的目標是描述 Azure AD Connect 同步操作工作。

## <a name="staging-mode"></a>臨時模式
臨時模式可以使用數種情況下，包括︰

-   高的可用性。
-   測試並部署新的設定變更。
-   介紹新的伺服器，然後解除委任舊]。

暫存模式中的伺服器，您可以對設定進行變更，並預覽變更，先伺服器作用中。 也可讓您執行完整匯入及完整的同步處理，以驗證之前到生產環境進行這些變更，都必須所有變更。

在安裝期間，您可以選取要**執行模式**中的伺服器。 這個動作會伺服器進行匯入及同步處理，但不會執行任何匯出。 暫存模式中未執行伺服器密碼同步處理或密碼回寫，即使您在安裝期間選取這些功能。 當您停用暫存模式時，伺服器就會啟動匯出、 啟用密碼同步處理，並啟用密碼回寫。

您仍然可以使用同步處理服務管理員強制匯出。

從 Active Directory 和 Azure AD 接收變更繼續暫存模式中的伺服器。 移到另一個伺服器職責永遠有最新變更，可以快速筆記的複本。 如果您對您的主要伺服器的設定變更，您必須負責臨時模式中的伺服器進行相同的變更。

對於您知道較舊版本的同步處理技術，由於伺服器有自己的 SQL 資料庫，是不同的暫存的模式。 此結構可讓暫存模式伺服器位於不同的資料中心。

### <a name="verify-the-configuration-of-a-server"></a>驗證伺服器的設定
若要套用此方法，請遵循下列步驟︰

1. [準備](#prepare)
2. [匯入並同步處理](#import-and-synchronize)
3. [驗證](#verify)
4. [切換作用中的伺服器](#switch-active-server)

#### <a name="prepare"></a>準備

1. 安裝 Azure AD Connect，選取 [**臨時模式**，然後取消選取 [安裝精靈中的最後一頁上的 [**開始同步處理**。 此模式下，可讓您手動執行同步處理引擎。
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. 符號關閉/登入，且從 [開始] 功能表上，選取 [**同步處理服務**。

#### <a name="import-and-synchronize"></a>匯入並同步處理

1. 選取 [**連接器**]，並選取第一個連接器類型**Active Directory 網域服務**使用。 按一下 [**執行**]，選取 [**完整匯入**及**[確定]**。 此類型的所有連接器，請執行這些步驟。
2. 選取連接器類型**Azure Active Directory (Microsoft)**。 按一下 [**執行**]，選取 [**完整匯入**及**[確定]**。
3. 請確定 [連接器] 索引標籤繼續保持選取狀態。 每個連接器類型**Active Directory 網域服務**中，按一下 [**執行**]，請選取**Delta 同步處理**， **[確定]**。
4. 選取連接器類型**Azure Active Directory (Microsoft)**。 按一下 [**執行**]，請選取**Delta 同步處理**， **[確定]**。

您現在接移匯出會變更為 Azure AD 和內部部署 AD （如果您使用的 Exchange 混合式部署）。 接下來的步驟可讓您要檢查有什麼是變更實際開始匯出至目錄之前。

#### <a name="verify"></a>驗證

1. 啟動命令提示字元，然後移至`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. 執行︰`csexport "Name of Connector" %temp%\export.xml /f:x`  
在同步處理服務找的連接器名稱。 有類似 「 contoso.com – AAD 」 的名稱為 Azure AD。
3. 執行︰`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. 您有 %temp%名為 export.csv，可以在 Microsoft Excel 中檢視中的檔案。 這個檔案包含要匯出的所有變更。
5. 資料或設定進行必要的變更，並執行下列步驟執行一次 （匯入及同步處理和驗證），直到應該要匯出的變更。

**了解 export.csv 檔案**

大部分是檔案的指。 若要了解內容一些縮寫︰

- OMODT – 物件修改類型。 指出是否新增、 更新或刪除物件層級的作業。
- AMODT – 屬性修改類型。 指出是否新增、 更新或刪除的屬性層級的作業。

多重值屬性值時，就會顯示不是每項變更。 僅限新增和移除的值的數目會顯示。

#### <a name="switch-active-server"></a>切換作用中的伺服器

1. 在目前使用中的伺服器上，關閉伺服器 (DirSync/FIM/Azure AD Sync)，它不會匯出至 Azure AD 或是在臨時模式 (Azure AD Connect) 進行設定。
2. 在**執行模式**的伺服器上執行安裝精靈，並停用 [**臨時模式**。
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>修復損毀
實作設計的一部分是規劃有損壞您同步處理伺服器會遺失該怎麼辦。 有不同的模型，以使用與使用哪一個取決於幾項因素包括︰

-   什麼是 Azure AD 當機期間無法無法變更物件的容錯？
-   如果您使用密碼同步處理時，是否使用者接受他們有 Azure AD 中使用舊密碼，以避免也會變更內部部署？
-   您是否有相依性在即時作業，例如密碼回寫？

根據您組織的原則，這些問題的答案，其中下列策略可以實作︰

-   重建需要時。
-   有多餘的備用伺服器，稱為**臨時模式**。
-   使用虛擬機器。

如果您不使用內建的 SQL Express 資料庫，然後，請參閱[SQL 高可用性](#sql-high-availability)] 區段中的色彩。

### <a name="rebuild-when-needed"></a>重建需要時
規劃伺服器重建需要時可可行的策略。 通常，安裝的同步處理引擎及執行幾個小時內，則可以完成的初始匯入及同步處理。 如果沒有在備用伺服器，則可以暫時使用網域控制站主控同步處理引擎。

同步處理引擎伺服器不會儲存物件的相關的任何狀態，因此資料庫可以重建 Active Directory 和 Azure AD 中的資料。 若要加入物件從內部部署與雲端使用**sourceAnchor**屬性。 如果您要重建現有物件內部部署與雲端伺服器，則同步處理引擎符合這些物件一起再次上重新安裝。 您需要的文件並儲存的項目會設定所做的變更到伺服器，例如篩選及同步處理的規則。 在您開始同步處理之前，必須重新套用這些自訂的設定。

### <a name="have-a-spare-standby-server---staging-mode"></a>有多餘的備用伺服器-臨時模式
如果您有更複雜的環境，然後有一或多個備用伺服器會建議。 在安裝期間，您可以啟用伺服器位於**臨時模式**。

如需詳細資訊，請參閱[臨時模式](#staging-mode)。

### <a name="use-virtual-machines"></a>使用虛擬機器
常見與支援的方法是執行虛擬機器中的同步處理引擎。 主機有問題，以便與同步處理引擎伺服器圖像可以將移轉到另一個伺服器。

### <a name="sql-high-availability"></a>SQL 可用性
如果您不使用 SQL Server Express 隨附於 Azure AD Connect，然後高可用性的 SQL Server 也應該考量。 支援只高可用性解決方案是 SQL 叢集。 不支援的方案包含鏡像及永遠上。

## <a name="next-steps"></a>後續步驟

**概觀主題**  

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)  
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)  
