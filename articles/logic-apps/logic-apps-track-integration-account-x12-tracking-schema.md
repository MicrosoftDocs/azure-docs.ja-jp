---
title: B2B メッセージの X12 追跡スキーマ - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps と Enterprise Integration Pack の統合アカウントで B2B メッセージを監視する X12 追跡スキーマを作成します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195185"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Azure Logic Apps の統合アカウントで X12 メッセージを追跡するスキーマを作成する

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
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | X12 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | X12 メッセージ受信者のパートナー名。 (省略可能) |
| senderQualifier | String | 送信パートナー修飾子。 (必須) |
| senderIdentifier | String | 送信パートナー識別子。 (必須) |
| receiverQualifier | String | 受信パートナー修飾子。 (必須) |
| receiverIdentifier | String | 受信パートナー識別子。 (必須) |
| agreementName | String | メッセージが解決される X12 契約の名前。 (省略可能) |
| direction | 列挙型 | メッセージ フローの方向 (受信または送信)。 (必須) |
| interchangeControlNumber | String | インターチェンジ制御番号。 (省略可能) |
| functionalGroupControlNumber | String | 機能制御番号。 (省略可能) |
| transactionSetControlNumber | String | トランザクション セット制御番号。 (省略可能) |
| CorrelationMessageId | String | 関連付けメッセージ ID。 {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} の組み合わせです。 (省略可能) |
| messageType | String | トランザクション セットまたはドキュメントのタイプ。 (省略可能) |
| isMessageFailed | Boolean | X12 メッセージが失敗したかどうか。 (必須) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 契約で技術確認が構成されているかどうか。 (必須) |
| isFunctionalAcknowledgmentExpected | Boolean | X12 契約で機能確認が構成されているかどうか。 (必須) |
| needAk2LoopForValidMessages | Boolean | 有効なメッセージのために AK2 ループが必要かどうか。 (必須) |
| segmentsCount | 整数 | X12 トランザクション セット内のセグメントの数。 (省略可能) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 トランザクション セット受信確認追跡スキーマ

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

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | X12 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | X12 メッセージ受信者のパートナー名。 (省略可能) |
| senderQualifier | String | 送信パートナー修飾子。 (必須) |
| senderIdentifier | String | 送信パートナー識別子。 (必須) |
| receiverQualifier | String | 受信パートナー修飾子。 (必須) |
| receiverIdentifier | String | 受信パートナー識別子。 (必須) |
| agreementName | String | メッセージが解決される X12 契約の名前。 (省略可能) |
| direction | 列挙型 | メッセージ フローの方向 (受信または送信)。 (必須) |
| interchangeControlNumber | String | 機能確認のインターチェンジ制御番号。 値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 (省略可能) |
| functionalGroupControlNumber | String | 機能確認の機能グループ制御番号。 値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 (省略可能) |
| isaSegment | String | メッセージの ISA セグメント。 値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 (省略可能) |
| gsSegment | String | メッセージの GS セグメント。 値は送信側にのみ設定されます。送信側では、パートナーに送信されたメッセージについて機能確認が受信されます。 (省略可能) |
| respondingfunctionalGroupControlNumber | String | 応答インターチェンジ制御番号。 (省略可能) |
| respondingFunctionalGroupId | String | 応答機能グループ ID (受信確認の AK101 にマップされます)。 (省略可能) |
| respondingtransactionSetControlNumber | String | 応答トランザクション セット制御番号。 (省略可能) |
| respondingTransactionSetId | String | 応答トランザクション セット ID (受信確認の AK201 にマップされます)。 (省略可能) |
| StatusCode | Boolean | トランザクション セット受信確認の状態コード。 (必須) |
| segmentsCount | 列挙型 | 受信確認の状態コード。 許可されている値は、**Accepted**、**Rejected**、**AcceptedWithErrors** です。 (必須) |
| processingStatus | 列挙型 | 受信確認の処理状態。 許可されている値は、**Received**、**Generated**、**Sent** です。 (必須) |
| CorrelationMessageId | String | 関連付けメッセージ ID。 {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} の組み合わせです。 (省略可能) |
| isMessageFailed | Boolean | X12 メッセージが失敗したかどうか。 (必須) |
| ak2Segment | String | 受信された機能グループ内のトランザクション セットに対する受信確認。 (省略可能) |
| ak3Segment | String | データ セグメントでのエラーを報告します。 (省略可能) |
| ak5Segment | String | AK2 セグメント内で識別されたトランザクション セットが承認されるか拒否されるかと、その理由を報告します。 (省略可能) |
||||

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

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | X12 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | X12 メッセージ受信者のパートナー名。 (省略可能) |
| senderQualifier | String | 送信パートナー修飾子。 (必須) |
| senderIdentifier | String | 送信パートナー識別子。 (必須) |
| receiverQualifier | String | 受信パートナー修飾子。 (必須) |
| receiverIdentifier | String | 受信パートナー識別子。 (必須) |
| agreementName | String | メッセージが解決される X12 契約の名前。 (省略可能) |
| direction | 列挙型 | メッセージ フローの方向 (受信または送信)。 (必須) |
| interchangeControlNumber | String | インターチェンジ制御番号。 (省略可能) |
| isaSegment | String | メッセージの ISA セグメント。 (省略可能) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 契約で技術確認が構成されているかどうか。 (必須) |
| isMessageFailed | Boolean | X12 メッセージが失敗したかどうか。 (必須) |
| isa09 | String | X12 ドキュメントのインターチェンジ日付。 (省略可能) |
| isa10 | String | X12 ドキュメントのインターチェンジ時刻。 (省略可能) |
| isa11 | String | X12 インターチェンジ制御標準識別子。 (省略可能) |
| isa12 | String | X12 インターチェンジ制御バージョン番号。 (省略可能) |
| isa14 | String | X12 受信確認が要求されます。 (省略可能) |
| isa15 | String | テストか実稼働かのインジケーター。 (省略可能) |
| isa16 | String | 要素の区切り記号。 (省略可能) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 インターチェンジ受信確認追跡スキーマ

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

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | X12 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | X12 メッセージ受信者のパートナー名。 (省略可能) |
| senderQualifier | String | 送信パートナー修飾子。 (必須) |
| senderIdentifier | String | 送信パートナー識別子。 (必須) |
| receiverQualifier | String | 受信パートナー修飾子。 (必須) |
| receiverIdentifier | String | 受信パートナー識別子。 (必須) |
| agreementName | String | メッセージが解決される X12 契約の名前。 (省略可能) |
| direction | 列挙型 | メッセージ フローの方向 (受信または送信)。 (必須) |
| interchangeControlNumber | String | パートナーから受信された技術確認のインターチェンジ制御番号。 (省略可能) |
| isaSegment | String | パートナーから受信された技術確認の SA セグメント。 (省略可能) |
| respondingInterchangeControlNumber |String | パートナーから受信された技術確認に対するインターチェンジ制御番号。 (省略可能) |
| isMessageFailed | Boolean | X12 メッセージが失敗したかどうか。 (必須) |
| StatusCode | 列挙型 | インターチェンジ受信確認の状態コード。 許可されている値は、**Accepted**、**Rejected**、**AcceptedWithErrors** です。 (必須) |
| processingStatus | 列挙型 | 受信確認の状態。 許可されている値は、**Received**、**Generated**、**Sent** です。 (必須) |
| ta102 | String | インターチェンジ日付。 (省略可能) |
| ta103 | String | インターチェンジ時刻。 (省略可能) |
| ta105 | String | インターチェンジ通知コード。 (省略可能) |
||||

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

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | X12 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | X12 メッセージ受信者のパートナー名。 (省略可能) |
| senderQualifier | String | 送信パートナー修飾子。 (必須) |
| senderIdentifier | String | 送信パートナー識別子。 (必須) |
| receiverQualifier | String | 受信パートナー修飾子。 (必須) |
| receiverIdentifier | String | 受信パートナー識別子。 (必須) |
| agreementName | String | メッセージが解決される X12 契約の名前。 (省略可能) |
| direction | 列挙型 | メッセージ フローの方向 (受信または送信)。 (必須) |
| interchangeControlNumber | String | インターチェンジ制御番号。 (省略可能) |
| functionalGroupControlNumber | String | 機能制御番号。 (省略可能) |
| gsSegment | String | メッセージの GS セグメント。 (省略可能) |
| isTechnicalAcknowledgmentExpected | Boolean | X12 契約で技術確認が構成されているかどうか。 (必須) |
| isFunctionalAcknowledgmentExpected | Boolean | X12 契約で機能確認が構成されているかどうか。 (必須) |
| isMessageFailed | Boolean | X12 メッセージが失敗したかどうか。 (必須)|
| gs01 | String | 機能識別コード。 (省略可能) |
| gs02 | String | アプリケーション送信者のコード。 (省略可能) |
| gs03 | String | アプリケーション受信者のコード。 (省略可能) |
| gs04 | String | 機能グループ日付。 (省略可能) |
| gs05 | String | 機能グループ時刻。 (省略可能) |
| gs07 | String | 担当機関コード。 (省略可能) |
| gs08 | String | バージョン/リリース/業種識別コード。 (省略可能) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 機能グループ受信確認追跡スキーマ

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

