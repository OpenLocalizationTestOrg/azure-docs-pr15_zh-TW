<properties
    pageTitle="Azure AD Connect 同步處理︰ 技術概念 |Microsoft Azure"
    description="說明 Azure AD Connect 同步處理的技術概念。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect 同步處理︰ 技術的概念
本文是[瞭解架構](active-directory-aadconnectsync-technical-concepts.md)的主題摘要。

Azure AD Connect 同步處理根據實心中繼目錄同步處理的平台。
下列各節介紹中繼目錄同步處理的概念。
Azure Active Directory 同步處理服務建置 MIIS、 ILM，和 FIM，提供連線到資料來源、 同步處理資料之間的資料來源，以及佈建和取消提供的身分識別的下一個平台。

![技術的概念](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

下列各節提供關於 FIM 同步處理服務的下列各方面的更多詳細資料︰

- 連接器
- 屬性流程
- 連接器空間
- Metaverse
- 佈建

## <a name="connector"></a>連接器

用來與連線目錄的程式碼模組稱為連接器 （先前稱為管理代理程式 (MAs)）。

這些是 Azure AD Connect 同步處理的電腦上安裝。
連接線會提供讓使用遠端系統通訊協定，而不要依賴特殊代理程式的部署無代理程式的能力。 這表示降低的風險及部署時間，尤其是處理重要的應用程式和系統。

在上方的圖片，連接器同義連接器空格，但是包含與外部系統的所有通訊。

連接器負責所有匯入的系統匯出功能，並開發人員不需要了解如何連線至每個系統原本即使用宣告式佈建自訂資料轉換時。

匯入和匯出時才會排程，以便進一步隔離系統中發生的之後變更執行不會自動傳送至連線的資料來源的變更。 此外，開發人員也可以建立自己連接器連接至幾乎任何資料來源。

## <a name="attribute-flow"></a>屬性流程

Metaverse 是鄰近連接器空格所有聯結的身分識別的合併的檢視。 在上圖中屬性流程描繪線加上輸入與輸出流程的箭頭。 屬性流程的複製，或從一個系統的資料到另一個程序，而所有屬性 （輸入或輸出） 的流量。

屬性流程當連接器空間和 metaverse 之間雙向同步處理 （完整或 delta） 作業排程要執行。

執行這些同步處理時，才會發生屬性流程。 屬性流都定義在同步處理規則。 這些可以輸入 (ISR 中上方的圖片) 或外寄 (OSR 上方的圖片中)。

## <a name="connected-system"></a>連線的系統

連線的系統 （又稱連線目錄） 是指的遠端系統同步處理連線到 Azure AD Connect 及讀取和寫入身分識別的資料，與。

## <a name="connector-space"></a>連接器空間

每個連線的資料來源的物件和屬性連接器空間中的篩選子集以表示。
這可以讓本機操作，而不需要使用同步處理物件時，請連絡遠端系統同步處理服務限制匯入的互動，並只會匯出。

當資料來源和連接器有提供變更 （delta 匯入） 的清單時，然後作業的效率隨著大幅只會變更後的最後一個投票循環交換。 連接器空間隔離變更傳播藉由要求連接器排程匯入和匯出的自動連接的資料來源。 此新增的保險授與您的想法和談測試、 預覽，或確認在下次更新時。

## <a name="metaverse"></a>Metaverse

Metaverse 是鄰近連接器空格所有聯結的身分識別的合併的檢視。

當身分識別連結在一起，並授權指派的匯入流程對應到不同的屬性，多個系統的 [彙總的資訊會開始中央 metaverse 物件。 從這個物件屬性流程，對應在內的輸出系統的資訊。

授權的系統這些 metaverse 到專案時，會建立物件。 一經移除所有連線，則會刪除 metaverse 物件。

無法直接編輯斷的物件。 物件中的所有資料都必須透過屬性流程會都提供。 Metaverse 維護常設連接器與每個連接器空間。 這些連接器並不需要重新評估為執行每次同步處理。 這表示 Azure AD Connect 同步處理不需要每次找出相符的遠端物件。 避免需要使用高代理程式，以防止變更通常可以負責相互關聯之物件的屬性。

當您發現新的資料來源，可能必須已經存在需要管理的物件，Azure AD Connect 同步處理會使用稱為加入規則程序，評估潛在的對象，用來建立的連結。
連結建立之後，此評估不再和標準屬性流程發生遠端連接的資料來源與 metaverse 之間。

## <a name="provisioning"></a>佈建

授權來源專案時可以建立新的物件，將新的連接器空間物件 metaverse 另一個連接器表示下游連線的資料來源中。

此原本就會建立連結]，然後屬性流程可以繼續雙向。

每當規則會決定，必須建立新的連接器空間物件，稱為佈建。 不過，因為這項作業，才會發生的連接器空間內，它不會不延續到連接的資料來源執行匯出之前。

## <a name="additional-resources"></a>其他資源

* [Azure AD 連線同步處理︰ 自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
