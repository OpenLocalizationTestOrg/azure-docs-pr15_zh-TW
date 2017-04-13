<properties 
    pageTitle="使用與 Windows SSH 鍵 Linux Vm |Microsoft Azure" 
    description="瞭解如何產生和連線至 Linux 上 Azure 虛擬機器在 Windows 電腦上使用 SSH 鍵。" 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>如何使用 SSH 鍵與 Windows Azure 上

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)

當您連線至 Azure 中的 Linux 虛擬機器 (Vm) 時，您應該使用[公開金鑰加密](https://wikipedia.org/wiki/Public-key_cryptography)提供登入您的 Linux VM 更安全的方式。 此程序牽涉使用安全殼層 (SSH)] 命令來驗證您自己，而非使用者名稱和密碼公開及私密金鑰 exchange。 密碼受到惡意強制攻擊，特別是在具網際網路 Vm，例如網頁伺服器上。 本文提供 SSH 索引鍵和如何產生在 Windows 電腦上的適當按鍵的概觀。


## <a name="overview-of-ssh-and-keys"></a>SSH 與索引鍵的概觀

您可以安全地登入您的 Linux VM 使用公開及私密金鑰︰

- **公開金鑰**會放在您 Linux VM 或您想要使用公開金鑰加密的任何其他服務。
- **私密金鑰**是什麼簡報您 Linux VM 當您登入]，以驗證您的身分識別。 保護這個私密金鑰。 不要共用它。

