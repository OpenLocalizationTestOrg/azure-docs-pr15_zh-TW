<properties 
    pageTitle="快速入門發表內容，視需要使用 Java |Microsoft Azure" 
    description="說明如何使用 Azure 媒體服務執行一般工作，包括編碼、 加密，以及串流資源。" 
    services="media-services" 
    documentationCenter="java" 
    authors="juliako" 
    manager="erikre" 
/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"   
    ms.author="juliako"/>

# <a name="get-started-with-delivering-content-on-demand-using-java"></a>開始使用視需要使用 Java 發表內容

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

##<a name="setting-up-an-azure-account-for-media-services"></a>針對媒體服務設定 Azure 帳戶

若要設定您的媒體服務帳戶，請使用 Azure 入口網站。 請參閱[如何建立媒體服務帳戶](media-services-portal-create-account.md)的主題。 Azure 入口網站中建立您的帳戶之後, 您就可以設定您的電腦，媒體服務開發項目。

##<a name="setting-up-for-media-services-development"></a>針對媒體服務開發設定

本節中的媒體服務開發 java 使用媒體服務 SDK 一般的先決條件。

###<a name="prerequisites"></a>必要條件

-   在新的或現有的 Azure 訂閱媒體服務帳戶。 請參閱[如何建立媒體服務帳戶](media-services-portal-create-account.md)的主題。
-   Java，您可以從[Azure Java 開發人員中心][]安裝 Azure 文件庫。

##<a name="how-to-use-media-services-with-java"></a>如何︰ 使用 Java 媒體服務

下列程式碼會顯示如何建立資產，的媒體檔案上傳至資產，轉換資產，並建立串流視訊定位器任務與執行工作。

您需要設定媒體服務帳戶，才能使用此程式碼。 如需帳戶設定的詳細資訊，請參閱[如何建立媒體服務帳戶](media-services-portal-create-account.md)。

