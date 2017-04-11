<properties 
   pageTitle="疑難排解部署的 StorSimple 裝置 |Microsoft Azure"
   description="說明如何診斷和修復發生錯誤，且目前已部署操作 StorSimple 裝置上。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />

# <a name="troubleshoot-an-operational-storsimple-device"></a>疑難排解操作的 StorSimple 裝置

## <a name="overview"></a>概觀

本文提供很有幫助解決設定問題的疑難排解指南，您可能會遇到後 StorSimple 裝置已部署和運作。 其說明常見的問題可能的原因與建議的步驟可協助您解決執行 Microsoft Azure StorSimple 時，您可能會遇到的問題。 這項資訊適用於 StorSimple 內部部署的實體裝置和 StorSimple 虛擬裝置。

在這份文件，您可以找到 Microsoft Azure StorSimple 作業期間，您可能會遇到的錯誤代碼清單結尾，以及步驟時可採取錯誤的解決方法。 

## <a name="setup-wizard-process-for-operational-devices"></a>設定精靈的程序操作的裝置

使用設定精靈 ([叫用 HcsSetupWizard][1]) 來檢查裝置設定並採取必要的修正動作。

當您在先前已設定和操作的裝置上執行設定精靈時，處理流程會不同。 您可以變更下列項目︰

- IP 位址]、 [子網路遮罩] 及 [閘道器
- 主要 DNS 伺服器
- 主要 NTP 伺服器
- 選用的 web proxy 設定

設定精靈不會執行的作業相關密碼收集與裝置註冊。

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>後續執行設定精靈的過程中發生的錯誤

下表描述錯誤，您可能會遇到當您執行的操作的裝置上設定精靈、 錯誤，可能的原因，並建議的解決方法的動作。 

| [否]。 | 錯誤訊息或條件 | 可能的原因 | 建議的動作 |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | 錯誤 350032︰ 此裝置已停用。 | 如果您已在裝置上執行設定精靈，您會看到此錯誤。 | 後續步驟[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。 停用的裝置無法置於服務。 可以重新啟用裝置可能會需要工廠重設。 |
|  2  | 呼叫 HcsSetupWizard: ERROR_INVALID_FUNCTION (從 HRESULT 的例外狀況︰ 0x80070001) | DNS 伺服器更新會失敗。 DNS 設定都是通用設定]，就會套用在所有的網路介面。 | 啟用介面，並重新套用 [DNS 設定。 這可能會中斷網路上的其他啟用的介面，因為這些設定全域。 |
|  3  | 裝置會顯示在線上 StorSimple 管理員服務入口網站中，但當您嘗試完成的最小的設定，並儲存設定時，操作會失敗。 | 初始在安裝期間，網頁 proxy 並未設定，即使但實際的 proxy 伺服器中的位置。 | 使用[測試 HcsmConnection cmdlet] [2]找出錯誤。 [連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)如果您無法修正問題。 |
|  4  | 呼叫 HcsSetupWizard︰ 值不屬於預期的範圍。 | 不正確的子網路遮罩會產生這個錯誤。 可能的原因有︰ <ul><li> 子網路遮罩會遺失或空白。</li><li>Ipv6 前置字元格式不正確。</li><li>介面雲端功能，但閘道器在遺失或不正確。</li></ul>請注意，資料 0 自動雲端功能是否執行設定精靈設定。 | 以判斷問題，請使用 [子網路 0.0.0.0 或 256.256.256.256，然後查看 [成果。 視需要請輸入的子網路遮罩、 閘道和 Ipv6 首碼的正確的值。 |
 
## <a name="error-codes"></a>錯誤碼

錯誤的列數字順序]。

|錯誤號碼|錯誤文字或描述|建議的使用者動作|
|:---|:---|:---|
|10502|存取您儲存的帳戶時，發生錯誤。|等待幾分鐘，然後再試一次。 如果仍有錯誤，請連絡 Microsoft 支援人員的下一個步驟。|
|40017|備份作業已失敗，因為在裝置上找不到備份原則中指定大量。|重試備份作業，如果錯誤持續發生，請連絡 Microsoft 支援服務。 下一個步驟。|
|40018|備份作業已失敗，因為備份原則中指定的區找不到任何裝置上。 |重試備份作業，如果錯誤持續發生，請連絡 Microsoft 支援服務。 下一個步驟。|
|390061|系統會忙碌或無法使用。|等待幾分鐘，然後再試一次。 如果仍有錯誤，請連絡 Microsoft 支援人員的下一個步驟。|
|390143|錯誤碼 390143 發生錯誤。 （未知的錯誤）。|如果仍有錯誤，請連絡 Microsoft 支援服務的下一個步驟。|

## <a name="next-steps"></a>後續步驟

如果您無法解決問題，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)取得協助。 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
