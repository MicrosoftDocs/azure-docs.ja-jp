---
title: マッピング データ フローでの式ビルダー
description: Azure Data Factory のマッピング データ フローで式ビルダーを使用して式を構築する
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584925"
---
# <a name="build-expressions-in-mapping-data-flow"></a>マッピング データ フローで式を構築する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

マッピング データ フローでは、多くの変換プロパティが式として入力されます。 これらの式は、実行時に Spark データ型に評価される列の値、パラメーター、関数、演算子、リテラルで構成されます。 マッピング データ フローには、これらの式の構築を支援する、**式ビルダー** と呼ばれる専用のエクスペリエンスがあります。 [IntelliSense](/visualstudio/ide/using-intellisense) のコード補完を利用して、強調表示、構文チェック、オートコンプリートを行うことで、式ビルダーはデータ フローを簡単に構築できるように設計されています。 この記事では、式ビルダーを使用してビジネス ロジックを効率よく構築する方法について説明します。

![式ビルダー](media/data-flow/expresion-builder.png "式ビルダー")

## <a name="open-expression-builder"></a>式ビルダーを開く

式ビルダーを開くための複数のエントリ ポイントがあります。 これらはすべて、データ フロー変換の特定のコンテキストに依存します。 最も一般的なユース ケースは、ユーザーがデータ フロー式言語を使用して列を作成または更新する[派生列](data-flow-derived-column.md)や[集計](data-flow-aggregate.md)のような変換の場合です。 式ビルダーを開くには、列の一覧の上にある **[Open expression builder]\(式ビルダーを開く\)** を選択します。 列のコンテキストをクリックして、その式に対する式ビルダーを直接開くこともできます。

![式ビルダーを開く](media/data-flow/open-expression-builder-derive.png "式ビルダーを開く")

[フィルター](data-flow-filter.md)のような一部の変換では、青い式テキスト ボックスをクリックすると、式ビルダーが開きます。 

![青い式ボックス](media/data-flow/expressionbox.png "青い式ボックス")

一致条件またはグループ化の条件で列を参照するときは、式で列から値を抽出できます。 式を作成するには、 **[計算列]** を選択します。

![[計算列] オプション](media/data-flow/computedcolumn.png "[計算列] オプション")

式またはリテラル値が有効な入力である場合は、 **[動的なコンテンツの追加]** を選択して、リテラル値に評価される式を構築します。

![[動的なコンテンツの追加] オプション](media/data-flow/add-dynamic-content.png "[動的なコンテンツの追加] オプション")

## <a name="expression-elements"></a>式の要素

マッピング データ フローでは、列の値、パラメーター、関数、ローカル変数、演算子、リテラルで式を構成できます。 これらの式は、文字列、ブール値、整数などの Spark データ型に評価される必要があります。

![式の要素](media/data-flow/expression-elements.png "式の要素")

### <a name="functions"></a>関数

マッピング データ フローには、式で使用できる組み込みの関数と演算子があります。 使用できる関数の一覧については、[マッピング データ フローの言語リファレンス](data-flow-expression-functions.md)に関するページを参照してください。

#### <a name="address-array-indexes"></a>配列インデックスのアドレス指定

配列型を返す列または関数を扱う場合は、角かっこ ([]) を使用して特定の要素にアクセスします。 インデックスが存在しない場合、式は NULL と評価されます。

![式ビルダーの配列](media/data-flow/expression-array.png "式データのプレビュー")

> [!IMPORTANT]
> マッピング データ フローでは、配列は 1 から始まります。つまり、最初の要素はインデックス 1 で参照されます。 たとえば、myArray[1] では、"myArray" という名前の配列の最初の要素にアクセスします。

### <a name="input-schema"></a>入力スキーマ

データ フローのソースのいずれかで定義済みのスキーマが使用されている場合、多くの式で名前により列を参照できます。 スキーマの誤差を利用している場合は、`byName()` または `byNames()` 関数を使用して明示的に列を参照したり、列のパターンを使用して一致させたりできます。

#### <a name="column-names-with-special-characters"></a>特殊文字を含む列名

特殊文字またはスペースを含む列名がある場合、それらを式で参照するには、その名前を中かっこで囲みます。

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>パラメーター

パラメーターは、実行時にパイプラインからデータ フローに渡される値です。 パラメーターを参照するには、 **[Expression elements]\(式の要素\)** ビューでパラメーターをクリックするか、名前の前にドル記号を付けて参照します。 たとえば、parameter1 という名前のパラメーターは `$parameter1` によって参照されます。 詳細については、「[マッピング データ フローをパラメーター化する](parameters-data-flow.md)」を参照してください。

### <a name="cached-lookup"></a>キャッシュされた検索

