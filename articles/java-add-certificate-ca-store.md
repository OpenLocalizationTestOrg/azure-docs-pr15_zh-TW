<properties 
    pageTitle="將憑證新增至 Java CA store |Microsoft Azure" 
    description="瞭解如何新增 Java CA 憑證 (cacerts) store Twilio 服務或 Azure 服務的憑證授權單位 (CA)。" 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>將憑證新增至 Java CA 憑證 」 存放區
下列步驟會顯示如何將憑證授權單位 (CA) 新增至 Java CA (cacerts) 憑證存放區。 使用範例適用於 Twilio 服務所需的憑證。 稍後在主題中所提供的資訊會說明如何 Azure 服務匯流排安裝 CA 的憑證。 

您可以使用 keytool 新增 CA 憑證之前壓縮您 JDK，並將其新增至 Azure 專案的**approot**資料夾，或者您可以執行的使用 keytool 將憑證新增 Azure 啟動工作。 假設您將加入 JDK 正在壓縮之前的憑證。 此外，會在範例中，使用特定的 CA 憑證，但會類似取得不同的憑證，並將其匯入 cacerts 存放區的步驟。

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>若要將憑證新增至 cacerts 存放區

1. 設定為您 JDK **jdk\jre\lib\security**資料夾的命令提示字元中，執行下列動作，請參閱安裝哪些憑證︰

    `keytool -list -keystore cacerts`

    系統會提示您儲存密碼。 預設的密碼已**變更**。 （如果您想要變更的密碼，請參閱<http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>keytool 文件）。這個範例假設 MD5 憑證指紋辨識 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 並未列出，且您想要匯入 （Twilio API 服務需要此特定憑證）。
2. 從清單中所列[GeoTrust 根憑證](http://www.geotrust.com/resources/root-certificates/)的憑證，取得憑證。 以滑鼠右鍵按一下序列 35:DE:F4:CF 與憑證的連結，並將其儲存到**jdk\jre\lib\security**資料夾。 為了此範例中，儲存為檔案命名**Equifax\_Secure\_憑證\_Authority.cer**。
3. 匯入的憑證，透過下列命令︰

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    當畫面提示您信任這個憑證，如果憑證有 MD5 指紋 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4，輸入**y**的回覆。
4. 執行下列命令，以確保的 CA 憑證已成功匯入︰

    `keytool -list -keystore cacerts`

5. Zip JDK，並將其新增至 Azure 專案的**approot**資料夾。

如需 keytool 相關資訊，請參閱<http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>。

## <a name="azure-root-certificates"></a>Azure 根憑證

使用 Azure 服務 （例如 Azure 服務匯流排） 的應用程式需要信任的巴爾的摩 CyberTrust 根憑證。 （從 2013 年 4 月 15 日 Azure 開始移轉從 GTE CyberTrust 全域根目錄巴爾的摩 CyberTrust 根。 此移轉所需完成的幾個月。）

巴爾的摩憑證可能已經安裝在您 cacerts 存放區，因此請記得要執行**keytool-清單**命令前，請參閱是否已經存在。

如果您需要新增巴爾的摩 CyberTrust 根目錄，它有序列 02:00:00:b9 和 SHA1 指紋 d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74。 可透過<https://cacert.omniroot.com/bc2025.crt>，儲存至本機檔案的副檔名**.cer**，並使用**keytool**如上所示然後匯入下載它。

## <a name="next-steps"></a>後續步驟

如需有關使用 Azure 的根憑證的詳細資訊，請參閱[Azure 根憑證移轉](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx)。

如需有關 Java 的詳細資訊，請參閱[Java 開發人員中心](/develop/java/)。
