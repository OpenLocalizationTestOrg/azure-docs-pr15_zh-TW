<properties 
    pageTitle="Windows 驗證和 Azure 多重因素驗證伺服器"
    description="這是 Azure 多重因素驗證] 頁面，能夠協助您部署 Windows 驗證和 Azure 多重因素驗證伺服器。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows 驗證和 Azure 多重因素驗證伺服器

[Windows 驗證] 區段中，可讓系統管理員啟用及設定 Windows 驗證的一或多個應用程式。  以下是要記住設定 Windows 驗證之前的項目清單。

-  不需要重新開機之前 Azure 多重因素驗證終端機服務就會生效。
-  如果 「 需要 Azure 多重因素驗證使用者須相符] 核取您不是在使用者清單中，您無法登入電腦後重新啟動。
-  信任的 IPs 是取決於應用程式是否可以提供與驗證的用戶端 IP。 支援目前只終端機服務。  







>[AZURE.NOTE]在 Windows Server 2012 R2 上的安全終端機服務不支援此功能。




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>若要保護與 Windows 驗證的應用程式，使用下列程序。

1. Azure 多重因素驗證伺服器中按一下 [Windows 驗證圖示。
![Windows 驗證](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. 核取 [啟用 Windows 驗證核取方塊。 根據預設，此方塊未核取。
3. [應用程式] 索引標籤可讓系統管理員設定的 Windows 驗證的一或多個應用程式。
4. 選取伺服器或應用程式，指定是否已啟用伺服器/應用程式。 按一下 [確定]。
5. 按一下 [新增... ] 按鈕。
6. [信任 IPs] 索引標籤可讓您略過來自特定 IPs Azure 多重因素驗證 Windows 的工作階段。 例如，如果員工使用應用程式，從 office，然後從 [首頁，您可以決定您不希望他們的電話鈴響 Azure 多重因素驗證，在辦公室時。 此，您可以指定 office 子網路為受信任 IPs 項目。
7. 按一下 [新增... ] 按鈕。
8. 如果您想要略過單一 IP 位址，請選取 [單一 IP]。
9. 如果您想要略過整個的 IP 範圍，請選取 [IP 範圍]。 範例 10.63.193.1-10.63.193.100。
10. 如果您想要指定範圍的 Ip 子網路標記法使用，請選取 [子網路]。 輸入子網路的起始 IP，然後選擇適當的網路遮罩，從下拉式清單。
11. 按一下 [確定] 按鈕。
