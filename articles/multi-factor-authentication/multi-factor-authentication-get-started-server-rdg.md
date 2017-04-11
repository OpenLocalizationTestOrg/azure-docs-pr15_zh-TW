<properties 
    pageTitle="遠端桌面閘道並使用 RADIUS Azure 多重因素驗證伺服器"
    description="這是 Azure 多重因素驗證] 頁面，能夠協助您部署遠端桌面 (RD) 閘道和 Azure 多重因素驗證伺服器使用 RADIUS。"
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
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>遠端桌面閘道並使用 RADIUS Azure 多重因素驗證伺服器

在許多情況下，遠端桌面閘道會使用本機 NPS 驗證的使用者。 這份文件將說明如何路由從遠端桌面閘道器 （透過本機 NPS) 至多重因素驗證伺服器的 RADIUS 要求取出。

多重因素驗證伺服器應該安裝在不同的伺服器，然後將 proxy 回到遠端桌面閘道伺服器上 NPS RADIUS 要求。 NPS 驗證的使用者名稱和密碼之後，它會傳回便會執行第二個因子變異數，傳回結果閘道之前驗證的多重因素驗證伺服器的回應。





## <a name="configure-the-rd-gateway"></a>設定 RD 閘道器

RD 閘道器必須 RADIUS 驗證傳送給 Azure 多重因素驗證設定。 一旦 RD 閘道器安裝完畢，設定，並且使用，請移至 RD 閘道器的屬性。 移至 [RD 首字放大存放區] 索引標籤，然後將其變更為使用中央伺服器，而不本機伺服器執行 NPS。 新增一或多個 Azure 多重因素驗證伺服器作為 RADIUS 伺服器，並指定每個伺服器的共用的密碼。





## <a name="configure-nps"></a>設定 NPS

RD 閘道器會使用 NPS Azure 多重因素驗證傳送 RADIUS 要求。 若要避免從多重因素驗證完成前逾時 RD 閘道器必須變更逾時。 若要設定 NPS 使用下列程序。

1. 在 NPS 中，展開 [RADIUS 用戶端與伺服器的功能表，在左欄中，按一下 [遠端 RADIUS 伺服器群組上。 移到 TS 閘道伺服器群組的屬性。 編輯顯示 RADIUS 伺服器，並移至 [負載平衡] 索引標籤。 變更 「 無回應邀請視為之前的秒數字卸除 」 和 「 之間的秒數要求伺服器識別時，無法使用 」 為 30 到 60 秒。 按一下 [上驗證/帳戶] 索引標籤，並確保指定半徑連接埠符合多重因素驗證伺服器接聽的連接埠。
2. NPS 也必須從 Azure 多重因素驗證伺服器接收 RADIUS proxy 設定。 在左側功能表中，按一下 [RADIUS 用戶端上]。 為半徑用戶端新增 Azure 多重因素驗證伺服器。 選擇 [好記的名稱，然後指定共用的密碼。
3. 展開 [左側導覽中的 [原則] 區段，按一下 [連線要求原則。 包含連線要求原則稱為 TS 閘道器授權原則設定 RD 閘道器時所建立。 此原則轉多重因素驗證伺服器 RADIUS 要求。
4. 複製此原則，建立新的 pin。 在新的原則，新增好記的名稱與用戶端易記名稱相符的條件設定上述步驟 2 中 Azure 多重因素驗證伺服器 RADIUS 用戶端。 變更驗證提供者的本機電腦。 此原則可確保從 Azure 多重因素驗證伺服器收到 RADIUS 要求時，在本機，而不是將 RADIUS 要求傳送至 Azure 多重因素驗證伺服器可能會導致循環播放條件進行驗證。 若要防止迴圈條件，此新原則的順序必須原始的原則轉寄多因素驗證伺服器的上方。

## <a name="configure-azure-multi-factor-authentication"></a>設定 Azure 多重因素驗證


--------------------------------------------------------------------------------



Azure 多重因素驗證伺服器設定為 [RADIUS proxy RD 閘道和 NPS 之間。  您應該安裝 RD 閘道伺服器不同的網域的伺服器上。 您可以使用下列程序來設定 Azure 多重因素驗證伺服器。

1. 開啟 Azure 多重因素驗證伺服器，然後按一下 [RADIUS 驗證] 圖示。 核取 [啟用 RADIUS 驗證核取方塊。
2. 在 [客戶] 索引標籤上確定連接埠相符 NPS 中的設定，然後按一下 [新增... ] 按鈕。 新增 RD 閘道伺服器 IP 位址、 應用程式的名稱 （可省略） 和共用的密碼。 共用的私人必須 Azure 多重因素驗證伺服器和 RD 閘道相同。
3. 按一下 [目標] 索引標籤並選擇 [RADIUS 伺服器選項] 按鈕。
4. 按一下 [新增... ] 按鈕。 輸入的 IP 位址、 共用的密碼和 NPS 伺服器的連接埠。 除非使用 RADIUS 用戶端和 RADIUS 目標中央 NPS 都相同。 共用的密碼必須符合 NPS 伺服器 RADIUS 用戶端一節中的一個設定。

![Radius 驗證](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
