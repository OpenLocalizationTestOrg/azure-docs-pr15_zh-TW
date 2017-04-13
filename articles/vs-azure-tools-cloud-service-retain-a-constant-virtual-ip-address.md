<properties
   pageTitle="如何保留在雲端服務的常數虛擬 IP 位址 |Microsoft Azure"
   description="瞭解如何確保您 Azure 雲端服務的虛擬 IP 位址 (VIP) 不會變更。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>若要保留在雲端服務的常數虛擬 IP 位址的方式

當您更新裝載於 Azure 雲端服務時，您可能需要確認服務的虛擬 IP 位址 (VIP) 不會變更。 多個網域管理服務使用的網域名稱系統 (DNS) 的註冊網域名稱。 DNS 只有 VIP 保持不變。 您可以使用**發佈精靈**Azure 工具] 中，以確保在更新後，不會變更您的雲端服務 VIP。 如需有關如何使用雲端服務的 DNS 網域管理的詳細資訊，請參閱[設定 Azure 雲端服務的自訂網域名稱](./cloud-services/cloud-services-custom-domain-name.md)。

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>發佈雲端服務，而不變更其 VIP

當您第一次將其部署到 Azure 在特定的環境中，例如生產環境時，會配置雲端服務的 VIP。 除非您明確刪除部署或部署更新程序隱含刪除，不會變更 VIP。 若要保留 VIP，絕對不能刪除您的部署，您也必須確定 Visual Studio 不會自動刪除您的部署。 您可以控制行為部署設定中指定**發佈精靈**，可支援幾個部署選項。 您可以指定新的部署或更新的部署，它可以是累加或同時，而這兩種更新部署保留 VIP。 如需定義的不同類型的部署，請參閱[發佈 Azure 應用程式精靈](vs-azure-tools-publish-azure-application-wizard.md)。  此外，您可以控制是否要在發生錯誤時，刪除之前的部署的雲端服務。 如果您沒有正確設定的選項，可能會意外變更 VIP。

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>若要更新雲端服務，而不變更其 VIP

1. 您至少一次部署您的雲端服務之後，開啟 Azure 專案，節點的快顯功能表，然後選擇 [**發佈]**。 **發佈 Azure 應用程式**精靈會出現。

1. 在訂閱清單中，選擇您要部署的項目，然後選擇 [**下一步**] 按鈕。 在精靈的 [**設定**] 頁面隨即出現。

1. 在 [**一般設定**] 索引標籤中，確認雲端服務，您部署，**環境**、**建立設定**，以及**服務設定**的名稱正確無誤所有。

1. 在 [**進階設定]**索引標籤中，確認儲存帳戶和部署標籤是正確、 的**刪除失敗的部署**核取方塊，以及已**部署**更新核取方塊。 選取 [**部署**更新] 核取方塊，您可以確保不會刪除您的部署，並重新發佈應用程式時，您 VIP 不會遺失。 清除**刪除部署錯誤] 核取方塊**，您可以確保您 VIP 部署時，發生錯誤時將不會遺失。

1. 若要進一步指定您想要更新的角色的方式，請選擇 [**部署的更新**] 方塊旁的 [**設定**] 連結，然後選擇的累加更新 」 或 「 同時更新] 選項**部署更新**的 [設定] 對話方塊中。 如果您選擇累加更新，更新每個執行個體一個接一個，讓應用程式，隨時可使用。 如果您選擇同時更新，同時更新所有執行個體。 同時更新較快，但您的服務可能無法使用更新程序期間。

1. 當您滿意您的設定時，選擇 [**下一步**] 按鈕。

1. 在精靈的 [**摘要**] 頁面中，確認您的設定，然後選擇 [**發佈**] 按鈕。

  >[AZURE.WARNING] 如果部署失敗，您應該位址失敗的原因，並重新部署，以避免損毀的狀態離開您的雲端服務。

## <a name="next-steps"></a>後續步驟

若要從 Visual Studio 深入瞭解發佈 Azure，請參閱[發佈 Azure 應用程式精靈](vs-azure-tools-publish-azure-application-wizard.md)。
