## <a name="send-messages-to-event-hubs"></a>傳送郵件給事件集線器

在本節中，我們會撰寫傳送事件到您的事件集線器 C 應用程式。 我們會使用從[Apache Qpid 專案](http://qpid.apache.org/)Proton AMQP 文件庫。 這是類似於服務匯流排佇列與使用主題 AMQP 從 C 為顯示[以下](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)項目。 如需詳細資訊，請參閱[Qpid Proton 文件](http://qpid.apache.org/proton/index.html)。

1. [Qpid AMQP Messenger] 頁面](http://qpid.apache.org/components/messenger/index.html)上，按一下 [**安裝 Qpid Proton**連結並遵循指示根據自己的環境。 我們會假設 Linux 環境。例如， [Azure Linux VM](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md)與 Ubuntu 14.04。

2. 若要編譯 Proton 文件庫，安裝下列套件︰

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. 下載[Qpid Proton 文件庫](http://qpid.apache.org/proton/index.html)文件庫，並解壓縮，例如︰

    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. 建立建置目錄、 編譯和安裝︰

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. 在您的公司目錄，建立名**sender.c**下列內容的新檔案。 請記得要取代您的事件中心名稱和命名空間名稱] 的值 (後者通常是`{event hub name}-ns`)。 您也必須**SendRule**先前建立的替代 URL 編碼版本的金鑰。 您可以進行 URL 編碼，[在這裡](http://www.w3schools.com/tags/ref_urlencode.asp)。

    ```
    #include "proton/message.h"
    #include "proton/messenger.h"

    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>

    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  

    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  

    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }

    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";

        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();

        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);

        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));

        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);

        pn_message_free(message);
    }

    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");

        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);

        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }

        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);

        return 0;
    }
    ```

6. 編譯的檔案，假設**gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] 在這個程式碼，我們會使用 1 外寄視窗越快越強制出的郵件。 一般而言，您的應用程式應該嘗試批次來增加處理量的郵件。 如需有關如何使用這項問題和其他環境中，以及繫結所提供的平台的 Qpid Proton 文件庫中看到[Qpid AMQP Messenger 頁面](http://qpid.apache.org/components/messenger/index.html)（目前 Perl PHP，Python 與 [注音標示）。
