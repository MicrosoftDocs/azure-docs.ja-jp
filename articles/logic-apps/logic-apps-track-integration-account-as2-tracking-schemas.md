---
title: B2B メッセージの AS2 追跡スキーマ - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps と Enterprise Integration Pack の統合アカウントで B2B メッセージを監視する AS2 追跡スキーマを作成します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193519"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Azure Logic Apps の統合アカウントで AS2 メッセージと MDN を追跡するスキーマを作成する

企業間 (B2B) 取引における成功、エラー、メッセージ プロパティは、統合アカウントで次の AS2 追跡スキーマを使って監視できます。

* AS2 メッセージ追跡スキーマ
* AS2 MDN 追跡スキーマ

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
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | AS2 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | AS2 メッセージ受信者のパートナー名。 (省略可能) |
| as2To | String | AS2 メッセージ受信者の名前 (AS2 メッセージのヘッダーから取得します)。 (必須) |
| as2From | String | AS2 メッセージ送信者の名前 (AS2 メッセージのヘッダーから取得します)。 (必須) |
| agreementName | String | メッセージが解決される AS2 契約の名前。 (省略可能) |
| direction | String | メッセージ フローの方向 (受信または送信)。 (必須) |
| messageId | String | AS2 メッセージ ID (AS2 メッセージのヘッダーから取得します)(省略可能) |
| dispositionType |String | Message Disposition Notification (MDN) のディスポジション タイプ値。 (省略可能) |
| fileName | String | ファイル名 (AS2 メッセージのヘッダーから取得します)。 (省略可能) |
| isMessageFailed |Boolean | AS2 メッセージが失敗したかどうか。 (必須) |
| isMessageSigned | Boolean | AS2 メッセージが署名されたかどうか。 (必須) |
| isMessageEncrypted | Boolean | AS2 メッセージが暗号化されたかどうか。 (必須) |
| isMessageCompressed |Boolean | AS2 メッセージが圧縮されたかどうか。 (必須) |
| correlationMessageId | String | AS2 メッセージ ID (メッセージを MDN と関連付けるために使用します)。 (省略可能) |
| incomingHeaders |JToken の辞書 | 受信 AS2 メッセージのヘッダー詳細。 (省略可能) |
| outgoingHeaders |JToken の辞書 | 送信 AS2 メッセージのヘッダー詳細。 (省略可能) |
| isNrrEnabled | Boolean | 値が不明の場合は、既定値を使用してください。 (必須) |
| isMdnExpected | Boolean | 値が不明の場合は、既定値を使用してください。 (必須) |
| mdnType | 列挙型 | 許可されている値は、**NotConfigured**、**Sync**、**Async** です。 (必須) |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
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

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | AS2 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | AS2 メッセージ受信者のパートナー名。 (省略可能) |
| as2To | String | AS2 メッセージを受信するパートナー名。 (必須) |
| as2From | String | AS2 メッセージを送信するパートナー名。 (必須) |
| agreementName | String | メッセージが解決される AS2 契約の名前。 (省略可能) |
| direction |String | メッセージ フローの方向 (受信または送信)。 (必須) |
| messageId | String | AS2 メッセージ ID。 (省略可能) |
| originalMessageId |String | AS2 の元のメッセージ ID。 (省略可能) |
| dispositionType | String | MDN のディスポジション タイプ値。 (省略可能) |
| isMessageFailed |Boolean | AS2 メッセージが失敗したかどうか。 (必須) |
| isMessageSigned |Boolean | AS2 メッセージが署名されたかどうか。 (必須) |
| isNrrEnabled | Boolean | 値が不明の場合は、既定値を使用してください。 (必須) |
| StatusCode | 列挙型 | 許可されている値は、**Accepted**、**Rejected**、**AcceptedWithErrors** です。 (必須) |
| micVerificationStatus | 列挙型 | 許可されている値は、**NotApplicable**、**Succeeded**、**Failed** です。 (必須) |
| correlationMessageId | String | 関連付け ID。 元のメッセージ ID (MDN の構成対象であるメッセージのメッセージ ID) です。 (省略可能) |
| incomingHeaders | JToken の辞書 | 受信メッセージのヘッダー詳細を示します。 (省略可能) |
| outgoingHeaders |JToken の辞書 | 送信メッセージのヘッダー詳細を示します。 (省略可能) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B プロトコル追跡スキーマ

B2B プロトコル追跡スキーマ について詳しくは、次のページをご覧ください。

* [X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>次の手順

* [B2B メッセージの監視方法](logic-apps-monitor-b2b-message.md)についての詳細情報
* [Azure Monitor ログでの B2B メッセージの追跡](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)について学習します