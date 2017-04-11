<properties
    pageTitle="自動調整入口網站中的雲端服務 |Microsoft Azure"
    description="（傳統）瞭解如何使用自動小數位數的設定規則的雲端服務 web 角色或工作者角色 Azure 中的 [傳統] 入口網站。"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>如何自動調整大小雲端服務

> [AZURE.SELECTOR]
- [Azure 入口網站](cloud-services-how-to-scale-portal.md)
- [Azure 傳統入口網站](cloud-services-how-to-scale.md)

Azure 傳統入口網站的 [縮放比例] 頁面中，您可以手動調整您的網頁角色或背景工作的角色，或您可以啟用根據 CPU 負載或訊息佇列自動縮放比例。

>[AZURE.NOTE] 本文著重於雲端服務 web 及工作者角色。 當您直接建立虛擬機器 （傳統） 時，它會裝載雲端服務中。 這項資訊部分適用於下列類型的虛擬機器。 按比例縮放的虛擬機器可用性設定其實正在進行開啟和關閉根據您設定的縮放比例規則。 如需有關虛擬機器和可用性集的詳細資訊，請參閱[管理可用性虛擬機器](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

設定應用程式的縮放比例之前，您應該考慮下列資訊︰

- 縮放比例，並受到核心使用方式。 較大的角色執行個體中使用更多核心。 您可以調整只在核心的限制內的應用程式，您的訂閱。 例如，如果您的訂閱有二十核心和您與調整大小的兩個中執行的應用程式的限制雲端服務 （總計的四個核心），您可以只縮放設定其他雲端服務部署在您的訂閱 16 個核心。 大小的詳細資訊，請參閱[雲端服務的大小](cloud-services-sizes-specs.md)。

- 您必須建立佇列，並將它與關聯角色之前，您可以擴充郵件閥值為基礎的應用程式。 如需詳細資訊，請參閱[如何使用佇列中儲存服務](../storage/storage-dotnet-how-to-use-queues.md)。

- 您可以調整連結至您的雲端服務的資源。 如需有關連結資源的詳細資訊，請參閱[如何︰ 連結至雲端服務的資源](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service)。

- 若要啟用您的應用程式的可用性，您應該確認部署與兩個或多個角色執行個體。 如需詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)。



## <a name="schedule-scaling"></a>排程縮放比例

根據預設，所有的角色未遵循特定的排程。 因此，變更任何設定套用至所有的時間和所有工作日年。 如果您想，您可以設定手動或自動的比例︰

- 包含工作日
- 週末
- 週住宿
- 週上午
- 特定的日期
- 特定的日期範圍

這是 conigured 在[Azure 傳統入口網站](https://manage.windowsazure.com/)上  
**雲端服務** > **\[雲端服務\]** > **比例** > **\[生產或臨時\]**頁面。

按一下您想要變更每一個角色**設定排程時間**] 按鈕。

![自動雲端服務的縮放比例根據排程][scale_schedules]



## <a name="manual-scale"></a>手動縮放比例

在 [**縮放比例**] 頁面中，您可以手動增加或減少的雲端服務中執行執行個體數目。 如果您還沒有建立排程，這被設定為您建立的每個排程或所有的時間。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [**雲端服務**]，然後按一下雲端服務，以開啟儀表板的名稱。

    > [AZURE.TIP] 如果您沒有看到您的雲端服務，您可能需要變更從**生產**到**臨時**或反向操作。

2. 按一下 [**縮放]**。

3. 選取您想要變更縮放比例選項的排程。 預設值為*無排定的時間*如果您不有任何已定義的排程。

4. 尋找 [**公制小數位數**] 區段，然後選取**NONE**。 這是所有角色的預設設定。

5. 每個雲端服務中的角色具有滑桿，以變更使用的執行個體數目。

    ![手動調整雲端服務角色][manual_scale]

    如果您需要更多的執行個體，您可能需要變更[雲端服務的虛擬機器大小](cloud-services-sizes-specs.md)。

6. 按一下 [**儲存**]。  
角色執行個體會新增或移除根據您的選擇。

>[AZURE.TIP] 每當您看到![][tip_icon]將滑鼠移至該，您可以取得相關的特定設定會說明。


## <a name="automatic-scale---cpu"></a>自動縮放比例-CPU

如果平均的 CPU 使用率百分比在上方或下方指定臨界值; 此縮放的比例建立或刪除角色執行個體。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [**雲端服務**]，然後按一下雲端服務，以開啟儀表板的名稱。

    > [AZURE.TIP] 如果您沒有看到您的雲端服務，您可能需要變更從**生產**到**臨時**或反向操作。

2. 按一下 [**縮放]**。

3. 選取您想要變更縮放比例選項的排程。 預設值為*無排定的時間*如果您不有任何已定義的排程。

4. 尋找 [**公制小數位數**] 區段，然後選取**[CPU**。

5. 現在您可以設定最小值和最大範圍的角色執行個體、 目標 CPU 使用率 （若要設定觸發小數位數） 及縮放向上和向下的執行個體數目。

![縮放 cpu 負載雲端服務角色][cpu_scale]

>[AZURE.TIP] 每當您看到![][tip_icon]將滑鼠移至該，您可以取得相關的特定設定會說明。





## <a name="automatic-scale---queue"></a>自動縮放比例-佇列中

此自動調整如果佇列中的郵件數在上方或下方指定臨界值;建立或刪除角色執行個體。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [**雲端服務**]，然後按一下雲端服務，以開啟儀表板的名稱。

    > [AZURE.TIP] 如果您沒有看到您的雲端服務，您可能需要變更從**生產**到**臨時**或反向操作。

2. 按一下 [**縮放]**。

3. 尋找 [**公制小數位數**] 區段，然後選取**[CPU**。

4. 現在您可以設定最小值和最大範圍的角色執行個體，佇列中的訊息和數量佇列中處理的每個執行個體，然後向上和向下縮放的執行個體數目。

![不按比例縮放的訊息佇列中的雲端服務角色][queue_scale]

>[AZURE.TIP] 每當您看到![][tip_icon]將滑鼠移至該，您可以取得相關的特定設定會說明。


## <a name="scale-linked-resources"></a>不按比例縮放連結的資源

通常您不按比例縮放的角色，時不按比例縮放的資料庫，應用程式使用也很有幫助。 如果您連結到雲端服務的資料庫，您可以按一下適當的連結來存取該資源的縮放比例設定。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [**雲端服務**]，然後按一下雲端服務，以開啟儀表板的名稱。

    > [AZURE.TIP] 如果您沒有看到您的雲端服務，您可能需要變更從**生產**到**臨時**或反向操作。

2. 按一下 [**縮放]**。

3. 尋找**連結的資源**] 區段，並按一下 [**管理此資料庫的縮放比例**。

    > [AZURE.NOTE] 如果您沒有看到**連結的資源**節，您可能沒有任何連結的資源。

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
