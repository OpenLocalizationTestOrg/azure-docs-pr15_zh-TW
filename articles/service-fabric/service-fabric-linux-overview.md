<properties
   pageTitle="Azure 服務 linux 布料的轉印圖樣 |Microsoft Azure"
   description="服務布料的轉印圖樣叢集支援 Linux 和 Java，這表示您就可以部署及主機服務布料的轉印圖樣應用程式撰寫 linux Java 及 C#。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>服務 linux 布料的轉印圖樣

服務 linux 布料的轉印圖樣預覽可讓您建立、 部署及管理 linux 可用性、 彈性的應用程式，就像 Windows 上一樣。 服務結構架構 （可靠的服務與可靠的動作項目），可 java linux 除了 C# （.NET 核心）。  您也可以建立[來賓可執行服務](service-fabric-deploy-existing-app.md)的任何語言或架構。 此外，預覽也支援來協調 Docker 容器。 來賓可執行檔或原生服務布料的轉印圖樣服務，使用服務的結構架構，可以執行 docker 容器。

服務 linux 布料的轉印圖樣等於概念 Windows 上的服務結構 （除了 OS 細節和程式設計語言支援）。 因此，能夠協助您熟悉技術適用於大部分的問題的[現有的文件](http://aka.ms/servicefabricdocs)。

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>支援的作業系統與程式設計語言

有限的預覽支援建立的一個方塊開發叢集多機器叢集除了執行 Ubuntu 伺服器 16.04 Azure 中。 預覽支援可靠的動作項目及可靠的無狀態服務架構 Java 及 C# 除了來賓可執行檔，來協調 Docker 容器。  

>[AZURE.NOTE] 可靠的集合中 Linux 尚未支援。 不支援販賣攤單獨叢集 [-只有一個方塊和 Azure Linux 多機器叢集支援的預覽。

## <a name="supported-tooling"></a>支援的工具

預覽支援透過 Azure CLI 叢集互動。 Java 開發人員，整合蝕與 Yeoman 隨附蝕支援 Linux 及 OSX。 OSX 整合使用 Linux VM vagrant 透過顯示進階設定。 C# 開發人員，與 Yeoman 整合提供產生應用程式範本。

## <a name="next-steps"></a>後續步驟


1. 熟悉[可靠的動作項目](service-fabric-reliable-actors-introduction.md)和[可靠的服務](service-fabric-reliable-services-introduction.md)的程式設計架構。

2. [在 Linux 開發環境準備作業](service-fabric-get-started-linux.md)

3. [OSX 的開發環境準備作業](service-fabric-get-started-mac.md)

4. [Linux 上建立您的第一個服務布料的轉印圖樣 Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
