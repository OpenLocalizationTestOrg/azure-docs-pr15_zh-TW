<properties 
    pageTitle="如何安裝 Apache Qpid Proton C 上 Linux VM |Microsoft Azure"
    description="如何建立使用 Azure 虛擬機器 CentOS Linux VM 以及如何建立及安裝 Apache Qpid Proton C 文件庫。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Azure Linux VM 上安裝 Apache Qpid Proton C

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

本節說明如何建立使用 Azure 虛擬機器 CentOS Linux VM 以及如何下載、 建立及安裝以及 Python 和 PHP 語言繫結 Apache Qpid Proton C 文件庫。 完成這些步驟之後，您將能夠執行本指南隨附 Python 和 PHP 範例。

使用[Azure 傳統入口網站][]執行的第一個步驟。 以下螢幕擷取畫面顯示如何建立名為 「 史 centos 」 CentOS VM:

![Azure Linux VM 上 proton][0]

佈建之後，入口網站中會顯示下列動作︰

![Azure Linux VM 上 proton][1]

若要登入電腦，您必須知道的端點連接埠 SSH 的。 選取新建立的 VM 並按一下 [**結束點**] 索引標籤上，您可以從[Azure 傳統入口網站][]取得這個值。 以下螢幕擷取畫面會顯示此電腦的公用 SSH 連接埠 57146。

![Azure Linux VM 上 proton][2]

您現在可以連線到電腦，使用 SSH。 此範例使用 PuTTY 工具，如以下的螢幕擷取畫面所示︰

![Azure Linux VM 上 proton][3]

Python 和 PHP 應用程式，此範例使用從 Apache Proton 用戶端文件庫。 這些文件庫，可從[http://qpid.apache.org/download.html](http://qpid.apache.org/download.html)下載。 散發套件中的讀我檔案說明安裝相依性，並建立 Proton 所需的步驟。 以下是摘要的步驟︰

1.  編輯 yum 設定檔案 (/ etc/yum.conf) 和 [註解至核心標題排除更新 (\#排除 = 核心\*)。 這是安裝 gcc 編譯器所需。

2.  安裝必要的套件︰

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  下載 Proton 文件庫︰

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  從通訊群組封存擷取 Proton 程式碼︰

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  建立並安裝使用下列步驟，將讀我檔案的程式碼︰

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

執行這些步驟後，Proton 且已安裝在電腦上可供使用。

## <a name="next-steps"></a>後續步驟

準備瞭解更多嗎？ 請參考下列連結︰

- [服務匯流排 AMQP 概觀][]

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure 傳統入口網站]: http://manage.windowsazure.com


