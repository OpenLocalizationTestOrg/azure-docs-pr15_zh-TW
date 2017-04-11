<properties
    pageTitle="如何傳送已排程的通知 |Microsoft Azure"
    description="本主題說明 Azure 通知集線器中使用排定的通知。"
    services="notification-hubs"
    documentationCenter=".net"
    keywords="推入通知，推入通知，排程推入通知"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>如何︰ 傳送已排程的通知


##<a name="overview"></a>概觀

如果您有您想要傳送通知給有些時候以後，但沒有輕鬆喚醒傳送通知給您後端的程式碼設定的情況。 標準層通知集線器支援此功能可讓您設定排程通知，為未來 7 天。

當傳送通知，只要使用[ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx)類別通知集線器 sdk 如下列範例所示︰

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

此外，您可以取消使用其 notificationId 先前排程的通知︰

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

有無限制的已排程的通知，您可以傳送數。