<properties
    pageTitle="允許 revtrieve Azure 堆疊鍵保存庫機密應用程式 |Microsoft Azure"
    description="使用範例應用程式來使用 Azure 堆疊鍵保存庫"
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

# <a name="run-the-sample-application-for-key-vault"></a>執行金鑰保存庫範例應用程式 

本指南中，您會使用範例應用程式從金鑰保存庫擷取密碼和密碼。

## <a name="download-the-samples-and-prepare"></a>下載的範例，並準備

從[Azure 金鑰保存庫用戶端範例頁面](https://www.microsoft.com/en-us/download/details.aspx?id=45343)下載 Azure 金鑰保存庫用戶端範例。

擷取.zip 檔案至您的本機電腦的內容。

閱讀**README.md**檔案 （這是文字檔案），然後依照指示。

## <a name="run-sample-1--hellokeyvault"></a>執行範例 #1--HelloKeyVault
HelloKeyVault 是引導鍵保存庫所支援的金鑰案例的主控台應用程式︰

  1. 建立/匯入的金鑰 （HSM 或軟體鍵）
  2. 加密使用內容索引鍵的密碼
  3. 自動換行鍵保存庫金鑰的內容索引鍵
  4. 列名的內容索引鍵
  5. 解密密碼
  6. 設定密碼

該主控台應用程式應該執行有任何變更，差異在於 App.Config 適當的設定將會更新根據下列步驟︰

1. 更新應用程式中的設定 HelloKeyVault\App.config 用於保存庫 URL、 應用程式原則的識別碼及密碼。 資訊也可以使用**scripts\GetAppConfigSettings.ps1**會產生。
2. 更新中 GetAppConfigSettings.ps1 強制變數的值。
3. 啟動 Windows PowerShell 視窗。
4. PowerShell 視窗中執行的 GetAppConfigSettings.ps1 指令碼。
5. 複製 HelloKeyVault\App.config 檔案的指令碼的結果。


## <a name="next-steps"></a>後續步驟

[部署 VM 使用金鑰保存庫密碼](azure-stack-kv-deploy-vm-with-secret.md)

[部署 VM 鍵保存庫憑證](azure-stack-kv-push-secret-into-vm.md)