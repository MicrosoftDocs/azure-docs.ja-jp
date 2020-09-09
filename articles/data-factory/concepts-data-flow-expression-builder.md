---
title: マッピング データ フローでの式ビルダー
description: Azure Data Factory のマッピング データ フローで式ビルダーを使用して式を構築する
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f522812f762b55ec61794101e6cd1ec15fb171ca
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212108"
---
# <a name="build-expressions-in-mapping-data-flow"></a>マッピング データ フローで式を構築する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

マッピング データ フローでは、多くの変換プロパティが式として入力されます。 これらの式は、実行時に Spark データ型に評価される列の値、パラメーター、関数、演算子、リテラルで構成されます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>式ビルダーを開く

Azure Data Factory ユーザー エクスペリエンスの式編集インターフェイスは、式ビルダーと呼ばれます。 式のロジックを入力すると、データ ファクトリでは、強調表示、構文チェック、およびオートコンプリートのために [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) コード補完が使用されます。

![式ビルダー](media/data-flow/xpb1.png "式ビルダー")

式が必須である派生列やフィルターなどの変換では、青い式ボックスを選択して式ビルダーを開きます。

![青い式ボックス](media/data-flow/expressionbox.png "式ビルダー")

一致条件またはグループ化の条件で列を参照するときは、式で列から値を抽出できます。 式を作成するには、 **[計算列]** を選択します。

![[計算列] オプション](media/data-flow/computedcolumn.png "式ビルダー")

式またはリテラル値が有効な入力である場合は、 **[動的なコンテンツの追加]** を選択して、リテラル値に評価される式を構築します。

![[動的なコンテンツの追加] オプション](media/data-flow/add-dynamic-content.png "式ビルダー")

## <a name="expression-language-reference"></a>式言語のリファレンス

マッピング データ フローには、式で使用できる組み込みの関数と演算子があります。 使用できる関数の一覧については、[マッピング データ フローの式関数](data-flow-expression-functions.md)に関するページを参照してください。

## <a name="column-names-with-special-characters"></a>特殊文字を含む列名

特殊文字またはスペースを含む列名がある場合、それらを式で参照するには、その名前を中かっこで囲みます。

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>式の結果のプレビュー

[デバッグ モード](concepts-data-flow-debug-mode.md)がオンに切り替えられている場合は、ライブ Spark クラスターを使用して、式の評価結果の進行中のプレビューを確認できます。 ロジックを構築するときに、リアルタイムに式をデバッグできます。 

![進行中のプレビュー](media/data-flow/exp4b.png "式データのプレビュー")

**[更新]** を選択すると、式の結果がソースのライブ サンプルに対して更新されます。

![[更新] ボタン](media/data-flow/exp5.png "式データのプレビュー")

## <a name="string-interpolation"></a>文字列補間

リテラル文字列テキストを式と共に囲むには、引用符を使用します。 式関数、列、およびパラメーターを含めることができます。 文字列補間は、パラメーターをクエリ文字列に含めるときに文字列連結の広範囲にわたる使用を回避するために役立ちます。 式の構文を使用するには、それを中かっこで囲みます。

文字列補間のいくつかの例を次に示します。

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>コメント式

単一行および複数行コメントの構文を使用して、式にコメントを追加します。

有効なコメントの例を次に示します。

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

式の先頭にコメントを配置すると、変換式を文書化するための変換テキスト ボックスにコメントが表示されます。

![変換テキストボックス内のコメント](media/data-flow/comments2.png "説明")

## <a name="regular-expressions"></a>正規表現

多くの式言語関数は、正規表現構文を使用します。 正規表現関数を使用する場合、式ビルダーでは、円記号 (\\) をエスケープ文字シーケンスとして解釈することが試みられます。 正規表現で円記号を使用するときは、正規表現全体をバッククォート (\`) 内に入れるか、円記号を二重で使用します。

バッククォートを使用する例を次に示します。

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

ダブル スラッシュを使用する例を次に示します。

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>配列インデックスのアドレス指定

配列を返す式関数では、角かっこ ([]) を使用して、返される配列オブジェクト内の特定のインデックスをアドレス指定します。 配列は先頭が 1 です。

![式ビルダーの配列](media/data-flow/expb2.png "式データのプレビュー")

## <a name="keyboard-shortcuts"></a>キーボード ショートカット

* Ctrl + K、Ctrl + C:行全体をコメントにします。
* Ctrl + K、Ctrl + U:コメントを解除します。
* F1:エディターのヘルプ コマンドを提供します
* Alt + 下方向キー:現在の行を下に移動します。
* Alt + 上方向キー:現在の行を上に移動します。
* Ctrl + Space キー:コンテキスト ヘルプを表示します。

## <a name="convert-to-dates-or-timestamps"></a>日付またはタイムスタンプに変換する

タイムスタンプ出力に文字列リテラルを含めるには、```toString()``` で変換をラップします。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

ミリ秒をエポックから日付またはタイムスタンプに変換するには、`toTimestamp(<number of milliseconds>)` を使用します。 時刻が秒単位で指定されている場合は、1,000 を掛けます。

```toTimestamp(1574127407*1000l)```

前の式の最後にある末尾の "l" は、インライン構文としての long 型への変換を示しています。

## <a name="find-time-from-epoch-or-unix-time"></a>エポックまたは Unix 時刻から時刻を検索する

toLong( currentTimestamp() - toTimestamp('1970-01-01 00:00:00.000', 'yyyy-MM-dd HH:mm:ss.SSS') ) * 1000l

## <a name="next-steps"></a>次のステップ

[データ変換式の作成を開始する](data-flow-expression-functions.md)
