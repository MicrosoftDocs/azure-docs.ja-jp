---
title: "B2B 監視の AS2 追跡スキーマ - Azure Logic Apps | Microsoft Docs"
description: "AS2 追跡スキーマを使用して、Azure 統合アカウントのトランザクションからの B2B メッセージを監視します。"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: e9c9cfda4dda1ec3f1b002016118bd49d540e90a
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>AS2 メッセージおよび MDN の追跡を開始または有効にして、成功、失敗、メッセージのプロパティを監視する
Azure 統合アカウントでは、次の AS2 追跡スキーマを使用して企業間 (B2B) 取引の監視に役立てることができます。

* AS2 メッセージ追跡スキーマ
* AS2 MDN 追跡スキーマ

## <a name="as2-message-tracking-schema"></a>AS2 メッセージ追跡スキーマ
````java

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
````

| プロパティ | 型 | Description |
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

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 追跡スキーマ
````java

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
````

| プロパティ | 型 | Description |
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

## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) についての詳細情報。    
* [B2B メッセージの監視方法](logic-apps-monitor-b2b-message.md)についての詳細情報。   
* [B2B カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)についての詳細情報。   
* [X12 追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)についての詳細情報。   
* [Operations Management Suite ポータルでの B2B メッセージ追跡](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)についての詳細情報。

