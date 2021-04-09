---
title: B2B メッセージの AS2 追跡スキーマ
description: Azure Logic Apps で AS2 メッセージを監視するための追跡スキーマを作成する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76906960"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Azure Logic Apps で AS2 メッセージを追跡するためのスキーマを作成する

企業間 (B2B) 取引における成功、エラー、メッセージ プロパティは、統合アカウントで次の AS2 追跡スキーマを使って監視できます。

* AS2 メッセージ追跡スキーマ
* AS2 メッセージ処理通知 (MDN) の追跡スキーマ

## <a name="as2-message-tracking-schema"></a>AS2 メッセージ追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | AS2 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | AS2 メッセージ受信者のパートナー名 |
| as2To | はい | String | AS2 メッセージ受信者の名前 (AS2 メッセージのヘッダーから取得) |
| as2From | はい | String | AS2 メッセージ送信者の名前 (AS2 メッセージのヘッダーから取得) |
| agreementName | いいえ | String | メッセージが解決される AS2 契約の名前 |
| direction | はい | String | メッセージ フローの方向 (`receive` または `send`) |
| messageId | いいえ | String | AS2 メッセージ ID (AS2 メッセージのヘッダーから取得) |
| dispositionType | いいえ | String | メッセージ処理通知 (MDN) の処理タイプ値 |
| fileName | いいえ | String | ファイル名 (AS2 メッセージのヘッダーから取得) |
| isMessageFailed | はい | Boolean | AS2 メッセージが失敗したかどうか |
| isMessageSigned | はい | Boolean | AS2 メッセージが署名されたかどうか |
| isMessageEncrypted | はい | Boolean | AS2 メッセージが暗号化されたかどうか |
| isMessageCompressed | はい | Boolean | AS2 メッセージが圧縮されたかどうか |
| correlationMessageId | いいえ | String | AS2 メッセージ ID (メッセージを MDN と関連付けるために使用) |
| incomingHeaders | いいえ | JToken の辞書 | 受信 AS2 メッセージのヘッダー詳細 |
| outgoingHeaders | いいえ | JToken の辞書 | 送信 AS2 メッセージのヘッダー詳細 |
| isNrrEnabled | はい | Boolean | 値が不明な場合に既定値を使用するかどうか |
| isMdnExpected | はい | Boolean | 値が不明な場合にその既定値を使用するかどうか |
| mdnType | はい | 列挙型 | 使用できる値: `NotConfigured`、`Sync`、および `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | AS2 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | AS2 メッセージ受信者のパートナー名 |
| as2To | はい | String | AS2 メッセージを受信するパートナー名 |
| as2From | はい | String | AS2 メッセージを送信するパートナー名 |
| agreementName | いいえ | String | メッセージが解決される AS2 契約の名前 |
| direction | はい | String | メッセージ フローの方向 (`receive` または `send`) |
| messageId | いいえ | String | AS2 メッセージ ID |
| originalMessageId | いいえ | String | AS2 の元のメッセージ ID |
| dispositionType | いいえ | String | MDN の処理タイプ値 |
| isMessageFailed | はい | Boolean | AS2 メッセージが失敗したかどうか |
| isMessageSigned | はい | Boolean | AS2 メッセージが署名されたかどうか |
| isNrrEnabled | はい | Boolean | 値が不明な場合にその既定値を使用するかどうか |
| StatusCode | はい | 列挙型 | 使用できる値: `Accepted`、`Rejected`、および `AcceptedWithErrors` |
| micVerificationStatus | はい | 列挙型 | 使用できる値: `NotApplicable`、`Succeeded`、および `Failed` |
| correlationMessageId | いいえ | String | 関連付け ID。MDN が構成された元のメッセージの ID |
| incomingHeaders | いいえ | JToken の辞書 | 受信メッセージのヘッダー詳細 |
| outgoingHeaders | いいえ | JToken の辞書 | 送信メッセージのヘッダー詳細 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B プロトコル追跡スキーマ

B2B プロトコル追跡スキーマ について詳しくは、次のページをご覧ください。

* [X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログで B2B メッセージを監視する](../logic-apps/monitor-b2b-messages-log-analytics.md)