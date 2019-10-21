---
title: Azure Data Factory の Mapping Data Flow の式ビルダー
description: Azure Data Factory の Mapping Data Flow の式ビルダー
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9862866d5cddb227d9417ac15db6b8ea851507e6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030297"
---
# <a name="mapping-data-flow-expression-builder"></a>Mapping Data Flow の式ビルダー



Azure Data Factory の Mapping Data Flow には、データ変換の式を入力できる式ボックスが表示されます。 このボックス内で、データ フローからの列、フィールド、変数、パラメーター、関数を使用します。 式を作成するには、式ビルダーを使用します。式ビルダーは、変換内の式テキスト ボックスをクリックすると起動します。 変換の列を選択したときに、[計算列] のオプションが表示されることもあります。 このオプションをクリックすると、起動した式ビルダーも表示されます。

![式ビルダー](media/data-flow/xpb1.png "式ビルダー")

式ビルダー ツールでは、テキスト エディター オプションが既定で設定されています。 オートコンプリート機能では、Azure Data Factory のデータ フロー全体からオブジェクト モデルが読み取られ、そのモデルが構文チェックされ、強調表示されます。

![式ビルダーのオートコンプリート](media/data-flow/expb1.png "式ビルダーのオートコンプリート")

## <a name="build-schemas-in-output-schema-pane"></a>[Output Schema]\(出力スキーマ\) ウィンドウにスキーマを作成する

![複合列の追加](media/data-flow/complexcolumn.png "列の追加")

左側の [Output Schema]\(出力スキーマ\) ウィンドウに、変更中の列とスキーマに追加している列が表示されます。 ここでは、単純なデータ構造と複雑なデータ構造を対話形式で作成できます。 [列の追加] を使用してフィールドを追加し、[Add subcolumn]\(サブ列の追加\) を使用して階層を構築します。

![サブ列の追加](media/data-flow/addsubcolumn.png "サブ列の追加")

## <a name="data-preview-in-debug-mode"></a>デバッグ モードでのデータのプレビュー

![式ビルダー](media/data-flow/exp4b.png "式データのプレビュー")

データ フロー式を処理しているときに、Azure Data Factory のデータ フロー デザイン サーフェイスからデバッグ モードをオンにします。これにより、作業しながら、作成中の式のデータの結果をプレビューできます。 式に対して、リアルタイムのライブ デバッグが可能です。

![デバッグ モード](media/data-flow/debugbutton.png "[Debug]\(デバッグ\) ボタン")

[更新] ボタンをクリックして、式の結果をソースのライブ サンプルに対してリアルタイムで更新します。

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
