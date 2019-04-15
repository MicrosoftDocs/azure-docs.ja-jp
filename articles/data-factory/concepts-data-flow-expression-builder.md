---
title: Azure Data Factory の Mapping Data Flow の式ビルダー
description: Azure Data Factory の Mapping Data Flow の式ビルダー
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: df9cfb0c0e36f54c8b1fbee4def552c78e9d42c1
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359779"
---
# <a name="mapping-data-flow-expression-builder"></a>Mapping Data Flow の式ビルダー

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory の Mapping Data Flow には、データ変換の式を入力できる式ボックスが表示されます。 このボックス内で、データ フローからの列、フィールド、変数、パラメーター、関数を使用します。 式を作成するには、式ビルダーを使用します。式ビルダーは、変換内の式テキスト ボックスをクリックすると起動します。 変換の列を選択したときに、[計算列] のオプションが表示されることもあります。 このオプションをクリックすると、起動した式ビルダーも表示されます。

![式ビルダー](media/data-flow/expression.png "式ビルダー")

式ビルダー ツールでは、テキスト エディター オプションが既定で設定されています。 オートコンプリート機能では、Azure Data Factory のデータ フロー全体からオブジェクト モデルが読み取られ、そのモデルが構文チェックされ、強調表示されます。

![式ビルダーのオートコンプリート](media/data-flow/expb1.png "式ビルダーのオートコンプリート")

## <a name="currently-working-on-field"></a>[Currently Working on]\(現在作業中\) フィールド

![式ビルダー](media/data-flow/exp3.png "現在作業中")

式ビルダー UI の左上には、[Currently Working On]\(現在作業中\) というフィールドと、現在作業中のフィールドの名前が表示されます。 UI で作成した式は、その現在作業中のフィールドに適用されます。 別のフィールドを変換する場合は、現在の作業を保存した後、このドロップダウンを使用して別のフィールドを選択し、他のフィールドの式を作成します。

## <a name="data-preview-in-debug-mode"></a>デバッグ モードでのデータのプレビュー

![式ビルダー](media/data-flow/exp4b.png "式データのプレビュー")

式を処理しているときに、Azure Data Factory のデータ フロー デザイン サーフェイスから、必要に応じてデバッグ モードをオンにできます。これにより、作業しながら、作成中の式のデータの結果をプレビューできます。 式に対して、リアルタイムのライブ デバッグが可能です。

![デバッグ モード](media/data-flow/debugbutton.png "[Debug]\(デバッグ\) ボタン")


![式ビルダー](media/data-flow/exp5.png "式データのプレビュー")

## <a name="comments"></a>説明

単一行および複数行コメントの構文を使用して、式にコメントを追加します。

![コメント](media/data-flow/comments.png "コメント")

## <a name="regular-expressions"></a>正規表現

Azure Data Factory の Data Flow の式言語では ([詳細なリファレンス ドキュメントはこちら](https://aka.ms/dataflowexpressions))、正規表現構文を含む関数を使用できます。 正規表現関数を使用する場合、式ビルダーでは、円記号 (\\) をエスケープ文字シーケンスとして解釈することが試みられます。 正規表現で円記号を使用するときは、正規表現全体をティック (\`) 内に入れるか、円記号を二重で使用します。

ティックの使用例

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

円記号を二重で使用した場合

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>配列インデックスのアドレス指定

配列を返す式関数では、角かっこ [] を使用して、返される配列オブジェクト内の特定のインデックスをアドレス指定します。 この配列は、1 ベースです。

![式ビルダーの配列](media/data-flow/expb2.png "式データのプレビュー")

## <a name="handling-names-with-special-characters"></a>特殊文字を使用した名前の処理

特殊文字またはスペースが含まれている列名がある場合は、その名前を中かっこを使って囲みます。
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>次の手順

[データ変換式の作成を開始する](data-flow-expression-functions.md)
