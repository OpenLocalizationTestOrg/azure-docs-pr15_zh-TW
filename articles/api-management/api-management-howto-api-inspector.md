<properties 
    pageTitle="如何使用追蹤 API 檢查通話 Azure API 管理" 
    description="瞭解如何追蹤使用 API 檢查 Azure API 管理來電。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>如何使用追蹤 API 檢查通話 Azure API 管理

API 管理提供可協助您處理偵錯和疑難排解您 Api API 檢查工具。 API 檢查可以以程式設計方式使用，而且也可以直接從開發人員入口網站使用。 

除了追蹤作業 API 檢查也會追蹤[原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)評估。 如需示範，請參閱[雲端封面片段 177︰ 更多 API 管理功能](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)和快轉至 21:00。

本指南可提供逐步解說，使用 API 檢查。

>[AZURE.NOTE] 只產生與提供要求包含所屬的[系統管理員](api-management-howto-create-groups.md)帳戶的訂閱索引鍵的 API 檢查追蹤。

## <a name="trace-call"></a>使用 API 檢查追蹤通話

若要使用 API 檢查，請新增**ocp apim 追蹤︰ true**要求標題至您的操作通話，然後下載並檢查追蹤使用由**ocp apim-追蹤位置**回應標頭的 URL。 這可以進行，以程式設計方式，並也可以直接從開發人員入口網站完成。

本教學課程中會顯示如何使用 [API 檢查]，使用基本計算機 API 設定[管理您的第一個 API](api-management-get-started.md)取得開始教學課程中追蹤的動作。 如果尚未完成的教學課程只需要幾分鐘的時間，匯入基本的計算機 API，或您可以使用另一個 API，例如回音 API 您所選擇。 每個 API 管理服務執行個體隨附預先設定，以盡可能降低回音 API，可嘗試使用和瞭解 API 管理。 盡可能降低回音 API 回傳回任何輸入傳送給它。 若要使用它，您可以叫用任何 HTTP 動作，並傳回的值只會是您所傳送。 



若要開始，按一下 [**開發人員入口網站**中 Azure 傳統入口網站的 API 管理服務]。 作業可直接從開發人員入口網站提供方便的方式來檢視並測試 API 的作業。

>如果您還沒有建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

![API 管理開發人員入口網站][api-management-developer-portal-menu]

從上方的功能表中，按一下 [ **Api** ，然後按一下 [**基本計算機**。

![盡可能降低回音 API][api-management-api]

按一下 [**試試看**嘗試**新增兩個整數**作業]。

![試試看][api-management-open-console]

保留預設參數值，然後選取您想要從**訂閱鍵**下拉式使用產品的訂閱索引鍵。

在開發人員入口網站**Ocp Apim 追蹤**中預設標題已設定為**true**。 這個標題設定會產生追蹤。

![傳送][api-management-http-get]

按一下 [**傳送**至叫用作業。

![傳送][api-management-send-results]

在回應中標題會類似下面的範例值**ocp apim 追蹤的位置**。

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

追蹤可以下載從指定的位置，並檢閱下一個步驟所示。

## <a name="inspect-trace"></a>檢查追蹤

若要檢閱追蹤中的值，下載追蹤檔案從**ocp apim-追蹤位置**的 URL。 它是 JSON 格式的文字檔案，並包含項目類似以下的範例。

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"></a>接下來的步驟

-   觀看示範追蹤中的原則運算式[雲端封面片段 177︰ 更多 API 管理功能](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)。 將轉至 21:00 若要查看示範。

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[開始使用 Azure API 管理]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 