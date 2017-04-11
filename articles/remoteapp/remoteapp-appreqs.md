
<properties
    pageTitle="應用程式需求 Azure RemoteApp |Microsoft Azure"
    description="瞭解您想要在 Azure RemoteApp 中使用的應用程式的需求。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="app-requirements"></a>應用程式需求

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 支援串流 32 位元或 64 位元的 windows 應用程式從 Windows Server 2012 R2 的圖像。 大部分現有 32 位元或 64 位元的 windows 應用程式中 Azure RemoteApp 執行 「 現狀 」 (遠端桌面服務或先前稱為 「 終端機服務) 環境。 不過，及執行也不一致-部分應用程式正常與執行，而其他人沒有。 下列資訊提供指導開發遠端桌面服務環境中的應用程式並測試，以確保相容性。

提示︰ 我們正在為您建立的應用程式使用範例。 您會看到新的主題討論 RemoteApp 中使用 Microsoft Access、 QuickBooks 及應用程式和 V。

## <a name="requirements"></a>需求
下列三個的需求，如果追蹤，協助您也可以在 RemoteApp 中執行的應用程式︰

1.  符合所有[憑證需求 Windows 桌面應用程式](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx)，並遵循[遠端桌面服務程式設計指導方針](https://msdn.microsoft.com/library/aa383490.aspx)的應用程式會有與 RemoteApp 完成相容性]。
2.  應用程式應該永遠不會資料儲存在本機上的圖像或 RemoteApp 執行個體，可能會遺失。  建立 RemoteApp 集合之後，請執行個體會複製沒有狀態，並應只包含應用程式。 將資料儲存於外部來源或使用者的設定檔。
3.  自訂圖像不應該包含可能會遺失的資料。  

## <a name="testing-your-apps"></a>測試您的應用程式
使用這些步驟來測試應用程式︰

1.  安裝 Windows Server 2012 R2 和您的應用程式
2.  啟用遠端桌面
3.  建立兩個使用者帳戶，使用者 a 和使用者 b，將兩個使用者帳戶新增至遠端桌面安全性群組。
4.  建立兩個同時 RDS 工作階段到電腦時啟動應用程式，檢查多工作階段相容性。
5.  驗證應用程式行為

## <a name="application-development-guidelines"></a>應用程式開發指導方針
使用下列方針為 RemoteApp 開發應用程式。

### <a name="multiple-users"></a>多位使用者

- 安裝[單一使用者的應用程式](https://msdn.microsoft.com/library/aa380661.aspx)，可以在多使用者環境中建立的問題。
- 應用程式應該在特定使用者的位置，全域套用至所有使用者的資訊作分開的 [[儲存特定使用者的資訊](https://msdn.microsoft.com/library/aa383452.aspx)。
- RemoteApp 使用多個[核心物件的命名空間](https://msdn.microsoft.com/library/aa382954.aspx);主要是在用戶端/伺服器應用程式中的服務會使用通用命名空間。
- 不安全的電腦名稱或指派給電腦的[IP 位址](https://msdn.microsoft.com/library/aa382942.aspx)是單一使用者與相關聯因為多個使用者可以到遠端桌面工作階段主機 （RD 工作階段主機） 伺服器同時登入。

### <a name="performance"></a>效能
- 您將您的應用程式新增至 RemoteApp 之前，請停用[圖形效果](https://msdn.microsoft.com/library/aa380822.aspx)。
- 最大化的所有使用者的 CPU 可用性，[停用[背景的工作](https://msdn.microsoft.com/library/aa380665.aspx)，或建立有效的背景不是資源的工作。
- 您應該微調並平衡多使用者的多處理器環境的應用程式[執行緒使用方式](https://msdn.microsoft.com/library/aa383520.aspx)。
- 若要最佳化效能，但最好[偵測到](https://msdn.microsoft.com/library/aa380798.aspx)的應用程式是否在用戶端工作階段中執行。
