<properties 
    pageTitle="Widevine 授權範本概觀 |Microsoft Azure" 
    description="本主題提供用於設定 Widevine 授權 Widevine 授權範本的概觀。" 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="widevine-license-template-overview"></a>Widevine 授權範本概觀

##<a name="overview"></a>概觀

Azure 媒體服務現在可讓您設定，並要求 Widevine 授權。 當使用者播放程式嘗試播放您 Widevine 受保護的內容時，會傳送要求給授權服務取得授權。 如果授權服務核准要求，它會發出授權會傳送給客戶，並可用於解密並播放指定的內容。

Widevine 授權要求為 JSON 訊息的格式。  

請注意，您可以選擇使用沒有值只 」 {} 」 建立空的郵件將所有預設值以建立授權範本。  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>JSON 訊息

名稱 | 值 | 描述
---|---|---
內容 |Base64 編碼字串 |傳送用戶端的授權要求。 
content_id | Base64 編碼字串|識別項，用於為每個 content_key_specs.track_type 衍生 KeyId(s) 與內容索引鍵。
提供者 |字串 |用來查詢內容索引鍵和原則。 所需。
policy_name | 字串 |先前已註冊原則的名稱。 選擇性
allowed_track_types | 列舉  | SD_ONLY 或 SD_HD。 控制項的內容索引鍵應該會包含在授權
content_key_specs | 陣列 JSON 的結構，請參閱**內容索引鍵規格**下方 | 更妥善地範圍上內容的按鍵來傳回控制項。 如需詳細資訊，請參閱下方內容金鑰規格。  只有一個 allowed_track_types 和 content_key_specs 可以指定。 
use_policy_overrides_exclusively | 布林值。 true 或 false | 使用原則 policy_overrides 所指定的屬性，並略過所有先前儲存的原則。
policy_overrides | JSON 結構，請參閱**原則會覆寫**下 | 此授權的原則設定。  在事件這項資產有預先定義的原則，就會使用這些指定的值。 
session_init | JSON 結構，請參閱**工作階段初始化**下方 | 選用資料傳遞到授權。
parse_only | 布林值。 true 或 false | 剖析的授權要求，但沒有授權會發出。 不過，值在回應中傳回的授權要求的表單。  

##<a name="content-key-specs"></a>內容索引鍵的規格 

如果已存在的原則，有不需要在內容索引鍵規格中指定的任何值。  與此內容相關的已存在原則會用於決定 HDCP 等 CGMS 輸出保護。  如果已存在的原則未註冊與 Widevine 授權伺服器，內容提供者可以插入授權要求的值。   


每個 content_key_specs 必須指定所有的追蹤，無論選項 use_policy_overrides_exclusively。 


名稱 | 值 | 描述
---|---|---
content_key_specs。 track_type | 字串 | 追蹤類型名稱。 如果 content_key_specs 指定授權邀請中，確認指定所有明確追蹤類型。 若要這麼做會造成失敗播放過去 10 秒內。 
content_key_specs  <br/> security_level | uint32 | 定義播放的用戶端強固需求。 <br/> 1-需要軟體式箱加密。 <br/> 2-軟體加密和模糊的解碼器需要。 <br/> 3-的材料和加密作業，都必須執行硬體備份信任的執行環境。 <br/> 4-加密和解碼的內容，必須執行硬體備份信任的執行環境。  <br/> 5-硬體備份信任的執行環境中，則必須處理加密、 解碼與媒體 （壓縮和未壓縮） 的所有處理。  
content_key_specs <br/> required_output_protection.hdc | 字串-下列其中一項︰ HDCP_NONE，HDCP_V1，HDCP_V2 | 指出是否已具備 HDCP
content_key_specs <br/>索引鍵 | Base64 <br/>編碼的字串|要用於此追蹤的內容索引鍵。 如果指定的 track_type 或 key_id 需要。  這個選項可讓您要插入，而不是讓 Widevine 授權伺服器產生或查閱鍵此追蹤的內容索引鍵的內容提供者。
content_key_specs.key_id| Base64 編碼的字串二進位，16 位元 | 索引鍵的唯一識別碼。 


##<a name="policy-overrides"></a>原則覆寫 

名稱 | 值 | 描述
---|---|---
policy_overrides。 can_play | 布林值。 true 或 false | 指出允許內容的播放。 預設值為 false。
policy_overrides。 can_persist | 布林值。 true 或 false |指出授權可能會保留靜態供離線使用的儲存空間。 預設值為 false。
policy_overrides。 can_renew | 布林值 true 或 false |表示允許此授權的更新。 如果為 true，可以延伸期間授權活動訊號。 預設值為 false。 
policy_overrides。 license_duration_seconds | int64 | 指出此特定授權的時間視窗。 0 表示有工期沒有限制。 預設值為 0。 
policy_overrides。 rental_duration_seconds | int64 | 允許播放時，會指出時間範圍。 0 表示有工期沒有限制。 預設值為 0。 
policy_overrides。 playback_duration_seconds | int64 | [檢視] 視窗的時間後授權工期內就會開始播放。 0 表示有工期沒有限制。 預設值為 0。 
policy_overrides。 renewal_server_url |字串 | 所有此授權活動訊號 （更新） 要求應該會導向至指定的 URL。 如果 can_renew 為 true，只會用這個欄位。
policy_overrides。 renewal_delay_seconds |int64 |License_start_time 之前先嘗試更新之後的秒數。 如果 can_renew 為 true，只會用這個欄位。 預設值為 0 
policy_overrides。 renewal_retry_interval_seconds | int64 | 指定延遲，後續的授權更新要求，若失敗之間的秒數。 如果 can_renew 為 true，只會用這個欄位。 
policy_overrides。 renewal_recovery_duration_seconds | int64 | 視窗] 允許的時間，在其中播放，繼續更新時嘗試，但由於失敗後端伺服器問題的授權。 0 表示有工期沒有限制。 如果 can_renew 為 true，只會用這個欄位。
policy_overrides。 renew_with_usage | 布林值 true 或 false |指出授權應該可更新的使用方式是開始。 如果 can_renew 為 true，只會用這個欄位。 

##<a name="session-initialization"></a>工作階段初始化

名稱 | 值 | 描述
---|---|---
provider_session_token | Base64 編碼字串 |此工作階段權杖會傳遞回授權，並會存在於後續續訂。  工作階段權杖會保留超出工作階段。 
provider_client_token | Base64 編碼字串 | 若要傳送回授權回應的用戶端權杖。  如果授權要求包含的用戶端權杖，就會忽略此值。 用戶端權杖會仍舊授權工作階段。
override_provider_client_token | 布林值。 true 或 false |如果 false 和授權要求包含的用戶端權杖，使用從要求權杖，即使在此結構中指定的用戶端權杖。  如果為 true，一律使用此結構中指定的 token。

##<a name="configure-your-widevine-licenses-using-net-types"></a>設定使用.NET 類型 Widevine 授權

媒體服務會提供可讓您設定 Widevine 授權的.NET Api。 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>媒體服務.NET SDK 中所定義的類別

以下是這些類型的定義。

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>範例

下列範例會示範如何使用.NET Api 設定簡單 Widevine 授權。

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>另請參閱

[使用 PlayReady 及/或 Widevine 動態一般加密](media-services-protect-with-drm.md)
