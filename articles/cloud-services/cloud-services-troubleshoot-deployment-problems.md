<properties
 pageTitle="雲端服務部署問題進行疑難排解 |Microsoft Azure"
 description="有一些常見部署 Azure 雲端服務時可能遇到的問題。 本文提供將其中一些的解決方案。"
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>雲端服務部署問題進行疑難排解

在雲端服務應用程式套件部署至 Azure 時，您可以從 Azure 入口網站中的 [**屬性**] 窗格中取得部署相關的資訊。 您可以使用詳細資料窗格中，協助您疑難排解問題雲端服務，然後開啟新的支援要求時，您可以為 Azure 支援提供這項資訊。

您可以找到 [**屬性**] 窗格，如下所示︰

* 在 Azure 入口網站中，按一下 [您的雲端服務部署**所有設定**]，然後按一下然後按一下 [**內容**。
* 在 Azure 傳統的入口網站中，按一下 [部署您的雲端服務，然後按一下**儀表板**，位於右下角的頁面 （下**快速檢視**）。 請注意此] 窗格上沒有 [內容] 標籤。

> [AZURE.NOTE] 您可以複製到剪貼簿 [**屬性**] 窗格的內容，按一下窗格右上角的圖示。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>問題︰ 我無法存取我的網站，但我部署開始與角色的所有執行個體準備

顯示在入口網站的網站 URL 連結不包含連接埠。 網站的預設連接埠是 80。 如果您的應用程式設定成不同的連接埠中執行，您必須新增正確的連接埠號碼 URL 存取網站時。

1. 在 Azure 入口網站中，按一下 [您的雲端服務的部署]。
2. 在 [Azure 入口網站的 [**屬性**] 窗格中，核取 （在下**輸入結束點**） 角色執行個體的連接埠。
3. 如果連接埠不 80，新增正確的連接埠值 url 時存取應用程式。 若要指定非預設連接埠，請輸入 URL，後面接著冒號 （:），後面接著的連接埠號碼，不加空格。

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>問題︰ 我角色執行個體回收不我執行任何項目

當 Azure 偵測到問題節點，因此會移至新的節點的角色執行個體治療服務會自動出現。 在這種情況下，您可能會看到自動回收角色執行個體。 若要瞭解是否服務治療發生︰

1. 在 Azure 入口網站中，按一下 [您的雲端服務的部署]。
2. Azure 入口網站的 [**屬性**] 窗格中，在檢閱資訊，並決定是否服務治療時發生觀察回收的角色的時間。

角色會也回收大約一次每月期間主機 OS 和來賓 OS 更新。  
如需詳細資訊，請參閱部落格文章[角色執行個體重新啟動到期 OS 升級](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>問題︰ 我無法執行 VIP 交換和收到錯誤訊息

如果正在進行部署更新，就不會允許 VIP 交換。 部署更新會自動時︰

* 新的來賓作業系統可供使用，您已設定自動更新。
* 服務治療就會發生。

若要瞭解是否自動更新，所以您無法執行 VIP 交換︰

1. 在 Azure 入口網站中，按一下 [您的雲端服務的部署]。
2. 在 [Azure 入口網站的 [**屬性**] 窗格中，查看**狀態**的值。 如果已**準備好**，請檢查以查看如果其中一個最近發生的**最後一個作業**可能會導致無法 VIP 交換。
3. 針對實際部署重複步驟 1 和 2。
4. 如果程序中，有的自動更新，請等候，即可完成之前，先執行 VIP 交換。

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>問題︰ 角色執行個體迴圈之間入門與初始化、 忙碌，停止

這種情況可能表示您的應用程式碼、 套件或設定檔案有問題。 在此情況下，您應該能夠看到變更每隔幾分鐘的狀態和 Azure 入口網站可能會說**回收**、**忙碌**或**初始化**等。 這表示已發生某些錯誤執行保持角色執行個體的應用程式。

如需有關如何疑難排解這個問題的詳細資訊，請參閱部落格文章[Azure PaaS 計算診斷資料](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)和[常見的問題，導致回收的角色](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)。

## <a name="problem-my-application-stopped-working"></a>問題︰ 我的應用程式已停止運作

1. 在 Azure 入口網站中，按一下 [角色執行個體。
2. 在 [Azure 入口網站的 [**屬性**] 窗格中，請考慮下列條件，以解決您的問題︰
   * 如果角色執行個體最近已停止 （您可以檢查的**中止計算**值），請更新部署。 若要查看的角色執行個體是否繼續自行運作，請等候。
   * 如果角色執行個體**忙碌**，請檢查您的應用程式程式碼，請參閱如果處理[StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck)事件。 您可能需要新增或修正處理此事件的部分程式碼。
   * 可幫助診斷資料和[Azure PaaS 計算診斷資料](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)部落格文章的疑難排解案例。

>[AZURE.WARNING] 如果您回收您雲端服務，您重設內容部署，有效清除原始問題的資訊。

## <a name="next-steps"></a>後續步驟

檢視更多[疑難排解文章](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services)雲端服務。

若要瞭解如何使用 Azure PaaS 電腦診斷資料，即可疑難排解在雲端服務角色問題，請參閱[Kevin Williamson 部落格系列](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)。
