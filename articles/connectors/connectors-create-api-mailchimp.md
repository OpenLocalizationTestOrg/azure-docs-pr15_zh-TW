<properties
pageTitle="MailChimp |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 MailChimp 是 SaaS 」 服務讓企業管理和自動化電子郵件行銷活動，包括傳送電子郵件行銷、 自動化的郵件和目標廣告活動。"
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-mailchimp-connector"></a>快速入門 MailChimp 連接器

MailChimp 是 SaaS 」 服務讓企業管理和自動化電子郵件行銷活動，包括傳送電子郵件行銷、 自動化的郵件和目標廣告活動。


>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

為的動作; 可 MailChimp 連接器有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。

 MailChimp 連接線具有下列的動作及/或可用的觸發程序︰

### <a name="mailchimp-actions"></a>MailChimp 動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|建立新的行銷活動，根據行銷活動類型，收件者清單和行銷活動設定 （主旨、 標題、 from_name 及 reply_to）|
|[newlist](connectors-create-api-mailchimp.md#newlist)|在您 MailChimp 帳戶中建立新的清單|
|[連結](connectors-create-api-mailchimp.md#addmember)|新增或更新清單成員|
|[removemember](connectors-create-api-mailchimp.md#removemember)|從清單刪除成員。|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|更新特定清單成員的資訊|
### <a name="mailchimp-triggers"></a>MailChimp 引動程序
您可以接聽這些事件︰

|觸發程序 | 描述|
|--- | ---|
|當已成員新增至清單|觸發工作流程，當新成員已新增至清單|
|建立新清單的時機|觸發工作流程時建立新的清單|


## <a name="create-a-connection-to-mailchimp"></a>建立 MailChimp 的連線
若要建立 MailChimp 邏輯應用程式，您必須先建立的**連線**，然後提供下列屬性的詳細資料︰

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供 MailChimp 認證|

>[AZURE.INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="reference-for-mailchimp"></a>MailChimp 參考
適用於版本︰ 1.0

## <a name="newcampaign"></a>newcampaign
新的行銷活動︰ 建立新的行銷活動，根據行銷活動類型，收件者清單和行銷活動設定 （主旨、 標題、 from_name 及 reply_to）

```POST: /campaigns```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|newCampaignRequest| |[是]|本文|無|傳送本文中的新的行銷活動要求參數 Json 物件|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="newlist"></a>newlist
新的清單︰ MailChimp 帳戶中建立新的清單

```POST: /lists```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|newListRequest| |[是]|本文|無|傳送本文中的新的行銷活動要求參數 Json 物件|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="addmember"></a>連結
將成員新增至清單︰ 新增或更新清單成員

```POST: /lists/{list_id}/members```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|字串|[是]|路徑|無|清單的唯一識別碼|
|newMemberInList| |[是]|本文|無|若要以新的成員資訊本文中傳送 Json 物件|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="removemember"></a>removemember
從清單中移除成員︰ 刪除清單中的成員。

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|字串|[是]|路徑|無|清單的唯一識別碼|
|member_email|字串|[是]|路徑|無|若要刪除的成員的電子郵件地址|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="updatemember"></a>updatemember
更新成員資訊︰ 更新特定清單成員的資訊

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|字串|[是]|路徑|無|清單的唯一識別碼|
|member_email|字串|[是]|路徑|無|若要更新的成員唯一的電子郵件地址|
|updateMemberInListRequest| |[是]|本文|無|若要更新的成員資訊本文中傳送 Json 物件|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="onmembersubscribed"></a>OnMemberSubscribed
為清單新增成員︰ 觸發工作流程，當新成員已新增至清單

```GET: /trigger/lists/{list_id}/members```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|字串|[是]|路徑|無|清單的唯一識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|202|接受|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="oncreatelist"></a>OnCreateList
建立新清單的時機︰ 觸發工作流程時建立新的清單

```GET: /trigger/lists```

沒有這個通話的參數
#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|202|接受|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義

### <a name="newcampaignrequest"></a>NewCampaignRequest


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|類型|字串|[是] |
|收件者|未定義|[是] |
|設定|未定義|[是] |
|variate_settings|未定義|無 |
|追蹤|未定義|無 |
|rss_opts|未定義|無 |
|social_card|未定義|無 |



### <a name="recipient"></a>收件者


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|list_id|字串|[是] |
|segment_opts|未定義|無 |



### <a name="settings"></a>設定


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|subject_line|字串|[是] |
|標題|字串|無 |
|from_name|字串|[是] |
|reply_to|字串|[是] |
|use_conversation|布林值|無 |
|to_name|字串|無 |
|folder_id|整數|無 |
|驗證|布林值|無 |
|auto_footer|布林值|無 |
|inline_css|布林值|無 |
|auto_tweet|布林值|無 |
|auto_fb_post|陣列|無 |
|fb_comments|布林值|無 |



### <a name="variatesettings"></a>Variate_Settings


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|winner_criteria|字串|無 |
|wait_time|整數|無 |
|test_size|整數|無 |
|subject_lines|陣列|無 |
|send_times|陣列|無 |
|from_names|陣列|無 |
|reply_to_addresses|陣列|無 |



### <a name="tracking"></a>追蹤


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|隨後便會開啟|布林值|無 |
|html_clicks|布林值|無 |
|text_clicks|布林值|無 |
|goal_tracking|布林值|無 |
|ecomm360|布林值|無 |
|google_analytics|字串|無 |
|clicktale|字串|無 |
|salesforce|未定義|無 |
|highrise|未定義|無 |
|（盒)|未定義|無 |



### <a name="rssopts"></a>RSS_Opts


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|feed_url|字串|無 |
|頻率|字串|無 |
|constrain_rss_img|字串|無 |
|排程|未定義|無 |



### <a name="socialcard"></a>Social_Card


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|image_url|字串|無 |
|描述|字串|無 |
|標題|字串|無 |



### <a name="segmentopts"></a>Segment_Opts


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|saved_segment_id|整數|無 |
|符合的項目|字串|無 |



### <a name="salesforce"></a>Salesforce


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|行銷活動|布林值|無 |
|備忘稿|布林值|無 |



### <a name="highrise"></a>Highrise


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|行銷活動|布林值|無 |
|備忘稿|布林值|無 |



### <a name="capsule"></a>（盒)


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|備忘稿|布林值|無 |



### <a name="schedule"></a>排程


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|小時|整數|無 |
|daily_send|未定義|無 |
|weekly_send_day|字串|無 |
|monthly_send_date|數字|無 |



### <a name="dailysend"></a>Daily_Send


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|星期日|布林值|無 |
|星期一|布林值|無 |
|星期二|布林值|無 |
|星期三|布林值|無 |
|星期四|布林值|無 |
|星期五|布林值|無 |
|星期六|布林值|無 |



### <a name="campaignresponsemodel"></a>CampaignResponseModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|無 |
|類型|字串|無 |
|create_time|字串|無 |
|archive_url|字串|無 |
|狀態|字串|無 |
|emails_sent|整數|無 |
|send_time|字串|無 |
|content_type|字串|無 |
|收件者|陣列|無 |
|設定|未定義|無 |
|variate_settings|未定義|無 |
|追蹤|未定義|無 |
|rss_opts|未定義|無 |
|ab_split_opts|未定義|無 |
|social_card|未定義|無 |
|report_summary|未定義|無 |
|delivery_status|未定義|無 |
|_links|陣列|無 |



### <a name="absplitopts"></a>AB_Split_Opts


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|split_test|字串|無 |
|pick_winner|字串|無 |
|wait_units|字串|無 |
|wait_time|整數|無 |
|split_size|整數|無 |
|from_name_a|字串|無 |
|from_name_b|字串|無 |
|reply_email_a|字串|無 |
|reply_email_b|字串|無 |
|subject_a|字串|無 |
|subject_b|字串|無 |
|send_time_a|字串|無 |
|send_time_b|字串|無 |
|send_time_winner|字串|無 |



### <a name="reportsummary"></a>Report_Summary


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|隨後便會開啟|整數|無 |
|unique_opens|整數|無 |
|open_rate|數字|無 |
|按下滑鼠按鈕|整數|無 |
|subscriber_clicks|數字|無 |
|click_rate|數字|無 |



### <a name="deliverystatus"></a>Delivery_Status


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|啟用|布林值|無 |
|can_cancel|布林值|無 |
|狀態|字串|無 |
|emails_sent|整數|無 |
|emails_canceled|整數|無 |



### <a name="link"></a>連結


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|相關|字串|無 |
|href|字串|無 |
|方法|字串|無 |
|targetSchema|字串|無 |
|結構描述|字串|無 |



### <a name="newlistrequest"></a>NewListRequest


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|名稱|字串|[是] |
|連絡人|未定義|[是] |
|permission_reminder|字串|[是] |
|use_archive_bar|布林值|無 |
|campaign_defaults|未定義|[是] |
|notify_on_subscribe|字串|無 |
|notify_on_unsubscribe|字串|無 |
|email_type_option|布林值|[是] |
|可見性|字串|無 |



### <a name="contact"></a>連絡人


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|公司|字串|[是] |
|address1|字串|[是] |
|地址 2|字串|無 |
|縣/市|字串|[是] |
|狀態|字串|[是] |
|郵遞區號|字串|[是] |
|國家/地區|字串|[是] |
|電話|字串|[是] |



### <a name="campaigndefaults"></a>Campaign_Defaults


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|from_name|字串|[是] |
|from_email|字串|[是] |
|主旨|字串|無 |
|語言|字串|[是] |



### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|[是] |
|名稱|字串|[是] |
|連絡人|未定義|[是] |
|permission_reminder|字串|[是] |
|use_archive_bar|布林值|無 |
|campaign_defaults|未定義|[是] |
|notify_on_subscribe|字串|無 |
|notify_on_unsubscribe|字串|無 |
|date_created|字串|無 |
|list_rating|整數|無 |
|email_type_option|布林值|[是] |
|subscribe_url_short|字串|無 |
|subscribe_url_long|字串|無 |
|beamer_address|字串|無 |
|可見性|字串|無 |
|模組|陣列|無 |
|統計資料|未定義|無 |
|_links|陣列|無 |



### <a name="stats"></a>統計資料


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|member_count|整數|無 |
|unsubscribe_count|整數|無 |
|cleaned_count|整數|無 |
|member_count_since_send|整數|無 |
|unsubscribe_count_since_send|整數|無 |
|cleaned_count_since_send|整數|無 |
|campaign_count|整數|無 |
|campaign_last_sent|整數|無 |
|merge_field_count|整數|無 |
|avg_sub_rate|數字|無 |
|avg_unsub_rate|數字|無 |
|target_sub_rate|數字|無 |
|open_rate|數字|無 |
|click_rate|數字|無 |
|last_sub_date|字串|無 |
|last_unsub_date|字串|無 |



### <a name="getlistsresponsemodel"></a>GetListsResponseModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|清單|陣列|無 |
|total_items|整數|無 |



### <a name="newmemberinlistrequest"></a>NewMemberInListRequest


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|email_type|字串|無 |
|狀態|字串|[是] |
|merge_fields|未定義|無 |
|興趣|字串|無 |
|語言|字串|無 |
|vip|布林值|無 |
|位置|未定義|無 |
|意義的內容|字串|[是] |



### <a name="firstandlastname"></a>FirstAndLastName


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|FNAME|字串|無 |
|加以|字串|無 |



### <a name="location"></a>位置


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|緯度|數字|無 |
|經緯度|數字|無 |



### <a name="memberresponsemodel"></a>MemberResponseModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|無 |
|意義的內容|字串|無 |
|unique_email_id|字串|無 |
|email_type|字串|無 |
|狀態|字串|無 |
|merge_fields|未定義|無 |
|興趣|字串|無 |
|統計資料|未定義|無 |
|ip_signup|字串|無 |
|timestamp_signup|字串|無 |
|ip_opt|字串|無 |
|timestamp_opt|字串|無 |
|member_rating|整數|無 |
|last_changed|字串|無 |
|語言|字串|無 |
|vip|布林值|無 |
|email_client|字串|無 |
|位置|未定義|無 |
|last_note|未定義|無 |
|list_id|字串|無 |
|_links|陣列|無 |



### <a name="lastnote"></a>Last_Note


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|note_id|整數|無 |
|created_at|字串|無 |
|created_by|字串|無 |
|附註|字串|無 |



### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|成員|陣列|無 |
|list_id|字串|無 |
|total_items|整數|無 |



### <a name="object"></a>物件






### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|意義的內容|字串|無 |
|email_type|字串|無 |
|狀態|字串|[是] |
|merge_fields|未定義|無 |
|興趣|字串|無 |
|語言|字串|無 |
|vip|布林值|無 |
|位置|未定義|無 |



### <a name="getmembersresponsemodel"></a>GetMembersResponseModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|成員|陣列|無 |
|list_id|字串|無 |
|total_items|整數|無 |



### <a name="adduserresponsemodel"></a>AddUserResponseModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|[是] |
|意義的內容|字串|[是] |
|unique_email_id|字串|無 |
|email_type|字串|無 |
|狀態|字串|無 |
|merge_fields|未定義|[是] |
|興趣|字串|無 |
|統計資料|未定義|無 |
|ip_signup|字串|無 |
|timestamp_signup|字串|無 |
|ip_opt|字串|無 |
|timestamp_opt|字串|無 |
|member_rating|整數|無 |
|last_changed|字串|無 |
|語言|字串|無 |
|vip|布林值|無 |
|email_client|字串|無 |
|位置|未定義|無 |
|last_note|未定義|無 |
|list_id|字串|無 |
|_links|陣列|無 |


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)
