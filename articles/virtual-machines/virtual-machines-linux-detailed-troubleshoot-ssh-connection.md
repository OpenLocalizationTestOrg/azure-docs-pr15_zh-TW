<properties
    pageTitle="詳細 SSH 疑難排解 Azure VM |Microsoft Azure"
    description="更詳細的疑難排解步驟連線到 Azure 虛擬機器問題 SSH"
    keywords="ssh 拒絕連線，ssh 錯誤、 azure ssh，SSH 連線失敗"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>詳細的 SSH 疑難排解步驟

有多個可能的原因 SSH 用戶端可能無法達到 VM 上的 SSH 服務。 如果您有追蹤多個[一般 SSH 疑難排解步驟](virtual-machines-linux-troubleshoot-ssh-connection.md)，您需要進一步疑難排解連線問題。 本文會引導您完成詳細的疑難排解步驟，以決定 SSH 連線失敗的位置，以及如何解決問題。

## <a name="take-preliminary-steps"></a>執行基本步驟

下圖顯示元件。

![顯示 SSH 服務的元件的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

下列步驟可協助您隔離失敗的來源，並算出解決方案或因應措施。

首先，檢查 VM 入口網站中的狀態。

在[Azure 入口網站](https://portal.azure.com)︰

1. 建立使用傳統的部署模型 Vm，選取 [**瀏覽** > **（傳統） 的虛擬機器** > *VM 名稱*。

    -或-

    使用資源管理員模型建立 Vm，選取 [**瀏覽** > **虛擬機器** > *VM 名稱*。

    [狀態] 窗格 vm 應該會顯示**執行**。 向下顯示新的計算、 儲存及網路資源的活動捲動。

2. 選取 [**設定**]，以檢查端點、 IP 位址和其他設定]。

    若要識別的使用資源管理員所建立的 Vm 中的端點，請確認已經定義了[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。 也驗證規則的已套用至 [網路] 安全性群組與他們參照子網路中。

在[Azure 傳統入口網站](https://manage.windowsazure.com)，為使用傳統的部署模型建立的 Vm:

1. 選取**虛擬機器** > *VM 名稱*。
2. 選取 [若要檢查其狀態 VM 的**儀表板**]。
3. 選取 [若要查看最近的計算、 儲存及網路資源的活動**監視器**]。
4. 選取以確保 SSH 流量的端點的**結束點**。

若要驗證網路連線，請核取 [設定結束點，請參閱是否您連絡 VM 透過其他通訊協定，例如 HTTP 或其他服務。

完成這些步驟之後, SSH 再次嘗試連線。


## <a name="find-the-source-of-the-issue"></a>找出問題的來源

SSH 用戶端電腦上的可能失敗達到 SSH 上的服務 Azure VM 問題或錯誤設定如下︰

- [SSH 用戶端電腦](#source-1-ssh-client-computer)
- [組織邊緣裝置](#source-2-organization-edge-device)
- [雲端服務端點，以及存取控制清單 (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [網路安全性群組](#source-4-network-security-groups)
- [Linux 型 Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>來源 1: SSH 用戶端電腦

若要刪除您的電腦為 [失敗的來源，請確認其進行 SSH 連線至另一個內部部署，Linux 電腦。

![醒目提示 SSH 用戶端電腦元件的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

如果連線失敗，請檢查您電腦上的下列動作︰

- 設定的本機防火牆封鎖了輸入或輸出 SSH 流量 (TCP 22)
- 安裝在本機的用戶端 proxy 軟體，使 SSH 連線
- 本機安裝網路監視使 SSH 連線的軟體
- 監視流量或允許/不允許特定類型的流量的其他類型的安全性軟體

如果其中一個條件套用時，請暫時停用軟體並嘗試 SSH 連線到內部部署電腦，找出您的電腦封鎖連線的原因。 然後使用您的網路系統管理員，以修正軟體設定為允許 SSH 連線。

如果您使用的憑證驗證]，確認您擁有下列權限到.ssh 資料夾在您的主目錄中︰

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*.pub
- Chmod 600 ~/.ssh/id_rsa （或任何其他檔案儲存在這些私人索引鍵）
- Chmod 644 ~/.ssh/known_hosts （包含您已連線至透過 SSH 的主機）

## <a name="source-2-organization-edge-device"></a>來源 2︰ 組織邊緣裝置

若要刪除您組織的邊緣裝置做為失敗的來源，請確認直接連線到網際網路的電腦，可以將 Azure VM 進行 SSH 連線。 如果您透過網站-VPN 或 Azure ExpressRoute 連線存取 VM，直接跳到[來源 4︰ 網路安全性群組](#nsg)。

![醒目提示組織邊緣裝置的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

如果您沒有安裝會直接在電腦連線到網際網路，它自己的 [資源] 群組中建立新的 Azure VM 或雲端服務，並用。 如需詳細資訊，請參閱[建立虛擬機器執行 Linux Azure 中](virtual-machines-linux-quick-create-cli.md)。 刪除資源群組或 VM 及雲端服務，當您完成您的測試。

如果直接連線到網際網路的電腦，您可以建立 SSH 連線，請檢查您的組織邊緣的裝置︰

- 內部防火牆封鎖了與網際網路 SSH 流量
- Proxy 伺服器，使 SSH 連線
- 侵入偵測或網路監視邊緣網路使 SSH 連線的裝置上執行的軟體

使用您的網路系統管理員，若要修正問題的組織邊緣裝置設定為允許與網際網路 SSH 流量。

## <a name="source-3-cloud-service-endpoint-and-acl"></a>來源 3︰ 雲端服務端點和 ACL

> [AZURE.NOTE] 此來源僅適用於建立使用傳統的部署模型的 Vm。 使用資源管理員所建立的 Vm，直接跳到[來源 4︰ 網路安全性群組](#nsg)。

若要排除的雲端服務端點及 ACL，做為失敗的來源，請確認在相同的虛擬網路中的另一個 Azure VM，可以將 VM 進行 SSH 連線。

![醒目提示雲端服務端點和 ACL 的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

如果您沒有安裝其他 VM 相同的虛擬網路中，您可以輕鬆地建立新的項目。 如需詳細資訊，請參閱[建立 Linux VM Azure 使用 CLI 上](virtual-machines-linux-quick-create-cli.md)。 刪除額外 VM，當您完成您的測試。

如果您可以建立 SSH 連線使用相同的虛擬網路中 VM，檢查下列項目︰

- **在目標 VM SSH 流量的端點設定。** 端點的私人 TCP 連接埠應符合 SSH 服務 VM 上的接聽所在的 TCP 連接埠。 （預設連接埠是 22）。 使用資源管理員部署模型建立 Vm，請確認 SSH TCP 連接埠號碼 Azure 入口網站中的，選取 [**瀏覽** > **虛擬機器 (v2)** > *VM 名稱* > **設定** > **結束點**。

- **在目標虛擬機器上 SSH 流量端點 ACL。** ACL 可讓您指定允許或拒絕連入流量從網際網路]，依其來源 IP 位址。 設定錯誤的 Acl 可以防止端點連入 SSH 流量。 核取您 Acl，確定該連入流量從您的 proxy 的公用 IP 位址或允許其他 edge server。 如需詳細資訊，請參閱[有關網路存取控制清單 (Acl)](../virtual-network/virtual-networks-acl.md)。

若要刪除端點做為來源的問題，請移除目前的端點，建立新的結束點，並指定 SSH 名稱 （TCP 連接埠 22 公開及私密金鑰的連接埠號碼）。 如需詳細資訊，請參閱[設定中 Azure 虛擬機器中的端點](virtual-machines-windows-classic-setup-endpoints.md)。

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>來源 4︰ 網路安全性群組

網路安全性群組可讓您微調控制允許傳入和傳出流量。 您可以建立規則，橫跨子網路雲端 Azure 虛擬網路中的服務。 檢查您的網路安全性群組規則，以確保允許的 SSH 流量與網際網路。
如需詳細資訊，請參閱[關於網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

## <a name="source-5-linux-based-azure-virtual-machine"></a>來源 5: Linux 型 Azure 虛擬機器

可能發生的問題的最後一個來源是 Azure 虛擬機器本身。

![醒目提示 Linux 型 Azure 虛擬機器的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

如果您尚未這麼做，請依照指示[重設密碼或 Linux 型虛擬機器 SSH](virtual-machines-linux-classic-reset-access.md)。

請嘗試重新連線從您的電腦。 如果仍然失敗，以下是一些可能的問題︰

- 目標虛擬機器上未執行 SSH 服務。
- TCP 連接埠 22 非接聽 SSH 服務。 若要測試此，您的本機電腦上安裝 telnet 用戶端，並執行 「 telnet *cloudServiceName*。 cloudapp.net 22 」。 這會決定虛擬機器是否允許傳入和傳出通訊 SSH 端點。
- 在本機的防火牆目標虛擬機器上有會阻止輸入或輸出 SSH 流量的規則。
- 侵入偵測或網路監視 Azure 虛擬機器執行軟體防止 SSH 連線。


## <a name="additional-resources"></a>其他資源
如需有關如何疑難排解應用程式存取的詳細資訊，請參閱[疑難排解存取 Azure 虛擬機器上執行的應用程式](virtual-machines-linux-troubleshoot-app-connection.md)