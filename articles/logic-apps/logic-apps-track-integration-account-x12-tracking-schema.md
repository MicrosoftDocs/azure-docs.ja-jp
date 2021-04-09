---
title: B2B メッセージの X12 追跡スキーマ
description: Azure Logic Apps で X12 メッセージを監視するための追跡スキーマを作成する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76905304"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Azure Logic Apps で X12 メッセージを追跡するためのスキーマを作成する

企業間 (B2B) 取引における成功、エラー、メッセージ プロパティは、統合アカウントで次の X12 追跡スキーマを使って監視できます。

* X12 トランザクション セット追跡スキーマ
* X12 トランザクション セット受信確認追跡スキーマ
* X12 インターチェンジ追跡スキーマ
* X12 インターチェンジ受信確認追跡スキーマ
* X12 機能グループ追跡スキーマ
* X12 機能グループ受信確認追跡スキーマ

## <a name="x12-transaction-set-tracking-schema"></a>X12 トランザクション セット追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | X12 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | X12 メッセージ受信者のパートナー名 |
| senderQualifier | はい | String | 送信パートナー修飾子 |
| senderIdentifier | はい | String | 送信パートナー識別子 |
| receiverQualifier | はい | String | 受信パートナー修飾子 |
| receiverIdentifier | はい | String | 受信パートナー識別子 |
| agreementName | いいえ | String | メッセージが解決される X12 契約の名前 |
| direction | はい | 列挙型 | メッセージ フローの方向 (`receive` または `send`) |
| interchangeControlNumber | いいえ | String | インターチェンジ制御番号 |
| functionalGroupControlNumber | いいえ | String | 機能制御番号 |
| transactionSetControlNumber | いいえ | String | トランザクション セット制御番号 |
| CorrelationMessageId | いいえ | String | 関連付けメッセージ ID は、{AgreementName} *{GroupControlNumber}* {TransactionSetControlNumber} の組み合わせです |
| messageType | いいえ | String | トランザクション セットまたはドキュメントのタイプ |
| isMessageFailed | はい | Boolean | X12 メッセージが失敗したかどうか |
| isTechnicalAcknowledgmentExpected | はい | Boolean | X12 契約で技術確認が構成されているかどうか |
| isFunctionalAcknowledgmentExpected | はい | Boolean | X12 契約で機能確認が構成されているかどうか |
| needAk2LoopForValidMessages | はい | Boolean | 有効なメッセージのために AK2 ループが必要かどうか |
| segmentsCount | いいえ | Integer | X12 トランザクション セット内のセグメントの数 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 トランザクション セット受信確認追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | X12 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | X12 メッセージ受信者のパートナー名 |
| senderQualifier | はい | String | 送信パートナー修飾子 |
| senderIdentifier | はい | String | 送信パートナー識別子 |
| receiverQualifier | はい | String | 受信パートナー修飾子 |
| receiverIdentifier | はい | String | 受信パートナー識別子 |
| agreementName | いいえ | String | メッセージが解決される X12 契約の名前 |
| direction | はい | 列挙型 | メッセージ フローの方向 (`receive` または `send`) |
| interchangeControlNumber | いいえ | String | 機能確認のインターチェンジ制御番号。 値は送信側にのみ設定されます。送信側では、パートナーに値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 |
| functionalGroupControlNumber | いいえ | String | 機能確認の機能グループ制御番号。 値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 |
| isaSegment | いいえ | String | メッセージの ISA セグメント。 値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 |
| gsSegment | いいえ | String | メッセージの GS セグメント。 値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 |
| respondingfunctionalGroupControlNumber | いいえ | String | 応答インターチェンジ制御番号 |
| respondingFunctionalGroupId | いいえ | String | 応答機能グループ ID (受信確認の AK101 にマップされます) |
| respondingtransactionSetControlNumber | いいえ | String | 応答トランザクション セット制御番号 |
| respondingTransactionSetId | いいえ | String | 応答トランザクション セット ID (受信確認の AK201 にマップされます) |
| StatusCode | はい | Boolean | トランザクション セット受信確認の状態コード |
| segmentsCount | はい | 列挙型 | 次の許可された値を持つ受信確認の状態コード: `Accepted`、`Rejected`、 `AcceptedWithErrors` |
| processingStatus | はい | 列挙型 | 次の許可された値を持つ受信確認の処理状態: `Received`、`Generated`、 `Sent` |
| CorrelationMessageId | いいえ | String | 関連付けメッセージ ID は、{AgreementName} *{GroupControlNumber}* {TransactionSetControlNumber} の組み合わせです |
| isMessageFailed | はい | Boolean | X12 メッセージが失敗したかどうか |
| ak2Segment | いいえ | String | 受信された機能グループ内のトランザクション セットに対する受信確認 |
| ak3Segment | いいえ | String | データ セグメントでのエラーを報告します |
| ak5Segment | いいえ | String | AK2 セグメント内で識別されたトランザクション セットが承認されるか拒否されるかと、その理由を報告します |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12 インターチェンジ追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | X12 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | X12 メッセージ受信者のパートナー名 |
| senderQualifier | はい | String | 送信パートナー修飾子 |
| senderIdentifier | はい | String | 送信パートナー識別子 |
| receiverQualifier | はい | String | 受信パートナー修飾子 |
| receiverIdentifier | はい | String | 受信パートナー識別子 |
| agreementName | いいえ | String | メッセージが解決される X12 契約の名前 |
| direction | はい | 列挙型 | メッセージ フローの方向 (`receive` または `send`) |
| interchangeControlNumber | いいえ | String | インターチェンジ制御番号 |
| isaSegment | いいえ | String | メッセージの ISA セグメント |
| isTechnicalAcknowledgmentExpected | Boolean | X12 契約で技術確認が構成されているかどうか  |
| isMessageFailed | はい | Boolean | X12 メッセージが失敗したかどうか |
| isa09 | いいえ | String | X12 ドキュメントのインターチェンジ日付 |
| isa10 | いいえ | String | X12 ドキュメントのインターチェンジ時刻 |
| isa11 | いいえ | String | X12 インターチェンジ制御標準識別子 |
| isa12 | いいえ | String | X12 インターチェンジ制御バージョン番号 |
| isa14 | いいえ | String | X12 受信確認が要求されます |
| isa15 | いいえ | String | テストか実稼働かのインジケーター |
| isa16 | いいえ | String | 要素の区切り記号 |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 インターチェンジ受信確認追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | X12 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | X12 メッセージ受信者のパートナー名 |
| senderQualifier | はい | String | 送信パートナー修飾子 |
| senderIdentifier | はい | String | 送信パートナー識別子 |
| receiverQualifier | はい | String | 受信パートナー修飾子 |
| receiverIdentifier | はい | String | 受信パートナー識別子 |
| agreementName | いいえ | String | メッセージが解決される X12 契約の名前 |
| direction | はい | 列挙型 | メッセージ フローの方向 (`receive` または `send`) |
| interchangeControlNumber | いいえ | String | パートナーから受信された技術確認のインターチェンジ制御番号 |
| isaSegment | いいえ | String | パートナーから受信された技術確認の SA セグメント |
| respondingInterchangeControlNumber | いいえ | String | パートナーから受信された技術確認に対するインターチェンジ制御番号 |
| isMessageFailed | はい | Boolean | X12 メッセージが失敗したかどうか |
| StatusCode | はい | 列挙型 | 次の許可された値を持つインターチェンジ受信確認の状態コード: `Accepted`、`Rejected`、 `AcceptedWithErrors` |
| processingStatus | はい | 列挙型 | 次の許可された値を持つ受信確認の状態: `Received`、`Generated`、 `Sent` |
| ta102 | いいえ | String | インターチェンジ日付 |
| ta103 | いいえ | String | インターチェンジ時刻 |
| ta105 | いいえ | String | インターチェンジ通知コード |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 機能グループ追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | X12 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | X12 メッセージ受信者のパートナー名 |
| senderQualifier | はい | String | 送信パートナー修飾子 |
| senderIdentifier | はい | String | 送信パートナー識別子 |
| receiverQualifier | はい | String | 受信パートナー修飾子 |
| receiverIdentifier | はい | String | 受信パートナー識別子 |
| agreementName | いいえ | String | メッセージが解決される X12 契約の名前 |
| direction | はい | 列挙型 | メッセージ フローの方向 (受信または送信) |
| interchangeControlNumber | いいえ | String | インターチェンジ制御番号 |
| functionalGroupControlNumber | いいえ | String | 機能制御番号 |
| gsSegment | いいえ | String | メッセージの GS セグメント |
| isTechnicalAcknowledgmentExpected | はい | Boolean | X12 契約で技術確認が構成されているかどうか |
| isFunctionalAcknowledgmentExpected | はい | Boolean | X12 契約で機能確認が構成されているかどうか |
| isMessageFailed | はい | Boolean | X12 メッセージが失敗したかどうか |
| gs01 | いいえ | String | 機能識別コード |
| gs02 | いいえ | String | アプリケーション送信者のコード |
| gs03 | いいえ | String | アプリケーション受信者のコード |
| gs04 | いいえ | String | 機能グループ日付 |
| gs05 | いいえ | String | 機能グループ時刻 |
| gs07 | いいえ | String | 担当機関コード |
| gs08 | いいえ | String | バージョン/リリース/業界識別コード |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 機能グループ受信確認追跡スキーマ

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| senderPartnerName | いいえ | String | X12 メッセージ送信者のパートナー名 |
| receiverPartnerName | いいえ | String | X12 メッセージ受信者のパートナー名 |
| senderQualifier | はい | String | 送信パートナー修飾子 |
| senderIdentifier | はい | String | 送信パートナー識別子 |
| receiverQualifier | はい | String | 受信パートナー修飾子 |
| receiverIdentifier | はい | String | 受信パートナー識別子 |
| agreementName | いいえ | String | メッセージが解決される X12 契約の名前 |
| direction | はい | 列挙型 | メッセージ フローの方向 (`receive` または `send`) |
| interchangeControlNumber | いいえ | String | インターチェンジ制御番号 (パートナーから技術確認が受信された場合に、送信側に対して入力されます) |
| functionalGroupControlNumber | いいえ | String | 技術確認の機能グループ制御番号 (パートナーから技術確認が受信された場合に、送信側に対して設定されます) |
| isaSegment | いいえ | String | インターチェンジ制御番号と同じですが、特定の場合にのみ設定されます |
| gsSegment | いいえ | String | 機能グループ制御番号と同じですが、特定の場合にのみ設定されます |
| respondingfunctionalGroupControlNumber | いいえ | String | 元の機能グループの制御番号 |
| respondingFunctionalGroupId | いいえ | String | 受信確認機能グループ ID の AK101 にマップされます |
| isMessageFailed | Boolean | X12 メッセージが失敗したかどうか |
| StatusCode | はい | 列挙型 | 次の許可された値を持つ受信確認の状態コード: `Accepted`、`Rejected`、 `AcceptedWithErrors` |
| processingStatus | はい | 列挙型 | 次の許可された値を持つ受信確認の処理状態: `Received`、`Generated`、 `Sent` |
| ak903 | いいえ | String | 受信されたトランザクション セットの数 |
| ak904 | いいえ | String | 識別された機能グループで承認されたトランザクション セットの数 |
| ak9Segment | いいえ | String | AK1 セグメントで識別された機能グループが承認されるか拒否されるかと、その理由 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B プロトコル追跡スキーマ

B2B プロトコル追跡スキーマ について詳しくは、次のページをご覧ください。

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログで B2B メッセージを監視する](../logic-apps/monitor-b2b-messages-log-analytics.md)