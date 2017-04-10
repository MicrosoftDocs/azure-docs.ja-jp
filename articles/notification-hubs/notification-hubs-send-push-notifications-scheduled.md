---
title: "スケジュール設定された通知を送信する方法 | Microsoft Docs"
description: "このトピックでは、Azure Notification Hubs でスケジュール設定された通知を使用する方法について説明します。"
services: notification-hubs
documentationcenter: .net
keywords: "プッシュ通知,プッシュ通知,プッシュ通知のスケジュール設定"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0


---
# <a name="how-to-send-scheduled-notifications"></a>方法: スケジュール設定された通知の送信
## <a name="overview"></a>Overview
未来のある時点で通知を送信することが必要なシナリオに対して、通知を送信するためのバックエンド コードの簡単なウェイクアップ方法がないような場合、 Standard レベルの Notification Hubs では、7 日後までの通知をスケジュール設定できる機能をサポートしています。

通知を送信する場合は、次の例に示すように Notification Hubs SDK の [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) クラスを使用します。

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

また、既にスケジュール設定されている通知を、その notificationId を使用して取り消すこともできます。

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

スケジュール設定された、送信可能な通知の数に制限はありません。




<!--HONumber=Nov16_HO3-->