在多個 Vm 和服務可這些公開及私密金鑰的按鍵。 您不需要針對每個 VM 或您想要存取的服務的一組金鑰。 如需更詳細的概觀，請參閱[公開金鑰加密](https://wikipedia.org/wiki/Public-key_cryptography)。

SSH 是允許安全的登入不安全的連線的加密的連線通訊協定。 它會裝載於 Azure Linux Vm 的預設連接通訊協定。 雖然 SSH 本身提供加密的連線，但是使用密碼以 SSH 連線仍會使 VM 受到惡意強制攻擊或猜測密碼。 連線到使用 SSH VM 更安全，且慣用的方法是使用這些公開及私密金鑰機碼，也就是 SSH 鍵。

如果您不想使用 SSH 鍵，您可以仍登入您的 Linux Vm 使用密碼。 如果您 VM 不網際網路上公開，使用密碼可能就足以。 不過，您仍需要的每個 Linux VM 管理您的密碼和與維護健全的密碼原則作法，例如 [最小的密碼長度與定期更新它們。 使用 SSH 索引鍵的個別認證管理跨多個 Vm 複雜度。


## <a name="windows-packages-and-ssh-clients"></a>Windows 套件和 SSH 用戶端

您連線至及管理 Linux Vm Azure 使用**ssh**用戶端中。 在 Windows 電腦通常不需要**ssh**用戶端安裝。 一般 Windows SSH 用戶端可以安裝下列套件中包含︰

- [在 Windows 版的給](https://git-for-windows.github.io/)
- [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] 最新的 Windows 10 週年紀念日更新包含被 for Windows。 此功能可讓您執行 Windows 子系統 Linux 和存取公用程式，例如 SSH 用戶端。 被為 Windows 仍在開發，而且會被視為 beta 版。 如需在 Windows 版被的詳細資訊，請參閱[在 Windows 上 Ubuntu 被](https://msdn.microsoft.com/commandline/wsl/about)。


## <a name="which-key-files-do-you-need-to-create"></a>您需要建立的主要的檔案？

Azure 需要至少 2048年位元， **ssh rsa**格式化公開及私密金鑰。 如果您使用傳統部署模型 Azure 資源管理，那麼您也需要產生 PEM (`.pem`檔案)。

以下是部署案例中，在每一個您使用的檔案類型︰

1. **ssh-rsa**金鑰，才能使用[Azure 入口網站](https://portal.azure.com)中，任何部署及使用[Azure CLI](../xplat-cli-install.md)資源管理員部署。
    - 這些按鍵通常需要所有大部分的人。
2. `.pem`若要建立使用[傳統入口網站](https://manage.windowsazure.com)的 Vm 需要檔案。 使用[Azure CLI](../xplat-cli-install.md)的傳統部署也支援這些按鍵。
    - 您只需要建立這些額外的金鑰和憑證，如果您正在管理建立使用傳統部署模型的資源。


## <a name="install-git-for-windows"></a>安裝 Windows 版的給

上一節所列包含多個封包`openssl`在 Windows 版的工具。 若要建立公開及私密金鑰需要這項工具。 雖然您可以選擇您想要的封裝，下列範例詳細說明如何安裝和使用**給 for Windows**。 **Windows 版給**可讓您存取其他開啟來源軟體 ([OS](https://en.wikipedia.org/wiki/Open-source_software)) 工具及公用程式實用 Linux Vm 處理。

1. 下載並安裝**Windows 版給**從下列位置︰ [https://git-for-windows.github.io/](https://git-for-windows.github.io/)。

2. 除非您特別需要變更，請在安裝過程接受預設的選項。

3. 從**[開始] 功能表**中執行**給艦隊** > **給** > **給艦隊**。 主控台看起來就像以下範例︰

    ![給的 Windows 被命令介面](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>建立私密金鑰

1. 在您**就可以給艦隊**] 視窗中，使用`openssl.exe`建立私人的索引鍵。 下列範例會建立名為`myPrivateKey`和憑證名為`myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    輸出看起來就像以下範例︰

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. 回答提示的國家/地區名稱、 位置、 組織名稱等。

3. 在您目前的工作目錄中建立您的新的私密金鑰和憑證。 安全性最佳作法，您應該私密金鑰上設定權限，以便僅能存取︰

    ```bash
    chmod 0600 myPrivateKey
    ```

4. 如果您需要管理傳統的資源，轉換`myCert.pem`至`myCert.cer`(DER 編碼 X509 憑證)。 只有當您需要特別管理較舊的傳統資源，請執行此選擇性的步驟。 

    轉換憑證使用下列命令︰

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>建立 PuTTY 私密金鑰

PuTTY 是在 Windows 版的常見 SSH 用戶端。 您可以自由使用任何您想要的 SSH 用戶端。 若要使用 PuTTY，您需要建立其他類型的金鑰-PuTTY 私人鍵 (PPK)。 如果您不想要使用 PuTTY，請略過此區段。

下列範例會建立專為使用 PuTTY 此其他私人登錄機碼︰

1. 若要將您私人的金鑰轉換成 PuTTYgen 可以瞭解 RSA 私密金鑰使用**給艦隊**。 下列範例會建立名為`myPrivateKey_rsa`從現有的金鑰名為`myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    安全性最佳作法，您應該私密金鑰上設定權限，以便僅能存取︰

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. 下載並執行 PuTTYgen 從下列位置︰ [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. 按一下功能表︰**檔案** > **載入私密金鑰**

4. 找出您的私密金鑰 (`myPrivateKey_rsa`在先前範例)。 當您開始**給艦隊**時的預設目錄`C:\Users\%username%`。 變更檔案的篩選，以顯示**所有檔案 (\*。\*)**:

    ![載入 PuTTYgen 現有私密金鑰](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. 按一下 [**開啟**]。 提示會指出金鑰已成功匯入︰

    ![已成功匯入的 PuTTYgen 鍵](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. 按一下**[確定**] 關閉提示]。

7. 公開金鑰會顯示在**PuTTYgen**視窗的頂端。 您複製並貼上此公開金鑰 Azure 入口網站或 Azure 資源管理員範本建立 Linux VM 時。 您也可以按一下 [**儲存公開金鑰**複本儲存到您的電腦︰

    ![儲存 PuTTY 公開金鑰檔案](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    下列範例會顯示您想要複製與此公開金鑰貼入 Azure 入口網站，當您建立 Linux VM。 公開金鑰然後通常是儲存在`~/.ssh/authorized_keys`上您的新 VM。

    ![當您建立 VM Azure 入口網站中使用公開金鑰](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. 回到**PuTTYgen**，按一下 [**儲存私密金鑰**︰

    ![儲存 PuTTY 私密金鑰檔案](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] 提示會詢問您是否要繼續但不是複雜密碼輸入您的金鑰。 複雜密碼，就像附加至您的私密金鑰的密碼。 即使某人以取得您的私密金鑰，他們仍會無法使用的索引鍵來進行驗證。 他們也需要複雜密碼。 不複雜密碼，如果其他人取得您私人的金鑰，他們可以登入任何 VM 或使用該金鑰的服務。 我們建議您建立複雜密碼。 不過，如果您忘記複雜密碼，就是無法復原。

    如果您想要輸入密碼，按一下 [**否]**，在 PuTTYgen 主視窗中輸入密碼，然後按一下**儲存私密金鑰**一次。 否則，請按一下**[是]**以繼續但不提供選用的複雜密碼。

8. 輸入的名稱及儲存 PPK 檔案的位置。


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>使用 Putty 至 SSH Linux 電腦

同樣地，PuTTY 是在 Windows 版的常見 SSH 用戶端。 您可以自由使用任何您想要的 SSH 用戶端。 下列步驟詳細說明如何使用您的私密金鑰使用 SSH 您 Azure VM 驗證方法。 步驟類似的其他 SSH 金鑰用戶端而言載入您的私密金鑰需要驗證 SSH 連線。

1. 下載並執行的 putty 從下列位置︰ [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. 填寫主機名稱或您 VM 從 Azure 入口網站的 IP 位址︰

    ![開啟新的 PuTTY 連線](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. 選取 [**開啟**]，再按一下 [**連線** > **SSH** > **驗證**] 索引標籤。 瀏覽]，然後選取您的私密金鑰︰

    ![選取 [驗證您 PuTTY 私人索引鍵](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. 按一下 [**開啟**連線到您的虛擬機器
 

## <a name="next-steps"></a>後續步驟
您也可以產生[使用 OS X 和 Linux](virtual-machines-linux-mac-create-ssh-keys.md)公開及私密金鑰。

更多關於被 for Windows 和 Windows 電腦上有 OS 工具方便的優點的詳細資訊，請參閱[在 Windows 上 Ubuntu 被](https://msdn.microsoft.com/commandline/wsl/about)。

如果您無法連線至您的 Linux Vm 使用 SSH，請參閱[疑難排解 SSH 連線 Azure Linux VM](virtual-machines-linux-troubleshoot-ssh-connection.md)。