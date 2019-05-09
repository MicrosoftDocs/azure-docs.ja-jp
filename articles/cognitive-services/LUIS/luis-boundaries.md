---
title: 制限
titleSuffix: Language Understanding - Azure Cognitive Services
description: この記事では、Azure Cognitive Services Language Understanding (LUIS) の既知の制限を示します。 LUIS には、複数の境界領域があります。 モデル境界は LUIS で意図、エンティティ、および機能を制御します。 キーの種類に基づくクォータ制限。 キーボードの組み合わせは LUIS Web サイトを制御します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/12/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 51f5355e365d0dcf7a103deab3356f015e75641e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897515"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS モデルとキーの境界
LUIS には、複数の境界領域があります。 1 つは[モデル境界](#model-boundaries)で、これにより LUIS で意図、エンティティ、および機能が制御されます。 2 つ目の領域は、キーの種類に基づく[クォータ制限](#key-limits)です。 3 つ目の境界領域は、LUIS Web サイトを制御するための[キーボードの組み合わせ](#keyboard-controls)です。 4 つ目の領域は、LUIS オーサリング Web サイトと LUIS [エンドポイント](luis-glossary.md#endpoint) API の間の[世界リージョン マッピング](luis-reference-regions.md)です。 


## <a name="model-boundaries"></a>モデル境界

アプリが LUIS モデルの制限と境界を超えている場合は、[LUIS ディスパッチ](luis-concept-enterprise.md#dispatch-tool-and-model) アプリまたは [LUIS コンテナー](luis-container-howto.md)を使用することを検討してください。 

|領域|制限|
|--|:--|
| [アプリ名][luis-get-started-create-app] | * 既定の最大文字数 |
| [バッチ テスト][batch-testing]| 10 データセット、データセットあたり 1000 発話|
| 明示的なリスト | アプリケーションあたり 50|
| [意図][intents]|アプリケーションあたり 500:499 のカスタムの意図、および必須の意図 _なし_。<br>[ディスパッチ ベース](https://aka.ms/dispatch-tool) アプリケーションには対応するディスパッチ ソースが 500。|
| [リスト エンティティ](./luis-concept-entity-types.md) | 親: 50、子: 20,000 項目。 Canonical 名は *既定の最大文字数。シノニム値は長さ制限なし。 |
| [機械学習エンティティ](./luis-concept-entity-types.md):<br> 複合、<br>  階層構造<br> シンプル|100 の親エンティティ (階層の子は含まない) または 330 のエンティティ (階層の子を含む) のいずれの制限。どちらも最初にユーザーのヒット数を制限します。<br><br>階層の例は、それぞれに 10 個の子がある 30 の階層になります。  これらの子が合計の 300 を使用し、階層の要素が残りの 30 を使用します。 |
| [パターン](luis-concept-patterns.md)|アプリケーションあたり 500 パターン。<br>パターンの最大文字数: 400 文字。<br>パターンあたり 3 Pattern.any エンティティ<br>パターン内の入れ子になった省略可能なテキストの最大数: 2|
| [Pattern.any](./luis-concept-entity-types.md)|アプリケーションあたり 100、パターンあたり 3 Pattern.any エンティティ |
| [フレーズ リスト][phrase-list]|10 フレーズ リスト、リストあたり 5,000 項目|
| [事前構築済みのエンティティ](./luis-prebuilt-entities.md) | 制限なし|
| [正規表現エンティティ](./luis-concept-entity-types.md)|20 エンティティ<br>最大文字数: 500 文字/ 正規表現エンティティ パターンあたり|
| [ロール](luis-concept-roles.md)|アプリケーションあたり 300 の役割。 エンティティあたりの 10 の役割|
| [発話][utterances] | 500 文字|
| [発話][utterances] | アプリケーションあたり 15,000 - 意図あたりの発話の数に制限はありません|
| [バージョン](luis-concept-version.md)| 制限なし |
| [バージョン名][luis-how-to-manage-versions] | 10 文字。英数字とピリオド (.) に限定 |

* 既定の最大文字数: 50 文字。 

## <a name="intent-and-entity-naming"></a>意図とエンティティの名前付け
意図およびエンティティの名前では、次の文字を使用しないでください。

|Character|Name|
|--|--|
|`{`|左の中かっこ|
|`}`|右の中かっこ|
|`[`|左の角かっこ|
|`]`|右の角かっこ|
|`\`|円記号|

## <a name="key-usage"></a>キー使用法

Language Understand には、作成用に 1 つの種類と、予測エンドポイントに対するクエリの実行用に 1 つの種類という、個別のキーがあります。 キーの種類の違いの詳細については、「[LUIS のオーサリング キーとクエリ予測エンドポイント キー](luis-concept-keys.md)」を参照してください。

## <a name="key-limits"></a>キーの制限

オーサリング キーでは、オーサリングとエンドポイントで制限が異なります。 LUIS サービス エンドポイント キーは、エンドポイント クエリに対してのみ有効です。


|キー|Authoring|エンドポイント|目的|
|--|--|--|--|
|Language Understanding オーサリング/スターター|100 万/月、5/秒|1,000/月、5/秒|ご自身の LUIS アプリの作成|
|Language Understanding [サブスクリプション][pricing] - F0 - Free レベル |無効|10,000/月、5/秒|ご自身の LUIS エンドポイントへのクエリの実行|
|Language Understanding [サブスクリプション][pricing] - S0 - Basic レベル|無効|50/秒|ご自身の LUIS エンドポイントへのクエリの実行|
|Cognitive Service [サブスクリプション][pricing] - S0 - Standard レベル|無効|50/秒|ご自身の LUIS エンドポイントへのクエリの実行|
|[感情分析の統合](luis-how-to-publish-app.md#enable-sentiment-analysis)|無効|課金なし|キー フレーズ データの抽出など、センチメント情報の追加 |
|音声統合|無効|5.50 米国ドル/1,000 エンドポイント要求|音声発話をテキスト発話に変換して、LUIS 結果を返す|

## <a name="keyboard-controls"></a>キーボード コントロール

|キーボード入力 | 説明 | 
|--|--|
|Ctrl + E|トークンと発話一覧のエンティティの切り替え|

## <a name="website-sign-in-time-period"></a>Web サイトへのサインイン時間

ご自身のサインイン アクセスは **60 分**です。 この時間を過ぎると、エラーが発生します。 再度サインインする必要があります。

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
