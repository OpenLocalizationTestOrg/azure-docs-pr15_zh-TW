<properties
   pageTitle="使用 Amazon Web 服務設定驗證 |Microsoft Azure"
   description="本文將說明如何建立和驗證在 Azure 自動化管理 AWS 資源 runbooks AWS 認證。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="aws 驗證設定 aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>驗證 Runbooks 與 Amazon Web 服務
自動化資源 Amazon Web 服務 (AWS) 中的一般工作可使用自動化 runbooks Azure 中完成。  您可以自動化 AWS 使用自動化 runbooks 就像您可以使用 Azure 中的資源中的許多工作。  所有所需的兩個動作︰

* AWS 訂閱及一組認證。  特別是您 AWS 便捷鍵和私密金鑰。  如需詳細資訊，請檢閱的文件[使用 AWS 認證](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)。
* Azure 訂閱和自動化帳戶。  如需有關 Azure 自動化帳戶設定的詳細資訊，請檢閱的文件[設定 Azure 執行為帳戶](../automation/automation-sec-configure-azure-runas-account.md)。  

驗證方法 AWS，您必須指定 AWS 認證，以驗證您 runbooks 執行從 Azure 自動化一組。 如果您已建立的自動化帳戶，而且您想要使用的驗證方法 AWS，您可以遵循下一節中的步驟進行。  如果您想要專用 runbooks 以 AWS 資源的帳戶，您應該先建立新的[自動化執行為帳戶](../automation/automation-sec-configure-azure-runas-account.md)（略過建立服務主要的選項），然後遵循下列步驟。

## <a name="configure-automation-account"></a>設定自動化帳戶
Azure 自動化與 AWS 通訊，您必須擷取您 AWS 的認證，並將其儲存為 Azure 自動化資產。  執行下列步驟中 AWS 文件[AWS 帳戶管理便捷鍵](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)以建立便捷鍵並複製**存取鍵識別碼**和**密碼便捷鍵**（您也可以下載您索引鍵的檔案，以將其安全儲存）。

建立並複製 AWS 安全性索引鍵後，您必須使用安全地將其儲存，並使用您 runbooks 參考這些 Azure 自動化帳戶建立認證資產。  請依照 [**建立新的認證**] 區段中的[Azure 自動化認證資產](../automation/automation-certificates.md/###To create a new credential with the Azure portal)的文件中的步驟，並輸入下列資訊︰

1. 在 [**名稱**] 方塊中，輸入**AWScred**或追蹤您命名標準適當的值。  
2. 在 [**使用者名稱**] 方塊中輸入您**存取識別碼**和您在**密碼**並**確認密碼**] 方塊中的**[私人便捷鍵**。   

## <a name="next-steps"></a>後續步驟

- Reivew 解決方案文章[自動化部署 VM Amazon Web 服務中](../automation/automation-scenario-aws-deployment.md)，瞭解如何建立 runbooks 自動化 AWS 中的工作。
