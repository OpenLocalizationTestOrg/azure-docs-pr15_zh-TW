## <a name="send-messages-to-event-hubs"></a>傳送郵件給事件集線器

事件集線器 Java 用戶端文件庫可供使用[Maven 中央存放庫](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)中，從 Maven 專案，並可使用下列的相依性宣告內 Maven 專案檔案參考︰    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
為建置環境類型不同，您可以明確地取得最新發行的 JAR 檔案從[Maven 中央存放庫](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)或[GitHub 的發行分配點](https://github.com/Azure/azure-event-hubs/releases)。  

簡單的事件發行者，匯入*com.microsoft.azure.eventhubs*套件事件集線器用戶端類別及公用程式類別，例如常見 Azure 服務匯流排郵件用戶端與共用的例外狀況的*com.microsoft.azure.servicebus*套件。 

下列範例中，首先您最愛的 Java 開發環境中建立新 Maven 專案主控台/命令介面應用程式。 將稱為類別```Send```。     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

命名空間和事件中心名稱取代建立事件中心時所使用的值。

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

然後，以將其 utf-8 位元組編碼字串建立單一的事件。 然後，我們會從連接字串建立新的事件集線器用戶端執行個體並傳送郵件。   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 
