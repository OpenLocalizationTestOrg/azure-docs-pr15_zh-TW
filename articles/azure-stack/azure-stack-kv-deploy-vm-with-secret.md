<properties
    pageTitle="部署 VM 使用密碼 Azure 堆疊鍵保存庫中儲存 |Microsoft Azure"
    description="瞭解如何部署 VM 使用儲存在 Azure 堆疊鍵保存庫中的密碼"
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

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>部署 VM 擷取金鑰保存庫中的密碼

當您需要將安全值 （例如密碼） 做為參數傳遞部署時，您可以將該值儲存為 Azure 堆疊的重要保存庫中的密碼，並參照其他 Azure 資源管理員範本中的值。 讓私人絕不會公開，您可以包含在範本中的該機密的參照。 您不需要手動輸入的值私人部署資源每次。 您可以指定哪些使用者或服務原則可以存取的機密。

## <a name="reference-a-secret-with-static-id"></a>參照靜態識別碼密碼

您參考的參數檔案，將數值傳遞給您的範本中的密碼。 您可以參考私人傳遞的金鑰保存庫資源識別碼] 和 [機密的名稱。 在此範例中，必須已經存在的金鑰保存庫密碼。 靜態值用於其資源識別碼。

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]接受私人的參數應該*securestring*。

## <a name="next-steps"></a>後續步驟
[部署與索引鍵保存庫範例應用程式](azure-stack-kv-sample-app.md)

[部署 VM 鍵保存庫憑證](azure-stack-kv-push-secret-into-vm.md)

