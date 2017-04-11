<properties
    pageTitle="Azure 行動互動的 android SDK 整合"
    description="說明如何在 Android 應用程式中整合 Azure 行動互動 SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Azure 行動互動的 android SDK 整合

> [AZURE.SELECTOR]
- [通用 Windows](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS](mobile-engagement-ios-sdk-overview.md)
- [Android](mobile-engagement-android-sdk-overview.md)

這份文件將說明整合及設定的所有選項可用 Azure 行動互動 Android SDK。

## <a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>進階的功能

### <a name="reporting-features"></a>報表功能

您可以新增下列功能︰

1. [進階報表的選項](mobile-engagement-android-advanced-reporting.md)
2. [位置報告] 選項](mobile-engagement-android-location-reporting.md)
3. [進階的設定選項](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>通知︰
[如何將整合 Android 應用程式中的範圍 （通知）](mobile-engagement-android-integrate-engagement-reach.md)

1. Google 雲端訊息 (GCM):[如何將整合 GCM 與行動裝置的互動](mobile-engagement-android-gcm-integrate.md)

2. Amazon 裝置訊息 (ADM):[如何將整合 ADM 與行動裝置的互動](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>標記計劃實作︰
[如何使用 Android 應用程式中標記 API 進階的行動互動](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>版本資訊

### <a name="423-08102016"></a>4.2.3 (08/10/2016)

 - 沒有更多的 WIFI 鎖定。
 - 呼叫 getDeviceId 之前初始化 (bug 4.2.0 中推出) 時，請修正死結。

所有的版本，請參閱[完整版本資訊](mobile-engagement-android-release-notes.md)。

## <a name="upgrade-procedures"></a>升級程序

如果您已經有整合我們 SDK 的舊版應用程式，請參閱[升級程序](mobile-engagement-android-upgrade-procedure.md)。