キャッシュされた検索を使用すると、キャッシュされたシンクの出力をインラインで参照できます。 `lookup()` と `outputs()` の各シンクで使用できる関数は 2 つあります。 これらの関数を参照する構文は `cacheSinkName#functionName()` です。 詳細については、[シンクのキャッシュ](data-flow-sink.md#cache-sink)に関する記事を参照してください。

`lookup()` を使用すると、現在の変換で一致する列がパラメーターとして取り込まれ、キャッシュ シンクでキー列に一致する行に等しい複合列が返されます。 返される複合列には、キャッシュ シンクにマップされている各列のサブ列が含まれます。 たとえば、コードと `Message` という列に一致するキー列を持つエラー コード キャッシュ シンク `errorCodeCache` があるとします。 `errorCodeCache#lookup(errorCode).Message` を呼び出すと、渡されたコードに対応するメッセージが返されます。 

`outputs()` を使用すると、パラメーターは取得されず、複合列の配列としてキャッシュ シンク全体が返されます。 これは、キー列がシンク内で指定されている場合は呼び出すことができず、キャッシュ シンクに行が少数ある場合にのみ使用する必要があります。 一般的なユース ケースでは、増分キーの最大値が追加されます。 キャッシュされた単一の集計行 `CacheMaxKey` に列 `MaxKey` が含まれている場合に最初の値を参照するには、`CacheMaxKey#outputs()[1].MaxKey` を呼び出します。

![キャッシュされた検索](media/data-flow/cached-lookup-example.png "キャッシュされた検索")

### <a name="locals"></a>ローカル

複数の列でロジックを共有する場合、またはロジックをコンパートメント化する場合は、派生列の内部にローカルを作成できます。 ローカルを参照するには、 **[Expression elements]\(式の要素\)** ビューでローカルをクリックするか、名前の前にコロンを付けて参照します。 たとえば、local1 という名前のローカルは、`:local1` によって参照されます。 ローカルの詳細については、[派生列のドキュメント](data-flow-derived-column.md#locals)を参照してください。

## <a name="preview-expression-results"></a>式の結果のプレビュー

[デバッグ モード](concepts-data-flow-debug-mode.md)がオンになっている場合は、デバッグ クラスターを対話的に使用して、式の評価をプレビューできます。 データ プレビューの横にある **[最新の情報に更新]** を選択すると、データ プレビューの結果が更新されます。 入力列を指定して、各行の出力を確認できます。

![進行中のプレビュー](media/data-flow/preview-expression.png "式データのプレビュー")

## <a name="string-interpolation"></a>文字列補間

式要素を使用する長い文字列を作成する場合は、文字列補間を使用して、複雑な文字列ロジックを簡単に構築できます。 文字列補間を使用すると、パラメーターをクエリ文字列に含めるときの文字列連結の多用を回避できます。 リテラル文字列テキストを式と共に囲むには、二重引用符を使用します。 式関数、列、およびパラメーターを含めることができます。 式の構文を使用するには、それを中かっこで囲みます。

文字列補間のいくつかの例を次に示します。

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> SQL ソース クエリで文字列補間構文を使用する場合、クエリ文字列は、'/n' を含まない 1 行にする必要があります。

## <a name="commenting-expressions"></a>式のコメント化

単一行および複数行コメントの構文を使用して、式にコメントを追加します。

有効なコメントの例を次に示します。

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

式の先頭にコメントを配置すると、変換式を文書化するための変換テキスト ボックスにコメントが表示されます。

![変換テキストボックス内のコメント](media/data-flow/comment-expression.png "説明")

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

## <a name="keyboard-shortcuts"></a>キーボード ショートカット

式ビルダーで使用できるショートカットの一覧を次に示します。 IntelliSense のほとんどのショートカットを、式の作成時に使用できます。

* Ctrl + K、Ctrl + C:行全体をコメントにします。
* Ctrl + K、Ctrl + U:コメントを解除します。
* F1:エディターのヘルプ コマンドを提供します
* Alt + 下方向キー:現在の行を下に移動します。
* Alt + 上方向キー:現在の行を上に移動します。
* Ctrl + Space キー:コンテキスト ヘルプを表示します。

## <a name="commonly-used-expressions"></a>よく使用される式

### <a name="convert-to-dates-or-timestamps"></a>日付またはタイムスタンプに変換する

タイムスタンプ出力に文字列リテラルを含めるには、```toString()``` で変換をラップします。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

ミリ秒をエポックから日付またはタイムスタンプに変換するには、`toTimestamp(<number of milliseconds>)` を使用します。 時刻が秒単位で指定されている場合は、1,000 を掛けます。

```toTimestamp(1574127407*1000l)```

前の式の最後にある末尾の "l" は、インライン構文としての long 型への変換を示しています。

### <a name="find-time-from-epoch-or-unix-time"></a>エポックまたは Unix 時刻から時刻を検索する

toLong( currentTimestamp() - toTimestamp('1970-01-01 00:00:00.000', 'yyyy-MM-dd HH:mm:ss.SSS') ) * 1000l

## <a name="next-steps"></a>次のステップ

[データ変換式の作成を開始する](data-flow-expression-functions.md)
