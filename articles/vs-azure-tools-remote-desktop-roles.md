<properties 
   pageTitle="使用遠端桌面 Azure 角色與 |Microsoft Azure"
   description="使用遠端桌面 Azure 角色"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-remote-desktop-with-azure-roles"></a>使用遠端桌面 Azure 角色

使用遠端桌面服務與 Azure SDK，您可以存取 Azure 角色和裝載之 Azure 虛擬機器。 在 Visual Studio 中，您可以從 Azure 專案設定遠端桌面服務。 若要啟用遠端桌面服務，您必須建立工作專案包含一或多個角色，然後將它發佈到 Azure。

>[AZURE.IMPORTANT] 您應該存取疑難排解或僅限開發 Azure 角色。 每個虛擬機器中的目的是執行 Azure 應用程式，不執行其他用戶端應用程式中的特定角色。 如果您想要使用 Azure 裝載您可以使用任何用途的虛擬機器，請參閱從伺服器總管存取 Azure 虛擬機器。

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>若要啟用，並使用遠端桌面 Azure 角色

1. 在方案總管中開啟專案，快顯功能表，然後選擇**發佈**。

    **發佈 Azure 應用程式**精靈會出現。

    ![發佈雲端服務專案] 命令](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. 在精靈的**Microsoft Azure 發佈設定**頁面底部，選取 [**啟用遠端桌面**所有角色] 核取方塊。 

    **遠端桌面設定**] 對話方塊隨即出現。

1. 在 [**遠端桌面設定**] 對話方塊底部，選擇 [**更多選項**] 按鈕。 
 
    隨後便會顯示下拉式清單方塊可讓您建立或選擇憑證，以便透過遠端桌面連線時，您可以將加密認證資訊。

1. 在下拉式清單中，選擇 [**&lt;建立 >**，或選擇現有清單中的。 

    如果您選擇現有的憑證，略過下列步驟。

    >[AZURE.NOTE] 遠端桌面連線，您需要的憑證是不同的憑證，您使用的其他 Azure 作業。 遠端存取憑證必須私密金鑰。

    **建立憑證**] 對話方塊隨即出現。

    1. 提供的新的憑證，好記的名稱，然後選擇 [**確定**] 按鈕。 新的憑證會出現在下拉式清單方塊中。

    1. 在 [**遠端桌面設定**] 對話方塊中，提供使用者名稱和密碼。
    
        您無法使用現有的帳戶。 不指定系統管理員的使用者名稱，為新的帳戶。

        >[AZURE.NOTE] 如果密碼不符合複雜度需求，則會在 [密碼] 方塊旁邊出現紅色圖示。 密碼必須包含字母、 全部小寫字母和數字或符號。

    1. 選擇上便會到期帳戶及之後的遠端桌面連線會遭到封鎖的日期。

    1. 您已提供所需的資訊後，選擇**[確定**] 按鈕。
    
        啟用遠端存取服務的數個設定會新增至的.cscfg 和.csdef 檔案。

1. 當您準備好要發佈您的雲端服務，請在 [ **Microsoft Azure 發佈設定**精靈] 中，選擇**[確定**] 按鈕。

    如果您不是準備好要發佈，選擇 [**取消**] 按鈕。 儲存設定的設定，以及您可以在稍後發佈您的雲端服務。

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>使用遠端桌面連線到 Azure 角色

發佈您的雲端服務上 Azure 後，您可以使用伺服器總管登入虛擬機器 Azure 主控。 

1. 在 [伺服器總管展開**Azure**節點，然後再展開節點雲端服務，其角色來顯示清單的執行個體之一。

1. 開啟執行個體] 節點的快顯功能表，然後選擇 [**連線使用的遠端桌面**。

    ![透過遠端桌面連線](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. 輸入使用者名稱和您先前建立的密碼。 您現在已登入您的遠端工作階段。


