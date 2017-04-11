<properties
    pageTitle="疑難排解自動調整大小的虛擬機器縮放比例設定 |Microsoft Azure"
    description="疑難排解自動調整大小的虛擬機器縮放比例設定。 瞭解一般遇到的問題，以及如何解決方法。"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>疑難排解自動調整大小的虛擬機器縮放設定

**問題**– 您已建立自動縮放基礎結構 Azure 資源管理員中部署像這樣的範本，例如使用 VM 縮放比例設定 –: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – 您有您所定義的縮放比例規則和運作很好，除了，無論您將在 Vm 多少載入時，就不會自動調整大小。

## <a name="troubleshooting-steps"></a>疑難排解步驟

包含要考量的一些事項︰

- 每個 VM 有多少核心，以及會載入每個核心？
 上述範例 Azure 快速入門範本中有載入單一核心 do_work.php 指令碼。 如果您使用單一核心例如 Standard_A1 或 D1 的虛擬記憶體大小大於 VM，則必須執行這個載入多次。 檢查多少核心您 Vm 檢閱[大小的 Windows Azure 中的虛擬機器](../virtual-machines/virtual-machines-windows-sizes.md)

- 多少 Vm 設定 VM 縮放比例，請在您正在執行每一項工作？

    延展事件才會進行時平均值 CPU**所有**Vm 比例集中超過臨界值，在自動調整大小的規則定義時間內部。

- 您錯過的任何縮放比例事件？

    核取 [縮放比例事件 Azure 入口網站中的稽核記錄檔]。 可能發生縮放比例設定，錯過下的小數位數。 您可以篩選 「 小數位數]。

    ![稽核記錄][audit]

- 是您的縮放比例增益集和向外臨界值極大？

    假設您設定擴充平均 CPU 大於 50%超過 5 分鐘，並且在平均值的 CPU 時小於 50%的比例縮放時的規則。 CPU 使用率接近此閥值，持續增加和減少的設定大小的小數位數動作時，這會造成 「 拍打 」 的問題。 因此，自動調整大小服務會防止 「 拍打 」，可以顯示為不按比例縮放的。 因此請確定您向外] 及 [縮放比例增益集臨界值是很不同允許一些空格區隔縮放比例。

- 您是否撰寫您自己的 JSON 範本？

    您可以輕鬆進行錯誤，因此上述其中一個證明運作，以及進行小型增量的變更等從範本開始。 

- 手動調整或縮小嗎？

    請嘗試重新部署 VM 縮放比例設定不同的 「 容量 」 設定，若要手動變更 Vm 數資源。 若要這麼做為範例範本就在這裡︰ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing-您可能需要編輯範本，請確定您的縮放比例設定為使用有相同的電腦大小。 如果您已成功可以手動變更 Vm 數，您知道問題的隔離要自動調整大小。

- 檢查您的 Microsoft.Compute/virtualMachineScaleSet 及 Microsoft.Insights [Azure 資源檔案總管](https://resources.azure.com/)] 中的資源

    這是不可或缺的疑難排解工具會顯示您的狀態 Azure 資源管理員資源。 按一下您的訂閱，然後查看 [資源] 群組中您正在進行疑難排解。 在計算資源提供者查看 VM 小數位數，設定您建立並核取 [執行個體] 檢視，顯示您的部署狀態。 此外，請在 VM 縮放比例設定 Vm 的執行個體檢視。 接著進入 Microsoft.Insights 資源提供者，然後核取 [自動調整大小規則外觀精美。

- 診斷副檔名為工作，以及發出效能資料？

    __更新︰__若要使用 [主機基礎指標管線不再需要安裝副檔名為診斷已改良 azure 自動調整大小。 這表示幾個段落不再套用如果您建立自動縮放應用程式使用新的管線下一步]。 已經轉換成使用主機管線 Azure 範本的範例︰ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale、 https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale。 

    使用自動調整大小主機基礎指標是較佳的原因如下︰

    - 沒有診斷副檔名為較少變動必須安裝。
    - 簡單的範本。 只要將獲得深入見解自動調整大小規則新增至現有的縮放比例設定範本。
    - 報告及更快速啟動新的 Vm 更可靠。

    您可能會想要繼續使用副檔名為診斷只原因就是如果您需要的記憶體診斷報告/縮放比例。 Host （主機） 根據指標不報告記憶體。

    這一點之後，只要遵循本文其餘如果您仍在使用診斷副檔名您自動調整。

    自動調整大小 Azure 資源管理員中的商務連絡人就可以使用 （但不再） 透過 VM 呼叫延伸診斷副檔名。 它會發出效能資料至您在範本中定義的儲存空間帳戶。 此資料，然後會 Azure 監視器服務彙總。

    如果獲得深入見解服務無法讀取 Vm 的資料，就會傳送電子郵件-例如給您，如果 Vm 向下、，因此請查看您的電子郵件 （一個您指定在建立 Azure 帳戶）。

    您也可以移並自己查看的資料。 查看 Azure 儲存體帳戶使用雲端的檔案總管]。 範例使用[Visual Studio 雲端的檔案總管](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)] 中，登入，並挑選 Azure 訂閱您使用，和診斷副檔名中定義您的部署範本中參照的診斷儲存體帳戶名稱。

    ![雲端總管][explorer]

    這裡，您會看到多個資料表中每個 VM 的資料所儲存的位置。 參加 Linux] 和 [CPU 公制作為範例，查看最近的列。 Visual Studio 雲端總管支援查詢語言，因此您可以執行查詢，例如 「 時間戳記 gt datetime' 2016年-02-02T21:20:00Z' 」，請確定您已取得最新的事件 （假設時間會以 utc 表示）。 您看到中那里對應的縮放比例規則設定您的資料嗎？ 在下面的範例中，針對機器 20 CPU 開始移到最後一個 5 分鐘增加為 100%。

    ![儲存資料表][tables]

    如果資料不存在，就表示問題是在 Vm 執行診斷副檔名。 如果資料是有，就表示有問題比例規則，或深入見解服務。 檢查[Azure 狀態](https://azure.microsoft.com/status/)。

    一旦您已經完成這些步驟，如果您仍然有自動調整大小問題，您可以嘗試[msdn 文章](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp)，或[堆疊溢位](http://stackoverflow.com/questions/tagged/azure)上的論壇或登支援電話。 要準備共用範本和效能資料的檢視。

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
