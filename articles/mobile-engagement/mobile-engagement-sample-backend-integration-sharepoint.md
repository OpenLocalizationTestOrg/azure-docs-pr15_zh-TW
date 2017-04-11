<properties 
    pageTitle="Azure 行動互動的後端整合" 
    description="若要從 SharePoint 中建立行銷活動的 SharePoint 後端與連線 Azure 行動互動" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Azure 行動互動-API 整合

在自動行銷系統中，建立並啟動行銷活動也會自動出現。 針對此項目的-Azure 行動互動可讓建立這類自動化的行銷活動，以及使用 Api。 

依照一般做法新客戶使用行動互動前端介面建立宣告投票等做為其行銷活動的一部分。 不過當行銷活動成熟時，就需要運用以便可以在行動互動動態根據從後端系統傳送中的資料建立行銷活動建立完全自動化的管線鎖定 （例如任何 CRM 系統或 CMS 系統 SharePoint 等） 的後端系統中的資料。 

![][5]

本教學課程中會經歷的案例的 SharePoint 商務版使用者填入 SharePoint 清單與行銷資料，而自動化程序挑選 [從清單中的項目，且與行動互動系統使用可用的 REST Api 建立行銷活動的 SharePoint 資料連線。 
 
> [AZURE.IMPORTANT] 一般而言，您可以使用這個範例，開始了解如何呼叫任何行動互動 REST API，因為它詳細資料的兩個重要層面呼叫 Api-驗證，並按一下 [傳遞的參數。 

## <a name="sharepoint-integration"></a>SharePoint 整合
1. 以下是範例 SharePoint 清單的外觀。 **標題**、**類別**、 **NotificationTitle**、**訊息**及**URL**是用來建立通知。 有稱為**IsProcessed**主控台程式的表單中的範例自動化程序會使用資料行。 您可以的執行這個主控台程式為 Azure WebJob，好讓您可以排程，或您可以直接使用 SharePoint 工作流程建立及插入的 SharePoint 清單項目時，啟動公告的程式。 在這個範例中，我們會使用經歷的 SharePoint 清單中的項目，建立中 Azure 行動互動的通知，每一個和最後標記**IsProcessed**標幟為 true 在成功通知建立該主控台程式。

    ![][1]

2. 我們正在使用的程式碼，從*遠端 SharePoint 中的驗證線上使用的用戶端物件模型*範例[以下](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c)驗證的 SharePoint 清單。
 
3. 驗證之後，我們迴圈，找出所有新建立的項目清單項目 (這也會有**IsProcessed** = false)。 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>行動互動整合
1.  當我們找到的項目，需要處理，我們會擷取建立清單項目與通話的通知所需的資訊`CreateAzMECampaign`建立及後續`ActivateAzMECampaign`啟動它。 這些是基本上 REST API 來電撥打至行動互動後端。 

2.  行動互動 REST Api 需要**基本驗證配置授權 HTTP 標頭**的組成`ApplicationId`和`ApiKey`從 Azure 入口網站取得。 請確認您從**api 金鑰**區段使用索引鍵和*不*從**sdk 索引鍵**一節。 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. 建立通知輸入行銷活動-參照[文件](https://msdn.microsoft.com/library/azure/mt683750.aspx)。 您需要請確定您要指定行銷活動`kind`為*公告*[內容](https://msdn.microsoft.com/library/azure/mt683751.aspx)，並將它當成 FormUrlEncodedContent 傳遞。 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. 建立通知之後，您會看到像下面行動互動入口網站 (請注意，狀態 = 草稿及啟動 = n/A)

    ![][3]

5. `CreateAzMECampaign`建立公告活動並傳回其識別碼來電者。 `ActivateAzMECampaign`若要啟動的行銷活動的參數為需要此 Id。 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. 啟動通知之後，您會看到類似於下行動互動入口網站上︰

    ![][4]

7. 時所啟動的行銷活動，將會看到通知來啟動任何符合準則的行銷活動的裝置。 

8. 您也會發現清單項目標示 IsProcessed = false 具有已設為 True，一旦建立公告行銷活動。  

此範例會建立簡單的公告活動指定大多必要的屬性。 您可以自訂此您可以從入口網站使用的資訊與方法很類似[以下](https://msdn.microsoft.com/library/azure/mt683751.aspx)。 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
