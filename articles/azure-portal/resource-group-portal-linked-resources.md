<properties 
    pageTitle="[並排顯示] 庫中的連結與相關資源" 
    description="瞭解相關和連結會顯示在 Azure 預覽入口網站的 [並排顯示] 圖庫中的資源。" 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>[並排顯示] 庫中的連結與相關資源

[並排顯示] 圖庫可讓您尋找磚特定的資源，並將其拖曳到您目前的刀。 使用 [並排顯示] 庫，您可以建立橫跨資源的管理檢視。 針對指定的資源，相關的資源包含所有共用資源，為相同的資源群組的資源與資源的連結至或資源。

## <a name="linked-resources-in-azure-resource-manager"></a>連結的資源 Azure 資源管理員中的商務連絡人

連結 」 功能的 Azure 資源管理員。  讓您即使他們不會位於相同的 [資源] 群組中，將資源之間的關聯。 執行階段的資源，不會影響帳單與角色型存取不會影響連結造成任何影響。  它是只是您可以用來代表關聯，以便工具等磚圖庫可提供豐富的管理體驗的機制。  您的工具可以檢查使用連結 API 的連結，並提供自訂關係管理以及體驗。 

## <a name="how-do-i-link-my-resources"></a>我要如何連結我的資源？

當您建立資源透過網站或部署透過 PowerShell 的 Azure 或 Azure CLI 範本時，連結會自動建立一些相關的資源。 使用[連結資源 REST API](https://msdn.microsoft.com/library/azure/mt238499.aspx)或宣告在範本中的關聯，您也以程式設計方式可以連結資源。 使用連結資源的完整討論，請參閱[連結資源 Azure 資源管理員] 中](../resource-group-link-resources.md)。

## <a name="next-steps"></a>後續步驟

- 如果您需要的簡介撰寫 Azure 資源管理員範本，請參閱[撰寫範本](../resource-group-authoring-templates.md)。
- 若要深入瞭解建立資源之間的連結更詳細說明，請參閱[連結資源 Azure 資源管理員] 中](../resource-group-link-resources.md)。
- 若要瞭解更多關於使用透過 [預覽] 入口網站的資源群組，請參閱[使用 Azure 預覽入口網站管理 Azure 資源](resource-group-portal.md)。
