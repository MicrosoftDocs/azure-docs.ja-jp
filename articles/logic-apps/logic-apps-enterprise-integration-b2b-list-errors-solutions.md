---
title: B2B シナリオでの一般的なエラーや問題の解決策
description: Azure Logic Apps で B2B シナリオをトラブルシューティングする場合の一般的なエラーや問題の解決策について説明します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86232561"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Azure Logic Apps における B2B のエラーと解決策

この記事は、Logic Apps B2B シナリオで発生する可能性があるエラーを解決するために役立ちます。また、エラーを解決するための適切なアクションを提案します。

## <a name="agreement-resolution"></a>契約解決

### <a name="no-agreement-found"></a>契約が見つかりません 

**エラーの説明**:契約解決パラメーターを含む契約が見つかりません

**ユーザー操作**: 同意済みビジネス ID を使用して、契約を統合アカウントに追加する必要があります。 ビジネス ID は入力メッセージの ID と一致する必要があります。

### <a name="no-agreement-found-with-identities"></a>ID が <ID> の契約が見つかりません

**エラーの説明**:次の ID の契約が見つかりません。'AS2Identity'::'Partner1' and'AS2Identity'::'Partner3'

**ユーザー操作**: 契約に構成されている AS2-From または AS2-To が無効です。 契約の構成で AS2 メッセージ ヘッダーの AS2 ID と一致するように、AS2 メッセージの "AS2-From" または "AS2-To" ヘッダーまたは契約を修正します。

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Missing AS2 message headers (AS2 メッセージ ヘッダーが見つかりません)  

**エラーの説明**:AS2 ヘッダーが無効です。 "AS2-To" または "AS2-From" ヘッダーのいずれかが空です。

**ユーザー操作**: AS2-From、AS2-To、またはその両方のヘッダーが含まれていない AS2 メッセージを受信しました。 AS2 メッセージの AS2-From ヘッダーと AS2-To ヘッダーを確認し、契約の構成に基づいて修正します。

### <a name="missing-as2-message-body-and-headers"></a>Missing AS2 message body and headers (AS2 メッセージ本文とヘッダーが見つかりません)    

**エラーの説明**:要求のコンテンツが null か空です。

**ユーザー操作**: メッセージ本文が含まれていない AS2 メッセージを受信しました。

### <a name="as2-message-decryption-failure"></a>AS2 message decryption failure (AS2 メッセージの解読エラー)

**エラーの説明**: [processed/Error: decryption-failed]

**ユーザー操作**: @base64ToBinary を AS2Message に追加してからパートナーに送信します。

次に例を示します。

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>MDN decryption failure (MDN 解読エラー)

**エラーの説明**: [processed/Error: decryption-failed]

**ユーザー操作**: @base64ToBinary を MDN に追加してからパートナーに送信します。

次に例を示します。

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Missing signing certificate (署名証明書が見つかりません)

**エラーの説明**:AS2 パーティーに署名証明書が構成されていません。 AS2-From: partner1 AS2-To: partner2

**ユーザー操作**: 署名に合わせて正しい証明書を使用して AS2 の契約設定を構成します。

## <a name="x12-and-edifact"></a>X12 と EDIFACT

### <a name="leading-or-trailing-space-found"></a>先頭または末尾のスペースが見つかりました    

**エラーの説明**:解析中にエラーが発生しました。 ID '987654' のインターチェンジに含まれる ID '123456'、送信者 ID 'Partner1'、受信者 ID 'Partner2' の EDIFACT トランザクション セット (グループ以外) は、次のエラーで中断されています:"Leading Trailing separator found" (先頭または末尾の区切り記号が見つかりました)

**ユーザー操作**: 先頭と末尾のスペースを許可するように契約設定を構成します。 先頭と末尾のスペースを許可するように契約設定を編集します。

![スペースの許可](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Duplicate check has enabled in the agreement (契約で重複チェックが有効になりました)

**エラーの説明**:制御番号が重複しています

**ユーザー操作**: このエラーは、受信したメッセージの制御番号が重複していることを示します。 制御番号を修正し、メッセージを再送信します。

### <a name="missing-schema-in-the-agreement"></a>Missing schema in the agreement (契約にスキーマが見つかりません)

**エラーの説明**:解析中にエラーが発生しました。 ID '000056422' のインターチェンジ内の、ID '56422' の機能グループに含まれる ID '564220001' の X12 トランザクション セット (送信者 ID '12345678'、受信者 ID '87654321') が次のエラーで中断されています:"このメッセージは、ドキュメントの種類が不明なため、契約内で構成された既存のスキーマのいずれかに解決されませんでした"

**ユーザー操作**: 契約設定でスキーマを構成します。

### <a name="incorrect-schema-in-the-agreement"></a>Incorrect schema in the agreement (契約のスキーマが正しくありません)

**エラーの説明**:このメッセージは、ドキュメントの種類が不明なため、契約内で構成された既存のスキーマのいずれかに解決されませんでした。

**ユーザー操作**: 契約設定で正しいスキーマを構成します。

## <a name="flat-file"></a>フラット ファイル

### <a name="input-message-with-no-body"></a>Input message with no body (入力メッセージに本文がありません)

**エラーの説明**:InvalidTemplate。 行 '1'、列 '1902' のアクション 'Flat_File_Decoding' 入力でテンプレート言語式を処理できません。'Required property 'content' expects a value but got null. Path ''.' (必須のプロパティ 'content' に値を指定する必要がありますが null です。パス ''。)。

**ユーザー操作**: このエラーは、入力メッセージに本文が含まれていないことを示します。
