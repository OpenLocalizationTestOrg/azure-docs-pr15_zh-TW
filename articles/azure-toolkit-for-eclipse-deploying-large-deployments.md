<properties
    pageTitle="部署大型部署"
    description="瞭解如何部署大型的部署使用蝕 Azure 工具組。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>部署大型部署 #

如果您的部署太大包含預設 approot 資料夾中，您可以使用本機存放區資源作為部署根資料夾的您 JDK 和應用程式伺服器。

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>部署根資料夾中使用本機存放區的資源，為大型部署 ##

1. 建立新的本機存放區資源。 資源的名稱並不重要。 儲存資源是層級角色所定義。 若要存取本機存放裝置設定] 對話方塊，您可以建立新的本機存放區資源，最快速的方法是使用下列步驟︰ 以滑鼠右鍵按一下 [**專案總管**] 檢視中的角色 （您 Azure 專案節點展開如果您沒有看到該角色），按一下**Azure**，然後按一下 [**本機存放區**。 [**本機存放區**] 對話方塊中按一下 [**新增**]，以建立新的本機存放區資源]。
1. 至少 2048 mb （任何項目較不可能會造成相同的檔案大小的問題時，也會遭遇 approot） 設定所要的大小。
1. 確認已**清除內容的角色執行個體回收時**核取;這有助於防止部署的啟動邏輯回收的角色執行個體時，發生的衝突的資源已存在的檔案。
1. 確定**儲存之後部署資源的目錄路徑的環境變數**值會設定為字串**DEPLOYROOT**。 本機存放區的 [資源] 對話方塊看起來類似下列項目。
    ![][ic667943]

或者，如果**DEPLOYROOT**作為您的本機資源*名稱*，而且您不會變更自動產生的環境變數名稱 （將**DEPLOYROOT_PATH**在此情況下），就能運作的應用程式以及。

其他資訊建立本機存放區資源位於[本機存放區內容][]。

## <a name="see-also"></a>另請參閱 ##

[針對蝕 azure 工具組][]

[建立 Azure 中蝕認識全球應用程式][]

[安裝蝕 Azure 工具組][] 

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546
[本機存放區內容]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png
