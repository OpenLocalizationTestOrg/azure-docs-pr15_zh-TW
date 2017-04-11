<properties
   pageTitle="檢視與入口網站的部署作業 |Microsoft Azure"
   description="說明如何使用 Azure 入口網站偵測資源管理員部署中的錯誤。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Azure 入口網站檢視部署作業

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

您可以檢視針對透過 Azure 入口網站的部署作業。 您可能會是最感興趣檢視作業，當您收到錯誤部署時，本文著重於檢視作業已失敗。 入口網站提供可讓您輕鬆地找到錯誤，並決定潛在修正介面。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>使用部署作業來疑難排解

若要查看的部署作業，請使用下列步驟︰

1. 資源群組參與的部署，請注意最後一個部署的狀態。 您可以選取此狀態以取得更多詳細資料。

    ![部署狀態](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. 您會看到最近的部署歷程記錄。 選取 [無法部署]。

    ![部署狀態](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. 選取**失敗。如需詳細資訊，請按一下這裡**若要查看的部署失敗的原因描述。 下方圖像中的 DNS 記錄不是唯一的。  

    ![檢視失敗的部署](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    此錯誤訊息應該不足以開始進行疑難排解。 不過，如果您需要更多詳細資料的相關已完成的工作，您可以檢視作業，如下所示。

4. 您可以檢視中的所有部署作業中**部署**刀。 選取 [所有作業，若要查看更多詳細資料]。

    ![檢視作業](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    在此情況下，您會看到儲存帳戶、 虛擬網路及可用性設定已成功建立。 失敗的公用 IP 位址，及其他資源而未嘗試進行。

5. 您可以選取**事件**檢視以供部署的事件。

    ![檢視事件](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. 查看所有的部署事件，然後選取任何項目，如需詳細資訊。

    ![請參閱事件](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>若要疑難排解使用稽核記錄

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署錯誤，請使用下列步驟︰

1. 選取 [**稽核記錄**，以檢視資源群組的稽核記錄。

    ![選取 [稽核記錄](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. 在**稽核記錄**刀中，您會看到最近作業的所有資源群組的摘要，在您的訂閱。 其包含的圖形化表示時間的和狀態一項作業，以及作業的清單。

    ![顯示的動作](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. 您可以篩選您的稽核記錄，將焦點集中在特定條件的檢視。 選取頂端的 [**稽核記錄**刀**篩選**。

    ![篩選記錄](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. 從**篩選**刀中，選取 [只在您想要查看的作業，限制檢視稽核記錄檔的條件]。 例如，您可以篩選，僅顯示 [資源群組錯誤的作業。

    ![設定篩選選項](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. 您可以設定的時間範圍，進一步篩選作業。 下列圖像篩選檢視特定的 20 分鐘時段。

    ![設定的時間](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. 您可以在清單中選取任何一項作業。 挑選包含您想要研究的錯誤的作業。

    ![選取作業](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. 您會看到所有該作業的事件。 請注意**相互關聯識別碼**的摘要中。 此 ID 用來追蹤相關的事件。 使用技術支援，以疑難排解問題時，它可以很有幫助。 您可以選取任何事件，查看該事件詳細資料。

    ![選取 [事件]](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. 您會看到事件詳細資料。 特別是，請注意**屬性**的錯誤的相關資訊。

    ![顯示稽核記錄檔詳細資料](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

您想要的稽核記錄檔以套用篩選，保留的下次當您檢視，因此您可能需要變更這些值，擴大作業的檢視。

## <a name="next-steps"></a>後續步驟

- 解決特定部署錯誤的說明，請參閱[部署 Azure 與 Azure 資源管理員資源時的常見錯誤的解決方法](resource-manager-common-deployment-errors.md)。
- 若要瞭解如何使用稽核記錄監控其他類型的動作，請參閱[稽核作業與資源管理員](resource-group-audit.md)。
- 若要驗證您的部署之前執行，請參閱[部署 Azure 資源管理員範本的資源群組](resource-group-template-deploy.md)。
