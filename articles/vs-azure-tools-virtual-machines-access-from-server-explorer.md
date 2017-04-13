<properties
   pageTitle="從伺服器總管存取 Azure 虛擬機器 |Microsoft Azure"
   description="取得概略瞭解如何檢視建立及管理 Azure 虛擬機器 (Vm) 在 Visual Studio 中的伺服器總管] 中。"
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

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>從伺服器總管存取 Azure 虛擬機器

使用伺服器總管 Visual Studio 中，您可以瞭解裝載 Azure 虛擬機器顯示資訊。

## <a name="accessing-virtual-machines-in-server-explorer"></a>存取伺服器總管] 中的虛擬機器

如果您有裝載 Azure 虛擬機器時，您就可以存取這些伺服器總管] 中。 您必須先登入至您訂閱的 Azure 若要檢視您的行動服務。 登入伺服器總管中開啟 Azure 節點的快顯功能表，選擇 [**連線到 Microsoft Azure**。

### <a name="to-get-information-about-your-virtual-machines"></a>若要取得您的虛擬機器資訊

1. 在 [伺服器總管選擇虛擬機器，，然後選擇 F4 鍵以顯示其內容] 視窗。

    下表顯示哪些有內容，但它們是唯讀。 若要變更其，請使用[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。

  	|屬性|描述|
  	|---|---|
  	|DNS 名稱|含有虛擬機器的網際網路位址的 URL。|
  	|環境|虛擬機器，此屬性的值永遠是實際執行。|
  	|名稱|虛擬機器的名稱。|
  	|大小|虛擬機器，會反映的可用的記憶體和磁碟空間量的大小。 如需詳細資訊，請參閱如何︰ 設定虛擬機器大小。|
  	|狀態|值包括開始、 啟動、 停止、 停止和擷取狀態。 如果出現擷取狀態，請的目前狀態為未知。 此屬性的值不同[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)使用的值。|
  	|SubscriptionID|訂閱 ID Azure 帳戶。 檢視訂閱的內容，您可以在[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)上顯示此資訊。|

1. 選擇 [結束點] 節點，然後檢視 [**內容**] 視窗。

1. 下表說明可用的屬性的端點，但它們是唯讀狀態。 若要新增或編輯虛擬機器端點，請使用[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。 

  	|屬性|描述|
  	|---|---|
  	|名稱|端點的識別項。|
  	|私人連接埠|網路存取您的應用程式內部連接埠。|
  	|通訊協定|使用的通訊協定傳輸層這個端點的連接埠。|
  	|公用連接埠|用於公用存取您的應用程式的連接埠。|

## <a name="next-steps"></a>後續步驟

若要進一步瞭解如何在 Visual Studio 中使用 Azure 角色，請參閱[使用遠端桌面 Azure 角色](vs-azure-tools-remote-desktop-roles.md)。
