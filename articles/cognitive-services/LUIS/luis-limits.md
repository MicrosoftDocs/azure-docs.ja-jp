---
title: 制限 - LUIS
description: この記事では、Azure Cognitive Services Language Understanding (LUIS) の既知の制限を示します。 LUIS にはいくつかの制限領域があります。 モデルの制限によって、LUIS の意図、エンティティ、および機能が制御されます。 キーの種類に基づくクォータ制限。 キーボードの組み合わせは LUIS Web サイトを制御します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97976794"
---
# <a name="limits-for-your-luis-model-and-keys"></a>LUIS モデルとキーの制限
LUIS にはいくつかの制限領域があります。 1 つは[モデルの制限](#model-limits)であり、これによって LUIS で意図、エンティティ、および機能が制御されます。 2 つ目の領域は、キーの種類に基づく[クォータ制限](#key-limits)です。 3 つ目の制限領域は、LUIS Web サイトを制御するための[キーボードの組み合わせ](#keyboard-controls)です。 4 つ目の領域は、LUIS オーサリング Web サイトと LUIS [エンドポイント](luis-glossary.md#endpoint) API の間の[世界リージョン マッピング](luis-reference-regions.md)です。

<a name="model-boundaries"></a>

## <a name="model-limits"></a>モデルの制限

アプリが LUIS モデルの制限を超えている場合は、[LUIS ディスパッチ](luis-concept-enterprise.md#dispatch-tool-and-model) アプリまたは [LUIS コンテナー](luis-container-howto.md)を使用することを検討してください。

|領域|制限|
|--|:--|
| [アプリ名][luis-get-started-create-app] | \* 既定の最大文字数 |
| アプリケーション| Azure オーサリング リソースごとに 500 アプリケーション |
| [バッチ テスト][batch-testing]| 10 データセット、データセットあたり 1000 発話|
| 明示的なリスト | アプリケーションあたり 50|
| 外部エンティティ | 無制限 |
| [意図][intents]|アプリケーションあたり 500:499 のカスタムの意図、および必須の意図 _なし_。<br>[ディスパッチ ベース](https://aka.ms/dispatch-tool) アプリケーションには対応するディスパッチ ソースが 500。|
| [リスト エンティティ](./luis-concept-entity-types.md) | 親: 50、子: 20,000 項目。 Canonical 名は *既定の最大文字数。シノニム値は長さ制限なし。 |
| [機械学習エンティティ + ロール](./luis-concept-entity-types.md):<br> 複合、<br>シンプル、<br>エンティティのロール|100 個の親エンティティの制限または 330 個のエンティティの制限のどちらかの、ユーザーが最初に達した制限。 ロールは、この制限の目的のためのエンティティとしてカウントされます。 例として、次のような 2 つのロールを持つシンプル エンティティで構成された複合があります。1 つの複合 + 1 つのシンプル + 2 つのロール = 330 エンティティのうちの 4 つ。<br>サブエンティティは 5 レベルまで入れ子にすることができ、レベルあたり最大 10 個の子を持つことができます。|
|特徴量としてのモデル| 特定のモデルに対して特徴として使用できるモデルの最大数は、10 です。 特定のモデルに対して特徴として使用できるフレーズ リストの最大数は、10 です。|
| [プレビュー - 動的なリスト エンティティ](./luis-migration-api-v3.md)|クエリ予測エンドポイント要求あたり最大 1K のうちの 2 つのリスト|
| [パターン](luis-concept-patterns.md)|アプリケーションあたり 500 パターン。<br>パターンの最大文字数: 400 文字。<br>パターンあたり 3 Pattern.any エンティティ<br>パターン内の入れ子になった省略可能なテキストの最大数: 2|
| [Pattern.any](./luis-concept-entity-types.md)|アプリケーションあたり 100、パターンあたり 3 Pattern.any エンティティ |
| [フレーズ リスト][phrase-list]|500 個のフレーズ リスト。 モデルは機能制限となっているため、10 個のグローバル語句の一覧が表示されます。 交換不可能なフレーズ リストの最大フレーズ数は 5,000 です。 交換可能なフレーズ リストの最大フレーズ数は 50,000 です。 アプリケーションごとの合計フレーズの最大数は、500,000 フレーズです。|
| [事前構築済みのエンティティ](./howto-add-prebuilt-models.md) | 制限なし|
| [正規表現エンティティ](./luis-concept-entity-types.md)|20 エンティティ<br>最大文字数: 500 文字/ 正規表現エンティティ パターンあたり|
| [ロール](./luis-concept-entity-types.md)|アプリケーションあたり 300 の役割。 エンティティあたりの 10 の役割|
| [発話][utterances] | 500 文字<br><br>この文字制限より長いテキストがある場合は、LUIS に入力する前に発話を分割する必要があり、セグメントごとに個別の意図の応答を受け取ることになります。 句読点や音声内の長い一時停止など、操作可能な明らかな中断があります。|
| [発話の例][utterances] | アプリケーションあたり 15,000 - 意図あたりの発話の数に制限はありません<br><br>追加の例でアプリケーションをトレーニングする必要がある場合は、[ディスパッチ](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) モデル アプローチを使用します。 1 つ以上の意図で個々の LUIS アプリ (親ディスパッチ アプリに対して子アプリと呼ばれる) をトレーニングし、次に各子 LUIS アプリの発話からサンプリングされたディスパッチ アプリをトレーニングして、予測要求を正しい子アプリに送ります。 |
| [バージョン](./luis-concept-app-iteration.md)| アプリケーションごとに 100 バージョン |
| [バージョン名][luis-how-to-manage-versions] | 128 文字 |

\* 既定の最大文字数: 50 文字。

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>名前の一意性

オブジェクト名は、同じレベルの他のオブジェクトと比較したときに一意である必要があります。

|オブジェクト|制限|
|--|--|
|意図、エンティティ|すべての意図とエンティティの名前は、アプリのバージョン内で一意である必要があります。|
|ML エンティティ コンポーネント|すべての機械学習エンティティ コンポーネント (子エンティティ) は、同じレベルのコンポーネントに関して、同じエンティティ内で一意である必要があります。|
|特徴 | フレーズ リストなどのすべての名前付きの特徴は、アプリのバージョン内で一意である必要があります。|
|エンティティのロール|エンティティまたはエンティティ コンポーネントのすべてのロールは、同じエンティティ レベル (親、子、孫など) である場合に一意である必要があります。|

## <a name="object-naming"></a>オブジェクト名の規則

以下の名前では、以下の文字を使用しないでください。

|Object|除外する文字|
|--|--|
|インテント、エンティティ、およびロールの名前|`:`<br>`$` <br> `&`|
|バージョン名|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>リソース使用量と上限

Language Understand には、作成用に 1 つの種類と、予測エンドポイントに対するクエリの実行用に 1 つの種類という、個別のリソースがあります。 キーの種類の違いの詳細については、「[LUIS のオーサリング キーとクエリ予測エンドポイント キー](luis-how-to-azure-subscription.md)」を参照してください。

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>作成リソースの制限

Azure portal 内のリソースをフィルター処理する場合は、_kind_、`LUIS.Authoring` を使用します。 LUIS には Azure 作成リソースごとに 500 アプリケーションという制限があります。

|リソースの作成|TPS の作成|
|--|--|
|スターター|100 万/月、5/秒|
|F0 - Free レベル |100 万/月、5/秒|

* TPS = Transactions per second (1 秒あたりのトランザクション数)

価格に関して詳しくは、[こちら][pricing]をご覧ください。

### <a name="query-prediction-resource-limits"></a>クエリ予測リソース限度

Azure portal 内のリソースをフィルター処理する場合は、_kind_、`LUIS` を使用します。ランタイムで使用される LUIS クエリ予測エンドポイント リソースは、エンドポイント クエリに対してのみ有効です。

|クエリ予測リソース|クエリ TPS|
|--|--|
|F0 - Free レベル |10,000/月、5/秒|
|S0 - Standard レベル|50/秒|

### <a name="sentiment-analysis"></a>センチメント分析

センチメント情報を提供する[センチメント分析統合](luis-how-to-publish-app.md#enable-sentiment-analysis)が提供され、別の Azure リソースは必要ありません。

### <a name="speech-integration"></a>音声統合

[音声統合](../speech-service/how-to-recognize-intents-from-speech-csharp.md)では、ユニット コスト当たり 1,000 のエンドポイント要求を提供します。

価格に関して詳しくは、[こちら][pricing]をご覧ください。

## <a name="keyboard-controls"></a>キーボード コントロール

|キーボード入力 | 説明 |
|--|--|
|Ctrl + E|トークンと発話一覧のエンティティの切り替え|

## <a name="website-sign-in-time-period"></a>Web サイトへのサインイン時間

ご自身のサインイン アクセスは **60 分** です。 この時間を過ぎると、エラーが発生します。 再度サインインする必要があります。

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