| プロパティ | type | 説明 |
| --- | --- | --- |
| senderPartnerName | String | X12 メッセージ送信者のパートナー名。 (省略可能) |
| receiverPartnerName | String | X12 メッセージ受信者のパートナー名。 (省略可能) |
| senderQualifier | String | 送信パートナー修飾子。 (必須) |
| senderIdentifier | String | 送信パートナー識別子。 (必須) |
| receiverQualifier | String | 受信パートナー修飾子。 (必須) |
| receiverIdentifier | String | 受信パートナー識別子。 (必須) |
| agreementName | String | メッセージが解決される X12 契約の名前。 (省略可能) |
| direction | 列挙型 | メッセージ フローの方向 (受信または送信)。 (必須) |
| interchangeControlNumber | String | インターチェンジ制御番号 (パートナーから技術確認が受信された場合に、送信側に対して入力されます)。 (省略可能) |
| functionalGroupControlNumber | String | 技術確認の機能グループ制御番号 (パートナーから技術確認が受信された場合に、送信側に対して設定されます)。 (省略可能) |
| isaSegment | String | インターチェンジ制御番号と同じですが、特定の場合にのみ設定されます。 (省略可能) |
| gsSegment | String | 機能グループ制御番号と同じですが、特定の場合にのみ設定されます。 (省略可能) |
| respondingfunctionalGroupControlNumber | String | 元の機能グループの制御番号。 (省略可能) |
| respondingFunctionalGroupId | String | 受信確認機能グループ ID の AK101 にマップされます。 (省略可能) |
| isMessageFailed | Boolean | X12 メッセージが失敗したかどうか。 (必須) |
| StatusCode | 列挙型 | 受信確認の状態コード。 許可されている値は、**Accepted**、**Rejected**、**AcceptedWithErrors** です。 (必須) |
| processingStatus | 列挙型 | 受信確認の処理状態。 許可されている値は、**Received**、**Generated**、**Sent** です。 (必須) |
| ak903 | String | 受信されたトランザクション セットの数。 (省略可能) |
| ak904 | String | 識別された機能グループで承認されたトランザクション セットの数。 (省略可能) |
| ak9Segment | String | AK1 セグメントで識別された機能グループが承認されるか拒否されるかと、その理由。 (省略可能) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>B2B プロトコル追跡スキーマ

B2B プロトコル追跡スキーマ について詳しくは、次のページをご覧ください。

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [B2B カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>次の手順

* [B2B メッセージの監視方法](logic-apps-monitor-b2b-message.md)についての詳細情報。
* [Azure Monitor ログでの B2B メッセージの追跡](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)について学習します。
