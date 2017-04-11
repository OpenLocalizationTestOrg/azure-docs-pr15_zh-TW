<properties 
    pageTitle="雲端 Proxy 服務應用程式探索登錄設定 |Microsoft Azure" 
    description="本主題的目標是為您提供的步驟，您需要執行執行雲端應用程式探索代理程式的電腦上設定的必要的連接埠。" 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Proxy 服務的雲端應用程式探索登錄設定

根據預設，雲端應用程式探索代理程式設定為使用的連接埠 80 或 443。 如果您打算使用自訂的連接埠 （非 80 和 443） 的 proxy 伺服器的環境中安裝雲端應用程式探索，必須先設定您的代理程式，使用此連接埠。 設定為基礎的登錄機碼。


本主題的目標是為您提供的步驟，您需要執行執行雲端應用程式探索代理程式的電腦上設定的必要的連接埠。



**若要修改的連接埠使用雲端應用程式探索代理程式的電腦，請執行下列步驟︰**


1. 啟動登錄編輯程式。 <br> ![執行](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. 瀏覽至或建立下列登錄機碼︰ <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud 應用程式 Discovery\Endpoint** 

3. 建立新的**多字串**值稱為**連接埠**。 ![新](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. 若要開啟 [**編輯多字串**] 對話方塊中，按兩下 [連接埠] 的值。


5. 在 [數值資料] 文字方塊中，輸入下列的值，然後新增您的組織所使用的所有自訂連接埠︰ <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![編輯多字串](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. 按一下**[確定**] 關閉 [**編輯多字串**] 對話方塊。



**其他資源**


* [如何找到組織內 unsanctioned 的雲端應用程式所使用的](active-directory-cloudappdiscovery-whatis.md) 


