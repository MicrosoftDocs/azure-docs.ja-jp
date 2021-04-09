---
title: 制限と境界 - QnA Maker
description: QnA Maker には、ナレッジ ベースとサービスの一部について、メタデータの制限があります。 テストして発行するためには、これらの制限内にナレッジ ベースを維持することが重要です。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1e57ae537c271e61f0b2d37f5320cb177b04802b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98164874"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker ナレッジ ベースの制限と境界

以下に示す QnA Maker の制限は、[Azure Cognitive Search の価格レベルの制限](../../search/search-limits-quotas-capacity.md)と [QnA Maker の価格レベルの制限](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)を組み合わせたものです。 リソースごとにどの程度の数のナレッジベースを作成できて、各ナレッジベースをどこまで拡張できるかを理解するには、両方の制限をセットで知っておく必要があります。

## <a name="knowledge-bases"></a>ナレッジ ベース

ナレッジ ベースの最大数は、[Azure Cognitive Search レベルの制限](../../search/search-limits-quotas-capacity.md)に基づきます。

|**Azure Cognitive Search レベル** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|許可される発行済みナレッジ ベースの最大数|2|14|49|199|199|2,999|

 たとえば、レベルに 15 個の許可されたインデックスがある場合、14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックス `testkb` は、作成およびテスト用にすべてのナレッジ ベースで使用されます。

## <a name="extraction-limits"></a>抽出の制限

### <a name="file-naming-constraints"></a>ファイルの名前付けの制約

ファイル名に次の文字を含めることはできません。

|使用できない文字|
|--|
|単一引用符 `'`|
|二重引用符 `"`|

### <a name="maximum-file-size"></a>ファイルの最大サイズ

|Format|最大ファイル サイズ (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>ファイルの最大数

抽出できるファイルの最大数と最大ファイル サイズは、 **[QnA Maker の価格レベルの制限](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** に基づきます。

> [!NOTE]
> QnA Maker マネージド (プレビュー) は、追加できるソースの数に制限がない無料のサービスです。 管理 API と予測 API の両方に対して、スループットは現在、毎秒 10 トランザクションに制限されています。

### <a name="maximum-number-of-deep-links-from-url"></a>URL からのディープリンクの最大数

URL ページから QnA を抽出するためにクロールできるディープリンクの最大数は **20** です。

## <a name="metadata-limits"></a>メタデータの制限

メタデータは、テキストベースの "キー:値" のペアとして表示されます (`product:windows 10` など)。 これは小文字で格納され、比較されます。

### <a name="by-azure-cognitive-search-pricing-tier"></a>Azure Cognitive Search の価格レベル

ナレッジ ベースごとのメタデータ フィールドの最大数は、 **[Azure Cognitive Search レベルの制限](../../search/search-limits-quotas-capacity.md)** に基づきます。

|**Azure Cognitive Search レベル** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|QnA Maker サービスごとの最大のメタデータ フィールド数 (すべてのナレッジ ベースにわたって)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>名前と値

次の表に、メタデータの名前および値に使用できる文字と長さを示します。

|Item|使用できる文字|正規表現パターン マッチ|最大文字数|
|--|--|--|--|
|名前 (キー)|以下の文字を使用可能:<br>英数字<br>`_` (アンダースコア)<br> スペースを含めることはできません。|`^[a-zA-Z0-9_]+$`|100|
|値|以下を除くすべての文字を使用可能:<br>`:` (コロン)<br>`|` (縦棒)<br>使用できる値は 1 つだけです。|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>ナレッジ ベースのコンテンツの制限
ナレッジ ベース内のコンテンツの全体的な制限は以下のとおりです。
* 回答のテキストの長さ: 25,000 文字
* 質問のテキストの長さ: 1,000 文字
* メタデータ キーのテキストの長さ: 100 文字
* メタデータ値のテキストの長さ: 500 文字
* メタデータ名でサポートされる文字: アルファベット、数字、`_`
* メタデータ値でサポートされる文字: `:` と `|` を除くすべての文字
* ファイル名の長さ: 200
* サポートされるファイル形式: ".tsv"、".pdf"、".txt"、".docx"、".xlsx"
* 代替の質問の最大数: 該当なし
* 質問と回答のペアの最大数: 選択した **[Azure Cognitive Search レベル](../../search/search-limits-quotas-capacity.md#document-limits)** によって異なります。 質問と回答のペアは、Azure Cognitive Search インデックスのドキュメントにマップされます。
* URL/HTML ページ: 100 万文字

## <a name="create-knowledge-base-call-limits"></a>ナレッジ ベースの作成の呼び出しの制限
これらは、ナレッジ ベース作成操作 (つまり、 *[KB を作成する]* のクリック、または CreateKnowledgeBase API の呼び出し) ごとの制限を表します。
* 回答ごとの代替質問の推奨最大数:該当なし
* URL の最大数: 10
* ファイルの最大数: 10
* 呼び出しごとに許可される QnA の最大数:1000

## <a name="update-knowledge-base-call-limits"></a>ナレッジ ベースの更新の呼び出しの制限
これらは、更新操作 (つまり、 *[Save and train]\(保存してトレーニング\)* のクリック、または UpdateKnowledgeBase API の呼び出し) ごとの制限を表します。
* 各ソース名の長さ: 該当なし
* 追加または削除される代替質問の推奨最大数:該当なし
* 追加または削除されるメタデータ フィールドの最大数: 10
* 更新可能な URL の最大数: 5
* 呼び出しごとに許可される QnA の最大数:1000

## <a name="next-steps"></a>次のステップ

[サービス価格レベル](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)を変更するタイミングと方法について学びます。