取代您的 「 clientId 」 和 「 clientSecret' 變數的值。 程式碼也是儲存在本機的檔案。 您需要提供您自己使用的檔案。


    import java.io.*;
    import java.security.NoSuchAlgorithmException;
    import java.util.EnumSet;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.media.MediaConfiguration;
    import com.microsoft.windowsazure.services.media.MediaContract;
    import com.microsoft.windowsazure.services.media.MediaService;
    import com.microsoft.windowsazure.services.media.WritableBlobContainerContract;
    import com.microsoft.windowsazure.services.media.models.AccessPolicy;
    import com.microsoft.windowsazure.services.media.models.AccessPolicyInfo;
    import com.microsoft.windowsazure.services.media.models.AccessPolicyPermission;
    import com.microsoft.windowsazure.services.media.models.Asset;
    import com.microsoft.windowsazure.services.media.models.AssetFile;
    import com.microsoft.windowsazure.services.media.models.AssetFileInfo;
    import com.microsoft.windowsazure.services.media.models.AssetInfo;
    import com.microsoft.windowsazure.services.media.models.Job;
    import com.microsoft.windowsazure.services.media.models.JobInfo;
    import com.microsoft.windowsazure.services.media.models.JobState;
    import com.microsoft.windowsazure.services.media.models.ListResult;
    import com.microsoft.windowsazure.services.media.models.Locator;
    import com.microsoft.windowsazure.services.media.models.LocatorInfo;
    import com.microsoft.windowsazure.services.media.models.LocatorType;
    import com.microsoft.windowsazure.services.media.models.MediaProcessor;
    import com.microsoft.windowsazure.services.media.models.MediaProcessorInfo;
    import com.microsoft.windowsazure.services.media.models.Task;


    public class HelloMediaServices
    {
    // Media Services account credentials configuration
    private static String mediaServiceUri = "https://media.windows.net/API/";
    private static String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
    private static String clientId = "account name";
    private static String clientSecret = "account key";
    private static String scope = "urn:WindowsAzureMediaServices";
    private static MediaContract mediaService;

    // Encoder configuration
    private static String preferredEncoder = "Media Encoder Standard";
    private static String encodingPreset = "H264 Multiple Bitrate 720p";

    public static void main(String[] args)
    {

    try {
    // Set up the MediaContract object to call into the Media Services account
    Configuration configuration = MediaConfiguration.configureWithOAuthAuthentication(
    mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
    mediaService = MediaService.create(configuration);


    // Upload a local file to an Asset
    AssetInfo uploadAsset = uploadFileAndCreateAsset("BigBuckBunny.mp4");
    System.out.println("Uploaded Asset Id: " + uploadAsset.getId());


    // Transform the Asset
    AssetInfo encodedAsset = encode(uploadAsset);
    System.out.println("Encoded Asset Id: " + encodedAsset.getId());

    // Create the Streaming Origin Locator
    String url = getStreamingOriginLocator(encodedAsset);

    System.out.println("Origin Locator URL: " + url);
    System.out.println("Sample completed!");

    } catch (ServiceException se) {
    System.out.println("ServiceException encountered.");
    System.out.println(se.toString());
    } catch (Exception e) {
    System.out.println("Exception encountered.");
    System.out.println(e.toString());
    }

    }

    private static AssetInfo uploadFileAndCreateAsset(String fileName)
    throws ServiceException, FileNotFoundException, NoSuchAlgorithmException {

    WritableBlobContainerContract uploader;
    AssetInfo resultAsset;
    AccessPolicyInfo uploadAccessPolicy;
    LocatorInfo uploadLocator = null;

    // Create an Asset
    resultAsset = mediaService.create(Asset.create().setName(fileName).setAlternateId("altId"));
    System.out.println("Created Asset " + fileName);

    // Create an AccessPolicy that provides Write access for 15 minutes
    uploadAccessPolicy = mediaService
    .create(AccessPolicy.create("uploadAccessPolicy", 15.0, EnumSet.of(AccessPolicyPermission.WRITE)));

    // Create a Locator using the AccessPolicy and Asset
    uploadLocator = mediaService
    .create(Locator.create(uploadAccessPolicy.getId(), resultAsset.getId(), LocatorType.SAS));

    // Create the Blob Writer using the Locator
    uploader = mediaService.createBlobWriter(uploadLocator);

    File file = new File("BigBuckBunny.mp4");

    // The local file that will be uploaded to your Media Services account
    InputStream input = new FileInputStream(file);

    System.out.println("Uploading " + fileName);

    // Upload the local file to the asset
    uploader.createBlockBlob(fileName, input);

    // Inform Media Services about the uploaded files
    mediaService.action(AssetFile.createFileInfos(resultAsset.getId()));
    System.out.println("Uploaded Asset File " + fileName);

    mediaService.delete(Locator.delete(uploadLocator.getId()));
    mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));

    return resultAsset;
    }

    // Create a Job that contains a Task to transform the Asset
    private static AssetInfo encode(AssetInfo assetToEncode)
    throws ServiceException, InterruptedException {

    // Retrieve the list of Media Processors that match the name
    ListResult<MediaProcessorInfo> mediaProcessors = mediaService
                              .list(MediaProcessor.list().set("$filter", String.format("Name eq '%s'", preferredEncoder)));
    
              // Use the latest version of the Media Processor
              MediaProcessorInfo mediaProcessor = null;
              for (MediaProcessorInfo info : mediaProcessors) {
                  if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0) {
                      mediaProcessor = info;
                  }
              }
    
              System.out.println("Using Media Processor: " + mediaProcessor.getName() + " " + mediaProcessor.getVersion());
    
              // Create a task with the specified Media Processor
              String outputAssetName = String.format("%s as %s", assetToEncode.getName(), encodingPreset);
              String taskXml = "<taskBody><inputAsset>JobInputAsset(0)</inputAsset>"
                      + "<outputAsset assetCreationOptions=\"0\"" // AssetCreationOptions.None
                      + " assetName=\"" + outputAssetName + "\">JobOutputAsset(0)</outputAsset></taskBody>";
    
              Task.CreateBatchOperation task = Task.create(mediaProcessor.getId(), taskXml)
                      .setConfiguration(encodingPreset).setName("Encoding");
    
              // Create the Job; this automatically schedules and runs it.
              Job.Creator jobCreator = Job.create()
                      .setName(String.format("Encoding %s to %s", assetToEncode.getName(), encodingPreset))
                      .addInputMediaAsset(assetToEncode.getId()).setPriority(2).addTaskCreator(task);
              JobInfo job = mediaService.create(jobCreator);
            
              String jobId = job.getId();
              System.out.println("Created Job with Id: " + jobId);
    
              // Check to see if the Job has completed
              checkJobStatus(jobId);
              // Done with the Job
    
              // Retrieve the output Asset
              ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(job.getOutputAssetsLink()));
              return outputAssets.get(0);
          }
        
    
          public static String getStreamingOriginLocator(AssetInfo asset) throws ServiceException {
              // Get the .ISM AssetFile
              ListResult<AssetFileInfo> assetFiles = mediaService.list(AssetFile.list(asset.getAssetFilesLink()));
              AssetFileInfo streamingAssetFile = null;
              for (AssetFileInfo file : assetFiles) {
                  if (file.getName().toLowerCase().endsWith(".ism")) {
                      streamingAssetFile = file;
                      break;
                  }
              }
    
              AccessPolicyInfo originAccessPolicy;
              LocatorInfo originLocator = null;
    
              // Create a 30-day readonly AccessPolicy
              double durationInMinutes = 60 * 24 * 30;
              originAccessPolicy = mediaService.create(
                      AccessPolicy.create("Streaming policy", durationInMinutes, EnumSet.of(AccessPolicyPermission.READ)));
    
              // Create a Locator using the AccessPolicy and Asset
              originLocator = mediaService
                      .create(Locator.create(originAccessPolicy.getId(), asset.getId(), LocatorType.OnDemandOrigin));
    
              // Create a Smooth Streaming base URL
              return originLocator.getPath() + streamingAssetFile.getName() + "/manifest";
          }
    
          private static void checkJobStatus(String jobId) throws InterruptedException, ServiceException {
              boolean done = false;
              JobState jobState = null;
              while (!done) {
                  // Sleep for 5 seconds
                  Thread.sleep(5000);
                
                  // Query the updated Job state
                  jobState = mediaService.get(Job.get(jobId)).getState();
                  System.out.println("Job state: " + jobState);
    
                  if (jobState == JobState.Finished || jobState == JobState.Canceled || jobState == JobState.Error) {
                      done = true;
                  }
              }
          }
    
    }


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="additional-resources"></a>其他資源

媒體服務 Javadoc 文件，請參閱[Azure Java 文件的文件庫][]。

<!-- URLs. -->

  [Azure Java 開發人員中心]: http://azure.microsoft.com/develop/java/
  [Azure Java 文件庫]: http://dl.windowsazure.com/javadoc/
  [Media Services Client Development]: http://msdn.microsoft.com/library/windowsazure/dn223283.aspx

 
