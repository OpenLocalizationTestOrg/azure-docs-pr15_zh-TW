<properties 
    pageTitle="Azure 多重因素驗證-其運作方式"
    description="Azure 多重因素驗證可協助保護存取資料和會議的簡單登入程序的使用者需求時的應用程式。 該要求的驗證第二個表單來提供額外的安全性，且能提供強式驗證透過一系列的簡單的驗證選項。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Azure 多重因素驗證的運作方式

多重因素驗證的安全性在於層疊在一起的方法。 破壞多個驗證因素攻擊會相當大的挑戰。 即使攻擊者若要瞭解使用者的密碼，則沒有用而不也需要擁有的信任的裝置。 使用者應會遺失該裝置，找到該人員都無法使用它，除非他或她也知道使用者的密碼。

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure 多重因素驗證可協助保護存取資料和會議的簡單登入程序的使用者需求時的應用程式。  該要求的驗證第二個表單來提供額外的安全性，且能提供強式驗證透過一系列的簡單的驗證選項︰

- 電話
- 文字訊息
- 行動應用程式通知，可讓使用者選擇方法他們想要
- 行動應用程式的驗證碼
- 第 3 廠商誓言權杖

如需詳細資訊喔運作方式請參閱以下的影片。

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>多重因素驗證可用的方法
當使用者登入時，其他驗證會傳送給使用者。  以下是可用於此第二個驗證的方法的清單。

驗證方法  | 描述
------------- | ------------- |
電話 | 要求，驗證他們的登入按下 # 註冊的使用者的智慧型手機放通話。  這樣就完成驗證程序。  此選項可設定，可以變更為您指定的程式碼。
文字訊息 | 文字訊息便會傳送至使用者的智慧型手機上使用 6 位數代碼。  輸入這個程式碼的完成驗證程序。
行動應用程式通知 | 驗證將會傳送要求給使用者的智慧型手機要求他們完成驗證選取驗證從行動應用程式。 如果您選取作為您主要的驗證方法的應用程式通知，會發生這項目。  如果他們收到這不登入時，，選擇報告這網路詐騙。
使用行動應用程式的驗證碼 | 驗證碼便會傳送到行動應用程式使用者的智慧型手機上執行。  如果您選取作為您主要的驗證方法的驗證碼，會發生這項目。


##<a name="available-versions-of-azure-multi-factor-authentication"></a>可用版本的 Azure 多重因素驗證
使用三個不同版本 azure 多重因素驗證。  下表將說明每一種方式更多詳細資料。

版本  | 描述
------------- | ------------- |
Office 365 的多重因素驗證 | 這個版本獨佔搭配 Office 365 應用程式，並可從 Office 365 入口網站管理。 讓系統管理員可以立即協助保護 Office 365 資源使用多重因素驗證。  此版本隨附的 Office 365 訂閱。
Azure 系統管理員的多重因素驗證 | 多重因素驗證功能的 Office 365 的相同子集可免費 Azure 的所有管理員。 Azure 訂閱的每個系統管理帳戶現在可以讓此核心多重因素驗證功能以取得額外的保護。 想要存取 Azure 入口網站，以建立 VM，網站時，系統管理員管理儲存空間，讓行動服務或任何其他 Azure 服務可以新增多重因素驗證他們的管理員帳戶。
Azure 多重因素驗證 | Azure 多重因素驗證提供豐富的功能集。 <br><br>透過 Azure 管理入口網站、 報告進階和支援的其他設定選項提供範圍的內部部署與雲端應用程式。 Azure 多重因素驗證您可以購買為獨立的授權，並且會搭配 Azure Active Directory 進階版和企業版行動性套件內。 <br><br>它也能購買消耗為基礎的 Azure 訂閱中建立 Azure 多重因素驗證提供者。
##<a name="feature-comparison-of-versions"></a>版本的功能比較
下表下方提供 Azure 多重因素驗證各種版本中提供的功能清單。


功能  | 多重因素驗證 （包含在 Office 365 Sku） 的 Office 365|多重因素驗證 Azure 系統管理員 （隨附於 Azure 訂閱） | Azure 多重因素驗證 （包括中 Azure AD 進階版和企業版行動性套件）
------------- | :-------------: |:-------------: |:-------------: |
系統管理員可以保護 MFA 的帳戶| * | * （僅適用於 Azure 系統管理員帳戶）|*
第二個因素的行動應用程式|* | * | *
第二個因素的電話|* | * | *
第二個 factor 為簡訊|* | * | *
不支援 MFA 的用戶端應用程式密碼|* | * | *
管理員控制驗證方法| *| *| *
釘選模式| | | *
網路詐騙提醒| | | *
MFA 報表| | | *
一次略過| | | *
自訂的問候語的行動電話| | | *
自訂的行動電話的本機號碼| | | *
事件確認| | | *
信任的 IPs| | | *
暫停 MFA 記憶裝置 （公用預覽版本）| | | *
MFA SDK| | | *
MFA 使用 MFA 伺服器的內部部署應用程式| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>如何取得 Azure 多重因素驗證

如果您想要完整的功能，而非只所提供的 Office 365 使用者和 Azure 系統管理員所提供的 Azure 多重因素驗證，有幾個選項，將它︰

1.  購買 Azure 多重因素驗證授權，並將他們指派給使用者。
2.  購買擁有 Azure 多重因素驗證，例如 Azure Active Directory 進階版或企業版行動性套件中建立的授權，並將他們指派給使用者。
3.  建立 Azure 訂閱中 Azure 多重因素驗證提供者。 如果您還沒有 Azure 的訂閱，您可以註冊 Azure 試用訂閱。 轉換成一般訂閱試用版已到期之前，必須試用版訂閱。

使用有兩種使用模型使用的透過 Azure 訂閱計費 Azure 多重因素驗證提供者︰


- **每個使用者**。 通常適用於企業的想要啟用多因素驗證固定數定期需要驗證的員工。
- **每個驗證**。 通常適用於企業的想要啟用大型群組的外部使用者不常需要驗證的多重因素驗證。

定價詳細資料請參閱[Azure MFA 價格。](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

選擇每個授權數量或消耗模型最適合您的組織。   然後，以取得已啟動，請參閱[快速入門](multi-factor-authentication-get-started.md)
