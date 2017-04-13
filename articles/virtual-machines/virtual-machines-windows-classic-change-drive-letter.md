<properties
    pageTitle="讓 d 磁碟機，VM 資料磁碟的 |Microsoft Azure"
    description="說明如何變更 Windows vm 的磁碟機字母，使您可以使用 d 磁碟機做為資料磁碟機。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>在 Windows VM 資料磁碟機 d 磁碟機作為 

如果您的應用程式需要使用 D 磁碟機來儲存資料，請依照這些指示暫時磁碟使用不同的磁碟機。 儲存您要保留的資料永遠不會使用暫存的磁碟。

如果您調整或**停止 （解除配置）**虛擬機器，這可能會觸發新 hypervisor 虛擬機器中的位置。 計畫之內進行的維修作業事件也可能會觸發此位置。 在此案例中，將第一個可用的磁碟機字母重新指派暫存的磁碟。 如果您有特別需要 d 磁碟機的應用程式，您需要遵循這些步驟來暫時移動 pagefile.sys、 附加新資料磁碟並將其指派字母 D，然後將 pagefile.sys 移回暫存的磁碟機。 完成之後，Azure 不會再次 d︰ 如果 VM 會移至不同的 hypervisor。

如需有關如何 Azure 使用暫存的磁碟的詳細資訊，請參閱[瞭解暫存的磁碟機上的 Microsoft Azure 虛擬機器](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>附加資料磁碟

首先，必須將資料磁碟附加至虛擬機器。 

- 若要使用入口網站，請參閱[如何附加 Azure 入口網站中的資料磁碟](virtual-machines-windows-attach-disk-portal.md)
- 若要使用 [傳統] 入口網站，請參閱[如何附加到 Windows 虛擬機器資料磁碟](virtual-machines-windows-classic-attach-disk.md)。 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>暫時將 pagefile.sys 移到 C 磁碟機

1. 連線至虛擬機器。 

2. 以滑鼠右鍵按一下**[開始**] 功能表，然後選取 [**系統**]。

3. 在左功能表上，選取 [**進階的系統設定**]。

4. 在 [**效能**] 區段中，選取 [**設定**]。

5. 選取 [**進階**] 索引標籤。

5. 在 [**虛擬記憶體**] 區段中，選取 [**變更**]。

6. 選取**C**磁碟機，然後按一下 [**系統管理大小**，然後按一下**設定**。

7. 選取**D**磁碟機，然後按一下 [**沒有分頁檔案**，然後按一下**設定**。

8. 按一下 [套用]。 您會收到警告的電腦必須重新啟動，變更才會生效。

9. 重新啟動虛擬機器。




## <a name="change-the-drive-letters"></a>磁碟機字母變更 

1. 一旦 VM 重新啟動時，請登入 VM。

2. 按一下 [**開始**] 功能表中，輸入**diskmgmt.msc**並按下 Enter。 隨後便開始磁碟管理。

3. **D**，暫存磁碟機上按一下滑鼠右鍵，然後選取 [**變更磁碟機字母和路徑**。

4. 磁碟機字母底下選取磁碟機**G** ，然後按一下**[確定]**。 

5. 以滑鼠右鍵按一下資料磁碟空間，並選取**變更磁碟機代號及路徑**。

6. 磁碟機字母底下選取磁碟機**D** ，然後按一下**[確定]**。 

7. **G**、 暫存磁碟機上按一下滑鼠右鍵，然後選取 [**變更磁碟機字母和路徑**。

8. 磁碟機字母底下選取磁碟機**E** ，然後按一下**[確定]**。 

> [AZURE.NOTE] 如果您 VM 有其他磁碟機，請重新指派其他的磁碟及磁碟機的磁碟機字母使用相同的方法。 您想要磁碟設定︰  
>- C: OS 磁碟  
>- D︰ 資料磁碟  
>- E︰ 暫存磁碟



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>將 pagefile.sys 移回暫存磁碟機 

1. 以滑鼠右鍵按一下**[開始**] 功能表，然後選取 [**系統**

2. 在左功能表上，選取 [**進階的系統設定**]。

3. 在 [**效能**] 區段中，選取 [**設定**]。

4. 選取 [**進階**] 索引標籤。

5. 在 [**虛擬記憶體**] 區段中，選取 [**變更**]。

6. 選取**C** OS 磁碟機並按一下 [**沒有分頁檔案**，然後按一下 [**設定**。

7. 選取的暫存的儲存空間的磁碟機**E** ，然後按一下 [**系統管理大小**，然後按一下**設定**。

8. 按一下 [**套用**]。 您會收到警告的電腦必須重新啟動，變更才會生效。

9. 重新啟動虛擬機器。




## <a name="next-steps"></a>後續步驟
- 您可以增加可用儲存空間至您的虛擬機器[附加其他資料磁碟](virtual-machines-windows-attach-disk-portal.md)。



