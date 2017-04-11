<properties
    pageTitle="結構描述與企業整合套件概觀 |Microsoft Azure"
    description="瞭解如何使用 [企業整合套件和邏輯應用程式中的結構描述"
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>深入瞭解結構描述和企業整合套件  

## <a name="why-use-a-schema"></a>為什麼要使用的結構描述？
使用結構描述，以確認您收到的 XML 文件是有效的預期的資料中預先定義的格式。 結構描述用來驗證 B2B 情境中操作交換的訊息。

## <a name="add-a-schema"></a>新增結構描述
從 Azure 入口網站︰  

1. 選取 [**更多服務**]。  
![螢幕擷取畫面的 Azure 入口網站，醒目提示 [更多服務 」](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. 在 [篩選] 搜尋方塊中，輸入**整合**，然後從結果清單中選取 [**整合帳戶**。     
![篩選器] 搜尋方塊的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. 選取要新增的結構描述的**整合帳戶**。    
![整合帳戶清單的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. 選取 [**結構**] 方塊。  
![螢幕擷取畫面的 IEP 整合帳戶，醒目提示 [結構]](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>新增小於 2 MB 結構描述檔案  

1. 在開啟 （從先前的步驟） 的**結構描述**刀，選取 [**新增**。  
![螢幕擷取畫面的結構描述刀，「 新增 」 醒目提示] 按鈕](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. 輸入您的結構描述的名稱。 然後，若要上傳的結構描述檔案，請選取 [**結構描述**文字] 方塊旁的資料夾圖示。 上傳程序完成之後，請選取**[確定]**。    
![「 新增結構描述]，以醒目提示 [小型檔案] 的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>新增結構描述檔案超過 2 MB （上限為 8 MB）  

這個程序取決於 blob 容器存取層級︰**公用**或**沒有匿名存取**。 若要決定此存取權限層級]，在**Azure 儲存檔案總管**] 中，在**Blob 容器**，選取您想要的 blob 容器。 選取 [**安全性**]，然後選取 [**存取層級**] 索引標籤。

1. 如果**公用**blob 安全性存取層級，請遵循下列步驟。  
  ![螢幕擷取畫面的 Azure 儲存體總管] 中，使用 「 Blob 容器 」、 「 安全性 」 和 「 公用 「 醒目提示](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    。 上傳的結構描述至儲存空間，並複製 URI。  
    ![螢幕擷取畫面顯示儲存帳戶與反白顯示的 URI](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b。 在**新增的結構描述**，選取**檔案很大**，並提供在**內容 URI** ] 文字方塊中 URI。  
    ![結構描述，醒目提示 [大型檔案] 與 [新增] 按鈕的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. 如果**沒有 [匿名存取**blob 安全性存取層級，請遵循下列步驟。  
  ![螢幕擷取畫面的 Azure 儲存體總管] 中，使用 「 Blob 容器 」、 「 安全性 」 和 「 沒有匿名存取 「 醒目提示](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    。 若要儲存空間上, 傳結構描述。  
    ![儲存帳戶的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b。 產生共用的 access 簽章的結構描述。  
    ![儲存 sccount，與反白顯示共用的 access 簽章] 索引標籤的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c。 在**新增的結構描述**，選取**檔案很大**，並提供共用的 access 簽章 URI 在**內容 URI** ] 文字方塊中。  
    ![結構描述，醒目提示 [大型檔案] 與 [新增] 按鈕的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. 在 EIP 整合帳戶**結構描述**刀，您現在應該會看到最近新增的結構描述。  
![螢幕擷取畫面的 EIP 整合帳戶，「 結構描述] 與反白顯示新的結構描述](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>編輯結構描述
1. 選取 [**結構**] 方塊。  
2. 選取您想要從開啟的**結構描述**刀編輯的結構描述。
3. 在 [**結構描述**刀中，選取 [**編輯**]。  
![螢幕擷取畫面的結構描述刀](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. 選取您想要使用隨即會開啟檔案選擇器對話方塊來編輯的結構描述檔案。
5. 選取 [在檔案選擇器中的 [**開啟**]。  
![檔案選擇器的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. 您會收到通知，表示順利完成上傳。  

## <a name="delete-schemas"></a>刪除結構描述
1. 選取 [**結構**] 方塊。  
2. 選取您想要刪除開啟的**結構描述**刀的結構描述。  
3. 在 [**結構描述**刀中，選取 [**刪除**]。
![螢幕擷取畫面的結構描述刀](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. 若要確認您的選擇，請選取 [**是**]。  
![「 刪除結構描述] 確認訊息的螢幕擷取畫面](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. 最後，請注意，重新整理的**結構描述**刀中的結構描述的清單，而不會再列已刪除的結構描述。  
![螢幕擷取畫面的 EIP 整合帳戶，醒目提示 [結構]](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>後續步驟

- [深入瞭解企業整合套件](./app-service-logic-enterprise-integration-overview.md "瞭解企業整合套件")。  
