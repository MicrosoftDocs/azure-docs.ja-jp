---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 98c9d83424e7d347bb6e1166a3b00304d39e0cce
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095456"
---
[ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis) バックエンドを使用して、最新かつ最適な[インストール](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations)方法を使用しているクライアントの[デバイス登録](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration)を処理します。 また、サービスは、クロスプラットフォーム方式でプッシュ通知を送信します。 

これらの操作は[バックエンド操作用の Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) を使用して処理されます。 全体的なアプローチの詳細については、[アプリ バックエンドからの登録](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend)に関するドキュメントを参照してください。
