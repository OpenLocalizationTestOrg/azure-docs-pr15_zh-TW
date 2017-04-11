<properties
   pageTitle="如何調整 Azure 函數 |Microsoft Azure"
   description="了解 Azure 函數不按比例縮放以符合您事件導向工作負載的需求。"
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure 函數、 函數、 事件處理、 webhooks、 動態計算、 無架構"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>如何調整 Azure 函數

## <a name="introduction"></a>簡介

Azure 函數的優點是在需要時僅使用計算資源。 這表示您沒有支付閒置 Vm 或有保留-當您可能會需要的容量。 不過，在平台配置運算能力，程式碼正在執行，處理負載視縮放，而且當未執行程式碼，然後縮放向下。

這項新功能的機制是動態服務方案。  

本文提供的動態服務方案的運作方式及平台上視需要執行程式碼的縮放比例的概觀。

如果您還不熟悉 Azure 函數，請務必[Azure 函數概觀](functions-overview.md)文章，若要進一步瞭解其功能。

## <a name="configure-azure-functions"></a>設定 Azure 函數

縮放比例與相關兩個主要的設定︰

* [Azure 應用程式服務方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)或動態服務方案
* 執行環境的記憶體大小

函數的成本會根據您所選取的服務方案。 動態服務計劃，成本會是一個函數的執行時間、 記憶體大小和次數。 費用累算只有您的程式碼在執行時。

應用程式服務方案主控您的函數，在現有 Vm，也可能會用來執行其他程式碼。 為這些 Vm 支付每月之後，有不在執行函數的額外收費。

## <a name="choose-a-service-plan"></a>選擇服務方案

當您建立函數時，您可以選取動態服務方案或[應用程式服務方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)上執行。
在應用程式服務計劃，函數會執行專用 VM，就像今天的基本的 web 應用程式工作、 標準、 或進階版 sku 皆可。
此專用的 VM 配置給您的應用程式和功能，一律使用是否程式碼正在執行或不。 如果您的現有、 下利用 Vm 已在執行其他程式碼，或如果您希望持續或幾乎持續執行函數，這是很好的選項。 VM 以減少從執行階段，並記憶體大小的成本。 如此一來，您可以限制成本的一個或多個 Vm 上執行的多個長期函數的成本。

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]
