---
title: Azure Notification Hubs で APNS VOIP 通知を送信する
description: Azure Notification Hubs (公式にはサポートされていません) 経由で APNS VOIP 通知を送信する方法について説明します。
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80137651"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Notification Hubs (公式にはサポートされていません) 経由で APNS VOIP を使用する

Azure Notification Hubs で APNS VOIP 通知を使用することができます。ただし、このシナリオは正式にはサポートされていません。

## <a name="considerations"></a>考慮事項

Notification Hubs 経由で APNS VOIP 通知を送信することを選択した場合は、次の制限事項に注意してください。

- VOIP 通知を送信するには、`apns-topic` ヘッダーをアプリケーション バンドル ID + `.voip` サフィックスに設定する必要があります。 たとえば、バンドル ID が `com.microsoft.nhubsample` のサンプル アプリの場合、`apns-topic` ヘッダーを `com.microsoft.nhubsample.voip.` に設定する必要があります。

   アプリのバンドル ID はハブの APNS 資格情報の一部として構成する必要があり、値を変更できないため、この方法は Azure Notification Hubs では適切に機能しません。 また、Notification Hubs では、`apns-topic` ヘッダーの値を実行時にオーバーライドすることはできません。

   VOIP 通知を送信するには、`.voip` アプリ バンドル ID を使用して別の通知ハブを構成する必要があります。

- VOIP 通知を送信するには、`apns-push-type` ヘッダーを値 `voip` に設定する必要があります。

   お客様による iOS 13 への移行を支援するために、Notification Hubs では `apns-push-type` ヘッダーの正しい値の推論が試行されます。 標準の通知が中断されることのないよう、推論ロジックは意図的に単純になっています。 しかし、Apple では、標準の通知と同じ規則に従っていない特殊なケースとして VOIP 通知が扱われるため、この方法では VOIP 通知に関連する問題が発生します。

   VOIP 通知を送信するには、`apns-push-type` ヘッダーに明示的な値を指定する必要があります。

- Apple によって説明されているように、Notification Hubs では APNS ペイロードが 4 KB に制限されます。 VOIP 通知の場合、Apple では最大 5 KB のペイロードが許可されます。 Notification Hubs では、標準の通知と VOIP 通知が区別されないため、すべての通知が 4 KB に制限されます。

   VOIP 通知を送信するには、4 KB のペイロード サイズの制限を超えないようにする必要があります。

## <a name="next-steps"></a>次のステップ

詳細については、次のリンクを参照してください。

- [`apns-topic` および `apns-push-type` のヘッダーと値に関するドキュメント (VOIP 通知の特殊なケースを含む)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)。

- [ペイロード サイズの制限に関するドキュメント](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)。

- [Notification Hubs の更新 (iOS 13 関連)](push-notification-updates-ios-13.md#apns-push-type)。
