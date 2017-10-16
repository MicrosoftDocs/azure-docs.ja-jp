---
title: "SMS、電子メール、Webhook のレート制限 | Microsoft Docs"
description: "アクション グループから送信される可能性がある SMS、電子メールまたは webhook 通知の数を Azure で制限する方法を説明します。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>SMS メッセージ、電子メール、webhook 投稿のレート制限
レート制限とは、特定の電話番号または電子メール アドレスへの通知が多すぎる場合に、通知が一時停止されることです。 レート制限によって、アラートを管理しやすくなりアクション可能な状態が保証されます。

SMS と電子メールのルールは同じです。 レート制限のしきい値は次のとおりです。

 - **SMS**: 1 時間に 10 件のメッセージ。
 - **電子メール**: 1 時間に 100 件のメッセージ。

## <a name="rate-limit-rules"></a>レート制限のルール
- 特定の電話番号または電子メールでしきい値で許可された数を超えるメッセージを受信した場合に、レート制限が適用されます。
- 電話番号または電子メールは、多数のサブスクリプション間のアクション グループの一部です。 レート制限はすべてのサブスクリプションに対して適用されます。 メッセージが複数のサブスクリプションから送信された場合であっても、しきい値に達するとただちに制限が適用されます。  
- 1 つの電話番号または電子メールにレート制限が適用されると、通知を追加で送信してレート制限が適用されたことを通知します。 レート制限が期限切れになった場合は、通知で示されます。

## <a name="rate-limit-of-webhooks"></a>webhook のレート制限 ##
webhook 用のレート制限はありません。

## <a name="next-steps"></a>次のステップ ##
* 詳細については、「[SMS アラート動作](monitoring-sms-alert-behavior.md)」を参照してください。
* [アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法について学習します。
