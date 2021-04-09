---
title: v1 から v2 への API の移行
titleSuffix: Azure Cognitive Services
description: バージョン 1 のエンドポイントとオーサリングに関する Language Understanding API は非推奨となりました。 このガイドでは、バージョン 2 のエンドポイントとオーサリング API に移行する方法について説明します。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 867ae2cc7567077786bb0840cd11c47b786be423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018754"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>LUIS アプリの API v1 から v2 への移行ガイド
バージョン 1 の[エンドポイント](https://aka.ms/v1-endpoint-api-docs)と[オーサリング](https://aka.ms/v1-authoring-api-docs) API は非推奨となりました。 このガイドでは、バージョン 2 の[エンドポイント](https://go.microsoft.com/fwlink/?linkid=2092356)と[オーサリング](https://go.microsoft.com/fwlink/?linkid=2092087) API に移行する方法について説明します。

## <a name="new-azure-regions"></a>新しい Azure リージョン
LUIS では、LUIS API 用の新しい[リージョン](./luis-reference-regions.md)が用意されています。 LUIS ではリージョン グループごとに異なるポータルが用意されています。 アプリケーションは、クエリを実行するリージョンと同じリージョンに作成する必要があります。 アプリケーションによって、リージョンが自動的に移行されることはありません。 アプリはリージョンからエクスポートして、新しいリージョンで使用できるようにインポートします。

## <a name="authoring-route-changes"></a>オーサリング ルートの変更
オーサリング API ルートは、前は **prog** ルートが使用されていましたが、**api** ルートを使用するように変更されました。


| version | route |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>エンドポイント ルートの変更
エンドポイント API には、新しいクエリ文字列パラメーターと、異なる応答があります。 verbose フラグが true の場合、topScoringIntent に加え、すべての意図が、スコアに関係なく intents という名前の配列で返されます。

| version | GET ルート |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


v1 エンドポイントの成功応答:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 エンドポイントの成功応答:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>API でのキー管理が不要
サブスクリプション エンドポイント キー API は非推奨であり、410 GONE を返します。

| version | route |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure Portal で Azure [エンドポイント キー](luis-how-to-azure-subscription.md) が生成されます。 そのキーは、**[[Publish]\(公開\)](luis-how-to-azure-subscription.md)** ページで LUIS アプリに割り当てます。 実際のキー値を確認する必要はありません。 LUIS では、サブスクリプション名を使用して、割り当てが行われます。

## <a name="new-versioning-route"></a>新しいバージョン管理ルート
v2 モデルは、[バージョン](luis-how-to-manage-versions.md)に含まれています。 バージョン名は、ルートの 10 文字です。 既定のバージョンは "0.1" です。

| version | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities|

## <a name="metadata-renamed"></a>メタデータ名の変更
LUIS メタデータを返す API のいくつかが新しい名前に変更されました。

| v1 ルート名 | v2 ルート名 |
|--|--|
|PersonalAssistantApps |assistants|
|applicationcultures|cultures|
|applicationdomains|domains|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"sample" から "suggest" への名前変更
LUIS では、モデルを強化する可能性がある既存の[エンドポイント発話](luis-how-to-review-endpoint-utterances.md)から、発話が提案されます。 以前のバージョンでは、その提案は **sample** でした。 新しいバージョンでは、名前が sample から **suggest** に変更されています。 これは、LUIS Web サイトでは **[エンドポイントの発話の確認](luis-how-to-review-endpoint-utterances.md)** と呼ばれます。

| version | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**sample**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**sample**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/**suggest**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>事前構築済みドメインからのアプリの作成
[事前構築済みのドメイン](./howto-add-prebuilt-models.md)により、定義済みドメイン モデルが提供されます。 事前構築済みのドメインを使用すると、一般的なドメインの LUIS アプリケーションを迅速に開発できます。 この API により、事前構築済みのドメインに基づいて、新しいアプリを作成することができます。 応答は新しい appID です。

|v2 ルート|Verb|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |get、post|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>1.x アプリから 2.x へのインポート
エクスポートされた 1.x アプリの JSON には、[LUIS][LUIS] 2.0 にインポートする前に変更しなければならない領域があります。

### <a name="prebuilt-entities"></a>事前構築済みのエンティティ
[事前構築済みのエンティティ](./howto-add-prebuilt-models.md)は変更されました。 V2 の事前構築済みのエンティティが使用されていることを確認してください。 これには、datetime の代わりの [datetimeV2](luis-reference-prebuilt-datetimev2.md) の使用も含まれます。

### <a name="actions"></a>アクション
アクション プロパティが有効ではなくなりました。 これは空にする必要があります

### <a name="labeled-utterances"></a>ラベル付きの発話
V1 のラベル付きの発話では、単語またはフレーズの先頭と末尾にスペースを挿入できました。 そのスペースが削除されました。

## <a name="common-reasons-for-http-response-status-codes"></a>HTTP 応答状態コードの一般的な理由
[LUIS API の応答コード](luis-reference-response-codes.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

v2 API のドキュメントを使用して、LUIS [エンドポイント](https://go.microsoft.com/fwlink/?linkid=2092356)および[オーサリング](https://go.microsoft.com/fwlink/?linkid=2092087) API に対する既存の REST 呼び出しを更新します。

[LUIS]: ./luis-reference-regions.md