<properties
    pageTitle="快速入門 Azure 多重因素驗證提供者 |Microsoft Azure"
    description="瞭解如何建立 Azure 多重因素驗證提供者。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>快速入門 Azure 多重因素驗證提供者
使用預設的 Azure Active Directory 和 Office 365 使用者的全域管理員雙步驟驗證。 不過，如果您想要善用[進階的功能](multi-factor-authentication-whats-next.md)您應該購買完整版的 Azure 多重因素驗證 (MFA)。

> [AZURE.NOTE]  Azure 多重因素驗證提供者用來善用完整版的 Azure MFA 所提供的功能。 使用者的是誰**沒有透過 Azure MFA、 Azure AD 進階版或 EMS 的授權**。  Azure MFA、 Azure AD 進階版及 EMS 包括完整版的 Azure MFA 預設。  如果您擁有的授權，然後您不需要 Azure 多重因素驗證提供者。

Azure 多重因素驗證提供者，才能下載 SDK。

> [AZURE.IMPORTANT]  若要下載 SDK，請建立 Azure 多重因素驗證提供者，即使您有 Azure MFA、 AAD 進階版或 EMS 授權。  如果您建立此用途的 Azure 多重因素驗證提供者，並已授權，請務必使用**每個使用者**模型建立提供者。 然後連結至包含 Azure MFA、 Azure AD 進階版或 EMS 授權的目錄的提供者。  如此一來，可確保您會只付費如果您有多個唯一的使用者使用 SDK 超過您擁有的授權數。


## <a name="to-create-a-multi-factor-auth-provider"></a>若要建立多重因素驗證提供者

您可以使用下列步驟來建立 Azure 多重因素驗證提供者。

1. 以系統管理員身分登入[Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左側，選取 [ **Active Directory**]。
3. 在 [Active Directory] 頁面頂端，選取**多重因素驗證提供者**。
![建立可 MFA 提供者](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. 在底部，按一下 [**新增**。
![建立可 MFA 提供者](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. 在 [應用程式服務] 底下，選取 [**多重因素驗證提供者**
![建立 MFA 提供者](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. 選取 [**快速建立**]。
![建立可 MFA 提供者](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. 填寫下列欄位，然後選取 [**建立**]。
    1. **名稱**– 多重因素驗證提供者的名稱。
    2. **使用模型**是否要啟用個別的使用者，或支付每驗證。 選擇其中一個兩個選項︰
        - 每個驗證 – 購買費用每驗證的模型。 通常使用的消費者具應用程式中使用 Azure 多重因素驗證的案例。
        - 每個啟用使用者 – 購買每個收費的模型功能的使用者。 通常用於員工應用程式，例如 Office 365 的存取權。 如果您有一些已為 MFA Azure 授權的使用者，請選擇此選項。
    2. **目錄**– 多重因素驗證提供者相關聯的 Azure Active Directory 租用戶。 請注意下列任一動作︰
        - 您不需要建立多重因素驗證提供者的 Azure AD 目錄。 方塊保留空白，如果您只使用 Azure 多重因素驗證伺服器或 SDK 計劃。
        - 多重因素驗證提供者必須與 Azure AD 目錄，以善用進階功能相關聯。
        - Azure AD Connect、 AAD Sync 或 DirSync 是只要求，如果您與 Azure AD 目錄同步處理您的內部部署 Active Directory 環境。  如果您只使用一個未同步處理的 Azure AD 目錄，然後這不是必要。
![建立可 MFA 提供者](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. 一旦您按一下 [建立多重因素驗證提供者建立，您應該會看到訊息，說明︰**已順利建立多重因素驗證提供者**。 按一下 [**確定**]。
![建立可 MFA 提供者](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)
