<properties
    pageTitle="以 Azure 堆疊的 Visual Studio 部署範本 |Microsoft Azure"
    description="瞭解如何以 Azure 堆疊的 Visual Studio 部署範本。"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>部署 Azure 堆疊使用 Visual Studio 中的範本

使用 Visual Studio 部署 Azure 堆疊 POC Azure 資源管理員範本。

資源管理員範本部署，並提供您的應用程式，在單一、 協調運算中的所有資源。

1.  開啟 Visual Studio 2015 更新 1。

2.  按一下 [**檔案]**，按一下 [**新增**]，在**新的專案**] 對話方塊中按一下 [ **Azure 資源群組**。

3.  新的專案中，輸入**名稱**，然後按一下**[確定]**。

4.  在**選取 Azure 範本**] 對話方塊中，按一下**Windows 虛擬機器**]，然後再按一下**[確定]**。

  在新的專案中，展開在**方案總管]**窗格中的 [**範本**] 節點可以看到可用的範本的清單。

5.  在**方案總管**] 窗格中，以滑鼠右鍵按一下您的專案名稱，按一下 [**部署**，然後按一下**新的部署**。

6.  在 [**部署至 [資源群組**] 對話方塊的 [在 [**訂閱**] 下拉式清單，選取您的 Microsoft Azure 堆疊訂閱。

7.  在 [**資源群組**] 清單中，選擇現有的資源群組或建立新的範本。

8.  在 [**資源] 群組中的位置**] 清單中，選擇一個位置，然後再按一下 [**部署**。

9.  在 [**編輯參數**] 對話方塊中，輸入值參數 （依範本而定），，然後按一下 [**儲存**。

## <a name="next-steps"></a>後續步驟

[命令列以部署範本](azure-stack-deploy-template-command-line.md)
