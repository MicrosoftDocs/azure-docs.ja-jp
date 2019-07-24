---
title: SMS、電子メール、Azure アプリ プッシュ通知、webhook のレート制限
description: アクション グループから送信される可能性がある SMS、電子メール、Azure アプリ プッシュ通知、または webhook 通知の数を Azure で制限する方法を説明します。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 11fd6a2c58671cc5d0bcf0593239eb9e62aca834
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109493"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>音声、SMS、電子メール、Azure アプリ プッシュ通知、webhook 投稿のレート制限
レート制限とは、特定の電話番号、電子メール アドレス、またはデバイスに送信される通知が多すぎる場合に、通知が一時的に停止されることです。 レート制限によって、アラートを管理しやすくなりアクション可能な状態が保証されます。

レート制限のしきい値は次のとおりです。

- **SMS**: 5 分ごとに 1 件以下の SMS。
- **音声**: 5 分ごとに 1 件以下の音声通話。
- **電子メール**: 1 時間で 100 件以下の電子メール。
 
  その他のアクションは、レート制限を受けません。

## <a name="rate-limit-rules"></a>レート制限のルール
- 特定の電話番号または電子メールでしきい値で許可された数を超えるメッセージを受信した場合に、レート制限が適用されます。
- 電話番号または電子メールは、多数のサブスクリプション間のアクション グループの一部です。 レート制限はすべてのサブスクリプションに対して適用されます。 メッセージが複数のサブスクリプションから送信された場合であっても、しきい値に達するとただちに制限が適用されます。
- 電子メール アドレスにレート制限が適用されると、レート制限を伝える追加の通知が送信されます。 レート制限が期限切れになった場合は、電子メールで示されます。

## <a name="next-steps"></a>次の手順 ##
* 詳細については、「[SMS アラート動作](alerts-sms-behavior.md)」を参照してください。
* [アクティビティ ログ アラートの概要](alerts-overview.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)する方法について学習します。

