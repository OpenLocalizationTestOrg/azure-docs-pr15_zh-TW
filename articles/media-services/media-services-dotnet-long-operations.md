<properties 
    pageTitle="投票，以長期執行作業 |Microsoft Azure" 
    description="本主題說明如何投票長期執行作業。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="delivering-live-streaming-with-azure-media-services"></a>進行即時資料流與 Azure 媒體服務

##<a name="overview"></a>概觀

Microsoft Azure 媒體服務可提供開始作業的媒體服務傳送邀請的 Api (例如︰ 建立、 啟動、 停止或刪除頻道)。 這些是作業長。

媒體服務.NET SDK 提供 Api 傳送邀請，以完成作業，請等候 （在內部，Api 正在輪詢作業進度一些間隔）。 例如，當您撥打頻道時。Start，方法會傳回之後開始頻道。 您也可以使用非同步的版本︰ 等頻道。StartAsync() （有關任務架構非同步模式，請參閱[點選](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)）。 傳送作業要求及作業完成後，才投票狀態的 Api 稱為 「 投票 」 方法]。 這些方法 （特別是非同步版本） 的建議豐富型用戶端應用程式及/或狀態的服務。

有應用程式無法等待的時間有多長執行 http 要求的位置，並且想要手動檢查有作業進度的案例。 一般的範例是在瀏覽器互動狀態 web 服務︰ web 服務當瀏覽器建立新頻道要求時，啟動長時間執行的操作，並傳回瀏覽器的作業識別碼。 在瀏覽器無法再要求取得根據識別碼的作業的 web 服務 媒體服務.NET SDK 提供 Api，很適合這種情況。 這些 Api 稱為 「 非投票方法]。
「 非投票方法 」 有下列命名模式︰ 傳送*OperationName*作業 (例如，SendCreateOperation)。 傳送*OperationName*作業方法會傳回**IOperation**的物件。傳回的物件會包含可用來追蹤作業的資訊。 傳送*OperationName*OperationAsync 方法傳回**工作<IOperation>**。

目前，下列類別支援非投票的方法︰**頻道**、 **StreamingEndpoint**及**程式**。

若要獲得作業狀態，使用 [ **GetOperation**方法**OperationBaseCollection**類別。 使用下列的時間間隔，查看作業的狀態︰**頻道**] 和 [ **StreamingEndpoint**作業，請使用 30 秒。如需**程式**作業，使用 10 秒內。


##<a name="example"></a>範例

下列範例會定義稱為**ChannelOperations**的類別。 此課程定義可能是您的 web 服務類別定義的起點。 為求，下列範例會使用非非同步版本的方法。

範例也會顯示於用戶端可能會如何使用此課程。

###<a name="channeloperations-class-definition"></a>ChannelOperations 類別定義

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>用戶端程式碼

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
