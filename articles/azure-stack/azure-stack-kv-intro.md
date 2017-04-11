<properties
    pageTitle="Azure 堆疊鍵保存庫簡介 |Microsoft Azure"
    description="瞭解 Azure 堆疊鍵保存庫管理索引鍵和機密資料的方式"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Azure 堆疊中的關鍵保存庫簡介 #

## <a name="before-you-start"></a>在您開始之前

本文假設下列動作︰

- 讀取有權存取已啟用 Azure 金鑰保存庫的訂閱。
- Azure PowerShell SDK 是設定及使用。
- TP2 版本，才能執行所有的租用戶具作業僅從 PowerShell。

## <a name="key-vault-basics"></a>索引鍵保存庫基本概念

Azure 堆疊的金鑰保存庫可協助保護加密金鑰並使用雲端應用程式與服務的密碼。 您可以藉由使用金鑰保存庫，來加密金鑰和機密資料 （例如驗證鍵、 儲存帳戶金鑰、 資料加密金鑰、.pfx 檔和密碼）。

金鑰保存庫來簡化金鑰管理程序，並可讓您控制存取及加密資料的按鍵。 開發人員可以建立開發和測試以分鐘為單位的按鍵，然後流暢地移轉到產品金鑰。 安全性系統管理員可以授與 （和撤銷） 鍵，視需要的權限。

Azure 堆疊訂閱的任何人可以建立並使用金鑰保存庫。 雖然金鑰保存庫好處開發人員及其安全性系統管理員，但它可以實作並受管理組織其他 Azure 堆疊服務的系統管理員。 例如，此系統管理員可以使用訂閱登入 Azure 堆疊，建立的儲存索引鍵，然後進行以下操作的工作，組織保存庫︰

- 建立或匯入鍵或密碼
- 撤銷或刪除索引鍵或密碼
- 授權的使用者或應用程式存取金鑰保存庫，讓他們可以管理或使用其索引鍵和密碼
- 設定金鑰的使用狀況 （例如，簽章或加密）

此系統管理員可以提供開發人員應用程式中，從呼叫 Uri 然後安全性系統管理員提供使用記錄資訊。

開發人員也可以管理按鍵直接使用 Api。 如需詳細資訊，請參閱金鑰保存庫開發人員指南。

## <a name="scenarios"></a>案例

下表列出一些鍵保存庫可協助開發人員及其安全性系統管理員的需求的案例︰


### <a name="developer-for-an-azure-stack-application"></a>Azure 堆疊應用程式開發人員

**問題**︰ 我想要寫 Azure 堆疊的簽章及加密，使用索引鍵的應用程式，但我想這些是外部，從 [我的應用程式的如此一來解決方案適合的散布的應用程式。

**陳述式**︰ 索引鍵是儲存在保存庫和 URI 需要時叫用。


### <a name="developer-for-software-as-a-service-saas"></a>軟體開發人員為服務 (SaaS)

**問題︰**我不想責任的我的客戶租用戶索引鍵和密碼。

**陳述式︰**客戶可以將 Azure 堆疊匯入自己鍵，並加以管理。 我想要客戶擁有及管理其金鑰，讓我能專注於做什麼，其中會提供核心軟體功能。


### <a name="chief-security-officer-cso"></a>人力資源保全人員 (CSO)

**問題︰**我要確認我的組織是在主要的生命週期的控制項，且可以監視金鑰的使用方式。

**陳述式**索引鍵保存庫被設計，Microsoft 不會看到或擷取索引鍵。  應用程式需要執行加密作業使用客戶的按鍵，金鑰保存庫代表應用程式負責這項工作。 應用程式不會看到客戶的按鍵。  雖然我們使用多個 Azure 堆疊服務和資源，但我想要從 Azure 堆疊中的單一位置管理按鍵。 保存庫提供單一介面，不論多少保存庫中有哪些地區 Azure 堆疊它們，支援，以及哪些應用程式使用它們。

## <a name="next-steps"></a>後續步驟

[開始使用金鑰保存庫](azure-stack-kv-getting-started.md)
