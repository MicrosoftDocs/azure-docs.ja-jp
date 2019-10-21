---
title: ルーティングとタグ式
description: この記事では、Azure 通知ハブのルーティングとタグ式について説明します。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 66388f139b63c63e1f0f8ee8ee063e0ddd0f9da5
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213042"
---
# <a name="routing-and-tag-expressions"></a>ルーティングとタグ式

## <a name="overview"></a>概要

タグ式を使用することで、通知ハブを通じてプッシュ通知を送信する際に、デバイスの特定のセット、より具体的には登録を対象にできるようになります。

## <a name="targeting-specific-registrations"></a>特定の登録を対象にする

特定の登録を対象にする唯一の方法は、登録にタグを関連付け、そのタグを対象とすることです。 「 [登録管理](notification-hubs-push-notification-registration-management.md)」で説明したように、プッシュ通知を受け取るには、アプリケーションが通知ハブにデバイス ハンドルを登録する必要があります。 通知ハブに登録が作成されたら、アプリケーションのバックエンドが登録にプッシュ通知を送信できます。 アプリケーションのバックエンドは、次のように、特定の通知を対象とする登録を選択できます。

1. **ブロードキャスト**:通知ハブのすべての登録が通知を受信します。
2. **タグ**:指定したタグを含むすべての登録が通知を受信します。
3. **タグ式**:タグのセットが指定した式と一致するすべての登録が、通知を受信します。

## <a name="tags"></a>Tags

タグは、最大 120 文字の任意の文字列です。英数字と一部の英数字以外の文字 ("_"、"@"、"#"、"."、":"、"-") を使用できます。 ‘_’, ‘@’, ‘#’, ‘.’, ‘:’, ‘-’.次の例は、特定の音楽グループに関するトースト通知を受信するアプリケーションを示します。 このシナリオでは、次の図に示すように、通知をルーティングするための単純な方法として、さまざまなバンドを表すタグを使用して登録にラベルを付けます。

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

この図で、 **[Beatles]** というタグが付いたメッセージは、タグ **[Beatles]** で登録されたタブレットにのみ到達します。

タグの登録の作成の詳細については、「[登録管理](notification-hubs-push-notification-registration-management.md)」を参照してください。

[Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK の `Microsoft.Azure.NotificationHubs.NotificationHubClient` クラスの send notifications メソッドを使用して、タグに通知を送信することができます。 Node.js、または Push Notifications REST APIs を使用することもできます。  SDK を使用する例を次に示します。

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

タグは複数のアプリケーション固有概念を参照できるため、事前にプロビジョニングする必要はありません。 たとえば、このサンプル アプリケーションのユーザーがバンドについてコメントでき、自分のお気に入りのバンドのコメントだけでなく、自分がコメントしていないバンドに関する友人のすべてのコメントについてもトーストを受信したいとします。 以下の図はこのシナリオの例を示したものです。

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

この図で、Alice は Beatles の近況に関心があり、Bob は Wailers の近況に関心があります。 Bob は Charlie のコメントにも関心があり、Charlie は Wailers に関心があります。 Beatles に関する Charlie のコメントに関する通知が送信されると、Alice と Bob の両方に配信されます。

タグ (たとえば、"band_Beatles" や "follows_Charlie" など) 内では複数の関心をエンコードできますが、タグは単純な文字列であり、値を持つプロパティではありません。 登録は、特定のタグの有無のみを照合します。

関心のグループに送信するためのタグの使用方法の詳しい手順を示したチュートリアルは、「 [ニュース速報](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)」を参照してください。

> [!NOTE]
> Azure Notification Hubs では、登録ごとに最大 60 個のタグがサポートされます。

## <a name="using-tags-to-target-users"></a>ターゲット ユーザーにタグを使用する

タグを使用するもう 1 つの方法は、特定のユーザーのすべてのデバイスを識別する方法です。 次の図に示すように、ユーザー ID を含むタグを使用して登録にタグ付けすることができます。

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

この図では、uid:Alice のタグが付けられたメッセージは、"uid:Alice" というタグが付けられたすべての登録、つまり Alice のすべてのデバイスに到達します。

## <a name="tag-expressions"></a>タグ式

1 つのタグではなく、タグのブール式で識別される登録のセットが通知の対象になる場合もあります。

レッド ソックスとカーディナルズの試合に関してボストンのすべてのユーザーに通知を送信するスポーツ アプリケーションを考えてみてください。 クライアントのアプリケーションでチームと場所の関心に関するタグを登録すると、レッド ソックスまたはカーディナルズに関心のあるボストンのすべてのユーザーが通知の対象になります。 この条件は、次のブール式で表すことができます。

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

タグ式には AND (&&)、OR (||)、NOT (!) などのすべてのブール演算子を含むことができます。 かっこを使用することもできます。 タグ式に OR のみが含まれる場合、タグの数は 20 個に制限されます。それ以外の場合は、6 個のタグに制限されます。

SDK でタグ式を使用して通知を送信する例を次に示します。

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
