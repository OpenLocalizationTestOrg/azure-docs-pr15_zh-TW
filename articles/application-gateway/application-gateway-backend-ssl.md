<properties
   pageTitle="啟用 SSL 原則] 和 [應用程式閘道的端對端 SSL |Microsoft Azure"
   description="本頁面提供端對端 SSL 支援的應用程式閘道器的概觀。"
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>啟用 SSL 原則] 和 [應用程式閘道的端對端 SSL

## <a name="overview"></a>概觀

應用程式閘道支援在閘道，SSL 終止之後的後端伺服器的流量通常是傳送未加密。 這個選項可讓網頁伺服器，才能從高加密/解密開銷 unburdened。 不過的一些客戶加密後端伺服器通訊不是可接受的選項。 這可能是因為安全性/規範要求或應用程式可能只接受安全連線。 這類應用程式，應用程式閘道現在支援端對端加密。

[結束] 結束 SSL 可讓您將機密資料後端加密仍然運用 7 層負載平衡功能的優勢安全地傳送哪些應用程式閘道器提供，例如 cookie 相關性 URL 路由，支援路由根據網站或注入 X 的能力-轉寄-* 標題。

設定的端對端 SSL 通訊模式，應用程式閘道結束閘道使用者 SSL 工作階段，並解密使用者流量。 然後適用於設定的規則，以選取路由流量到適當的後端資料庫執行個體。 應用程式閘道然後啟動新 SSL 連線到後端伺服器，並重新來加密資料使用的後端伺服器的公開金鑰憑證之前傳輸後端的要求。 [結束] 結束 SSL 會設定為 Https，套用到後端資料庫 BackendHTTPSetting 通訊協定設定啟用。 已啟用的端對端 SSL 的後端資料庫中的每個後端伺服器必須設定為允許安全通訊的憑證。

![端對端 ssl 案例][1]

在此範例中，使用 TLS1.2 要求傳閱使用端對端 SSL Pool1 中的後端伺服器。

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>[結束] 結束] 和 [SSL 憑證的家

應用程式閘道只會與具有 whitelisted 其應用程式閘道器憑證的已知的後端執行個體通訊。 若要啟用家的憑證，您必須公開金鑰後端伺服器憑證的上傳至應用程式閘道器 （不根憑證）。 然後允許已知及 whitelisted backends 只連線。 閘道器錯誤剩餘 backends 結果。 自我簽署的憑證是只有和不建議使用生產負載測試之用。 這類憑證也必須與應用程式閘道中所述上述步驟可供使用前 whitelisted。

## <a name="application-gateway-ssl-policy"></a>應用程式閘道器 SSL 原則

應用程式閘道支援使用者可設定 SSL 交涉原則，可在應用程式閘道讓客戶更多的控制 SSL 連線。

1. SSL 2.0 和 3.0 依預設停用所有應用程式閘道器。 他們完全不可設定。
2. SSL 原則定義可讓您要停用任何下列 3 通訊協定-TLSv1 選項\_0，TLSv1\_1，TLSv1\_2。
3. 如果沒有 SSL 原則定義所有三 (TLSv1\_0，TLSv1\_1，TLSv1_2) 會啟用。

## <a name="next-steps"></a>後續步驟

之後瞭解能夠以 backends 流量傳送加密的表單中建立的應用程式閘道器的 [結束] 結束 SSL 以及 SSL 原則，移至 [[啟動應用程式閘道的端對端 SSL](application-gateway-end-to-end-ssl-powershell.md) 。

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png