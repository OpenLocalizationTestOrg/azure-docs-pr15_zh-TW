<properties
     pageTitle="如何將自訂網域名稱對應 Azure 內容傳遞網路 (CDN) 內容 |Microsoft Azure"
     description="本主題將示範如何將自訂網域對應 CDN 內容。"
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>如何將自訂網域對應至內容傳遞網路 」 (CDN) 端點
您可以將自訂網域對應 CDN 端點，才能在 Url 中使用您自己的網域名稱快取的內容，而不是使用 azureedge.net 的子網域。

有兩種方式，將您的自訂網域對應到 CDN 結束點︰

1. [使用您的網域註冊機構建立一筆 CNAME 記錄，並將您的自訂網域和子網域對應至 CDN 的端點](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    CNAME 記錄是想地圖來源網域的 DNS 功能`www.contosocdn.com`或`cdn.contoso.com`，目的地網域。 在此情況下，來源網域是您的自訂網域和子網域 （子網域， **www**或**cdn**所需等）。 目的地網域是您的 CDN 結束點。  

    將您的自訂網域對應至您的 CDN 端點的程序，不過，導致短時間停機時間的網域時要註冊 Azure 入口網站中該網域。

2. [新增**cdnverify**中繼註冊步驟](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    如果您的自訂網域目前支援的應用程式與服務等級協定 (SLA) 的要求沒有停機時間，然後您可以使用 Azure **cdnverify**子網域，讓使用者將能夠存取您的網域的 DNS 對應進行時提供中繼註冊步驟。  

註冊您的自訂網域使用上述程序之後，您會想要[確認自訂子網域參照您 CDN 結束點](#verify-that-the-custom-subdomain-references-your-cdn-endpoint)。

> [AZURE.NOTE] 您必須建立一筆 CNAME 記錄的網域註冊機構，將您的網域對應到 CDN 結束點。 CNAME 記錄對應的特定的子網域例如`www.contoso.com`或`cdn.contoso.com`。 無法將一筆 CNAME 記錄對應到根網域，例如`contoso.com`。
>    
> 子網域只能與一個 CDN 端點相關聯。 您建立 CNAME 記錄會傳送收件者為指定的端點子網域的所有流量。  例如，如果您建立關聯`www.contoso.com`與您的 CDN 端點，然後您無法將它與關聯其他 Azure 端點，例如儲存帳戶端點或雲端服務端點。 不過，您可以使用相同的網域，從不同的子網域為不同的服務端點。 您也可以將不同的子網域對應至相同的 CDN 端點。
>
> **從 Verizon Azure CDN** （標準和進階版） 端點，請注意，它會使用**90 分鐘**，才會傳播到 CDN 邊緣節點的自訂網域變更。

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>註冊 Azure CDN 端點的自訂網域

1.  登入[Azure 入口網站](https://portal.azure.com/)。
2.  按一下 [**瀏覽]**，然後**CDN 設定檔**] 然後與您想要將自訂網域名稱對應的端點 CDN 設定檔]。  
3.  在**CDN 設定檔**刀中，按一下 [與您想要建立關聯的子網域 CDN 結束點]。
4.  在端點刀頂端，按一下 [**新增自訂網域**] 按鈕。  在**新增自訂網域**刀，您會看到端點主機名稱，請從您的 CDN 端點，以用來建立新的 CNAME 記錄。 主機名稱地址的格式會顯示為**&lt;EndpointName >。 azureedge.net**。  您可以複製這個主機名稱，以用來建立 CNAME 記錄。  
5.  瀏覽至您的網域註冊機構的網站，然後找出 [建立 DNS 記錄] 區段。 您可能會發現此例如**網域名稱**、 **DNS**或**名稱伺服器管理**] 區段中。
6.  管理 CNAMEs 尋找區段。 您可能要移至 [進階的設定] 頁面，並尋找文字 CNAME、 別名或子網域。
7.  建立新的 CNAME 記錄的地圖您所選的子網域 （例如**www**或**cdn**） 中**新增自訂網域**的刀提供主機名稱。
8.  返回 [**新增自訂網域**刀，並輸入您的自訂網域，包括子網域，在對話方塊中。 例如，輸入格式的網域名稱`www.contoso.com`或`cdn.contoso.com`。   

    Azure 會驗證的 CNAME 記錄存在，您所輸入的網域名稱。 如果 CNAME 是正確的會驗證您的自訂網域。  **從 Verizon Azure CDN** （標準和進階版） 端點，可能需要 90 分鐘的時間，才會傳播到所有的 CDN 邊緣節點，不過的自訂網域設定。  

    請注意，在某些情況下，可能需要時間，才會傳播到網際網路上的名稱伺服器的 CNAME 記錄。 如果您的網域不會立即，驗證您認為 CNAME 記錄正確，接著等候幾分鐘的時間，然後再試一次。


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>註冊使用中繼 cdnverify 子網域是 Azure CDN 端點的自訂網域  

1. 登入[Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [**瀏覽]**，然後**CDN 設定檔**] 然後與您想要將自訂網域名稱對應的端點 CDN 設定檔]。  
3. 在**CDN 設定檔**刀中，按一下 [與您想要建立關聯的子網域 CDN 結束點]。
4. 在端點刀頂端，按一下 [**新增自訂網域**] 按鈕。  在**新增自訂網域**刀，您會看到端點主機名稱，請從您的 CDN 端點，以用來建立新的 CNAME 記錄。 主機名稱地址的格式會顯示為**&lt;EndpointName >。 azureedge.net**。  您可以複製這個主機名稱，以用來建立 CNAME 記錄。
5. 瀏覽至您的網域註冊機構的網站，然後找出 [建立 DNS 記錄] 區段。 您可能會發現此例如**網域名稱**、 **DNS**或**名稱伺服器管理**] 區段中。
6. 管理 CNAMEs 尋找區段。 您可能要移至 [進階的設定] 頁面，並尋找文字**CNAME**，**別名**或**子網域**。
7. 建立新的 CNAME 記錄，並提供資料的子網域別名包含**cdnverify**子網域。 例如，您所指定的子網域，就會在格式**cdnverify.www**或**cdnverify.cdn**。 然後提供 [主機名稱，這是您的 CDN 端點，格式**cdnverify。&lt;EndpointName >。 azureedge.net**。
8. 返回 [**新增自訂網域**刀，並輸入您的自訂網域，包括子網域，在對話方塊中。 例如，輸入格式的網域名稱`www.contoso.com`或`cdn.contoso.com`。 請注意，在此步驟中，您不需要開頭與**cdnverify**子網域。  

    Azure 會驗證的 CNAME 記錄存在，您所輸入的 cdnverify 網域名稱。
9. 此時，已經由 Azure，驗證您的自訂網域，但流量至您的網域不尚未傳遞到您的 CDN 結束點。 等候長期允許的自訂網域設定，才會傳播到 CDN 邊緣節點 （90 分鐘**Verizon 從 Azure CDN**， **Azure CDN Akamai 從**1 到 2 分鐘），回到您的 DNS 註冊機構的網站建立其他的 CNAME 記錄後，當您 CDN 端點對應子網域。 例如，指定為**www**或**cdn**，並為主機名稱的 [子網域**&lt;EndpointName >。 azureedge.net**。 此步驟中，使用您的自訂網域註冊已完成。
10. 最後，您可以刪除使用**cdnverify**，您建立 CNAME 記錄時，必須只能作為中間的步驟。  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>驗證自訂子網域參照您 CDN 端點

- 您已完成的註冊您的自訂網域之後，您可以在您自訂網域名稱的 CDN 端點來存取快取的內容。
首先，確定您有公用快取的端點的內容。 例如，如果您的 CDN 結束點與儲存帳戶相關聯，CDN 快取公用 blob 容器中的內容。 若要測試的自訂網域，請確定您容器的設定為允許公用存取，且包含至少一個 blob。
- 在瀏覽器中瀏覽至自訂網域名稱的 blob 的地址。 例如，如果您的自訂網域`cdn.contoso.com`，會將下列 URL 類似的快取 blob 的 URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>另請參閱

[如何啟用 Azure 內容傳遞網路 」 (CDN)](./cdn-create-new-endpoint.md)  
