<properties
pageTitle="使用 Oracle VM 映像的考量 |Microsoft Azure"
description="部署前，請瞭解有關支援的設定為在 Azure 中的 Windows Server 上 Oracle VM。"
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>其他考量 Oracle 虛擬機器圖像



本文涵蓋 Oracle 虛擬機器中 Azure 根據 Oracle 軟體圖像 Oracle 所提供的考量。  

-  Oracle 資料庫虛擬機器圖像
-  Oracle WebLogic 伺服器虛擬機器圖像
-  Oracle JDK 虛擬機器圖像

##<a name="oracle-database-virtual-machine-images"></a>Oracle 資料庫虛擬機器圖像
### <a name="clustering-rac-is-not-supported"></a>不支援叢集 (RAC)

Azure 目前不支援 Oracle 實數應用程式叢集 (RAC) 的 Oracle 資料庫。 可能會出現只 Oracle 資料庫的獨立執行個體。 這是因為 Azure 目前不支援虛擬磁碟共用多個虛擬機器執行個體之間的讀/寫方式。 多點的 UDP 也不支援。

### <a name="no-static-internal-ip"></a>沒有內部的靜態 IP

Azure 指派每一個虛擬機器內部 IP 位址。 除非虛擬機器是虛擬網路的一部分，虛擬機器的 IP 位址是動態，且虛擬機器重新啟動後，可能會變更。 這可能會導致問題，因為 Oracle 資料庫預期的靜態 IP 位址。 若要避免此問題，請考慮新增虛擬機器 Azure 虛擬網路。 如需詳細資訊，請參閱[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)和[建立虛擬網路 Azure 中](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

### <a name="attached-disk-configuration-options"></a>附加的磁碟設定選項

[作業系統磁碟上的虛擬機器或附加在磁碟上，也稱為資料磁碟，您可以將資料檔案。 連結的磁碟可能會提供更佳的效能和大小彈性比作業系統磁碟。 作業系統磁碟可能會比僅適用於資料庫底下 10 gb。

連結的磁碟依賴 Azure Blob 儲存體服務。 每個磁碟都能夠秒 (IOPS) 的約 500 個輸入輸出作業理論最大值。 連結的磁碟的效能可能並非最佳一開始，且 IOPS 效能可能會大幅改善一段 「 燒錄增益集 」 60 90 分鐘的連續的作業。 如果磁碟會維持閒置，IOPS 效能可能會降低到另一個燒錄在一段連續的作業。 簡言之，更多的作用中的磁碟，很可能會最佳 IOPS 效能的方法。

雖然最簡單的方法是將單一磁碟附加到虛擬機器，並將該磁碟上的資料庫檔案，此方法也會限制龐大的效能。 不過，您通常可以改善有效 IOPS 效能，如果您使用多個連結的磁碟、 分散，在資料庫的資料，然後使用 [Oracle 自動儲存空間管理 (ASM)。 如需詳細資訊，請參閱[Oracle 自動儲存空間的概觀](http://www.oracle.com/technetwork/database/index-100339.html)。 雖然您可以使用多個磁碟的作業系統層級，因為不知道以提升 IOPS 效能，就不會建議的方法。

請考慮附加根據您是否要排列優先順序讀取作業的效能或撰寫您的資料庫作業的多個磁碟的兩種不同的方法︰

- **Oracle ASM 自己**有可能導致寫入作業效能，但糟 IOPS 讀取作業與使用磁碟陣列的方式。 下圖邏輯描述這樣的安排中。  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] 評估取捨寫入效能及讀取-案例為基礎的效能。 當您使用此而有所不同您實際的結果。

### <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和損毀的復原考量

使用時 Oracle 資料庫 Azure 虛擬機器中，您負責實作避免任何停機時間高可用性和損毀修復解決方案。 您也會負責備份資料與應用程式。

Oracle 資料庫企業版 （不含 RAC) 上 Azure 的高可用性和損毀復原，可以使用兩個不同的虛擬機器中的兩個資料庫中的[資料保護、 作用中的資料保護](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)] 或 [ [Oracle 金門](http://www.oracle.com/technetwork/middleware/goldengate)，達成。 兩個虛擬機器應該相同的[雲端服務](virtual-machines-linux-classic-connect-vms.md)和相同的[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)中，確保他們可以存取彼此移到私人常設 IP 位址。  此外，建議將虛擬機器放在同一個[顯示狀態設定](virtual-machines-windows-manage-availability.md)為允許 Azure 放置到另一個錯誤網域及升級網域。 只在相同的雲端服務中的虛擬機器可以參與相同的可用性設定。 每個虛擬機器必須至少 2 GB 的記憶體和 5 GB 的磁碟空間。

可以使用一個虛擬機器，另一個的虛擬機器中的次要 （備用） 資料庫的主要資料庫達到高的可用性與 Oracle 資料防衛隊，並設定它們之間的單向複寫。 結果為資料庫複本的 「 讀取 」 權限。 您可以使用 Oracle GoldenGate 設定兩個資料庫之間的雙向複寫。 若要瞭解如何設定高可用性解決方案的資料庫使用這些工具，請參閱[使用中的資料保護](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html)及[GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) Oracle 網站的文件。 如果您需要讀取寫入的 access 資料庫的複本，您可以使用[Oracle 作用中的資料保護](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic 伺服器虛擬機器圖像

-  **叢集支援企業版只。** 您使用 WebLogic 叢集只有時使用 WebLogic Server 企業版的授權。 不要使用叢集與 WebLogic Server 標準版。

-  **連線逾時︰**如果您的應用程式需要連線至公用端點的另一個 Azure 雲端服務 （例如資料庫層服務），Azure 可能四個閒置數分鐘後關閉這些開啟的連線。 這可能會影響功能和依賴連接共用的應用程式，因為連線的更多的限制了停用可能不會再保留有效。 如果這會影響您的應用程式，請考慮啟用連線集區上的 「 保持 」 邏輯。

    端點*內部*Azure 雲端服務 （例如*相同*的雲端服務，為您 WebLogic 虛擬機器中獨立資料庫虛擬機器） 部署時，然後連線是直接和 Azure 負載平衡器，不需要使用，因此不需連線逾時。

-  **不支援 UDP 多點傳送。** Azure 支援 UDP 單，但不是點或廣播。 能夠依賴 Azure UDP 單功能 WebLogic 伺服器。 為獲得最佳結果依賴 UDP 單中，我們建議您 WebLogic 叢集大小會保留靜態，或與叢集中包含的受管理伺服器不超過 10 會保留。

-  **WebLogic 伺服器預期公開及私密金鑰 （例如，使用企業 Javabean） T3 存取相同的連接埠。** 請考慮多層情況 WebLogic 伺服器叢集包含兩個或多個受管理伺服器名稱為**SLWLS**雲端服務中執行服務層級 (EJB) 應用程式的位置。 用戶端層是不同的雲端服務中執行簡單的 Java 程式試著在服務層呼叫 EJB。 因為必要負載平衡服務圖層時，您必須建立 WebLogic 伺服器叢集虛擬機器公用負載平衡結束點。 如果您指定的端點的私人連接埠是公用連接埠 (例如，7006:7008) 不同，會發生下列錯誤︰

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    這是因為任何遠端 T3 存取 WebLogic 伺服器預期的負載平衡器連接埠和相同的受管理的 WebLogic 伺服器連接埠。 在上述情況中，用戶端存取連接埠 7006 （負載平衡器連接埠），及受管理的伺服器接聽 7008 （私人的連接埠）。 這項限制是僅適用於 T3 存取權，而不 HTTP。

    若要避免此問題，請使用下列因應措施其中一項︰

    -  使用相同的私人及公用的連接埠號碼 T3 存取專用的負載平衡端點。

    -  啟動 WebLogic 伺服器時，包括下列 JVM 參數︰

            -Dweblogic.rjvm.enableprotocolswitch=true

如需相關資訊，請參閱在<http://support.oracle.com>的 KB 文章**860340.1** 。

-  **動態叢集和負載平衡限制。** 假設您想要使用 WebLogic Server 中的動態叢集，並將它公開透過單一、 公用負載平衡端點 Azure 中。 這可以只要您每個受管理的伺服器 （未指派的動態從範圍） 使用固定的連接埠號碼，並不會啟動其他比機器管理員追蹤受管理的伺服器 （也就是不超過一個受管理的每個虛擬機器伺服器）。 當您設定的結果比虛擬機器所啟動的更多 WebLogic 伺服器 （也就是位置多 WebLogic 伺服器執行個體共用相同的虛擬機器），然後不能超過其中一個這些繫結至指定的連接埠號碼 WebLogic Server 伺服器執行個體 – 該虛擬機器上其他會失敗。

    相反地，如果您設定自動將唯一的連接埠號碼指派給受管理的伺服器管理員伺服器，然後負載平衡不可能因為 Azure 時需要此設定不支援對應到多個私人的連接埠、 單一公用連接埠。

-  **虛擬機器 Weblogic 伺服器的多個執行個體。** 根據您的部署需求，您可以考慮執行相同的虛擬機器中的多個 WebLogic 伺服器執行個體的選項，如果虛擬機器夠大。 例如大小中等虛擬機器，其中包含兩個核心，您可以選擇執行兩個 WebLogic 伺服器執行個體。 請注意，但我們還是建議您避免將您的結構，就會大小寫，如果您是使用只要執行多個 WebLogic 伺服器執行個體的虛擬機器介紹單點失敗。 使用兩個以上的虛擬機器可能更好的方法，每個這些虛擬機器者可以執行多個 WebLogic 伺服器執行個體。 每個這些 WebLogic 伺服器執行個體可能仍相同叢集的一部分。 請注意，不過，不目前可能使用 Azure 這類 WebLogic 伺服器部署相同的虛擬機器中所公開的負載平衡端點，因為 Azure 負載平衡器需要負載平衡伺服器分配給唯一的虛擬機器。

##<a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虛擬機器圖像

-  **JDK 6 和 7 最新的更新。** 雖然我們建議您使用最新公用、 支援的版本 Java (目前 Java 8)，Azure 也讓 JDK 6 和 7 的圖像。 這是尚未準備好升級至 JDK 8 的舊版應用程式。 上一個 JDK 圖像的更新可能不會使用一般大眾，指定 Microsoft 與的合作關係 Oracle，而 JDK 6 和 7 Azure 所提供的圖像是包含較新的非公用更新以正常方式由 Oracle 所提供 Oracle 的支援客戶選取群組。 JDK 圖像的最新版本將可供一段時間的 JDK 6 和 7 更新版本。

    適用於此 JDK 6 和 7 圖像和虛擬機器和圖像，衍生 JDK 僅可用於 Azure。

-  **64 位元 JDK。** Oracle WebLogic 伺服器虛擬機器圖像和 Azure 所提供的虛擬機器 Oracle JDK 圖像包含 Windows Server 與 JDK 的 64 位元版本。

##<a name="additional-resources"></a>其他資源
[Oracle Azure 虛擬機器圖像](virtual-machines-linux-classic-oracle-images.md)
