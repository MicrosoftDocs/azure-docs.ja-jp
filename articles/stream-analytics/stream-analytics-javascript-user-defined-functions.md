---
title: Azure Stream Analytics の JavaScript ユーザー定義関数
description: この記事では、Stream Analytics における JavaScript のユーザー定義関数について概説します。
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 12/15/2020
ms.openlocfilehash: 70015ef24039694789ce96a6c4853221fe2377c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020385"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics での JavaScript ユーザー定義関数
 
Azure Stream Analytics は、JavaScript で記述されたユーザー定義関数をサポートします。 JavaScript が提供する一連の豊富な **String**、**RegExp**、**Math**、**Array**、**Date** メソッドによって、Stream Analytics ジョブを伴う複雑なデータ変換の作成が容易になります。

## <a name="overview"></a>概要

JavaScript ユーザー定義関数は、外部との接続を必要としない、ステートレスの計算のみのスカラー関数をサポートします。 関数の戻り値には、スカラー (単一) 値のみを指定できます。 ジョブに JavaScript ユーザー定義関数を追加した後は、組み込みのスカラー関数と同様に、クエリ内の任意の場所で関数を使用できます。

ここでは、JavaScript ユーザー定義関数が役立つ可能性があるシナリオをいくつか示します。
* 正規表現関数を用いた文字列の解析と操作 (例: **Regexp_Replace()** and **Regexp_Extract()** )
* データのデコードとエンコード (例: バイナリから 16 進数への変換)
* JavaScript の **Math** 関数を用いた数値計算
* 並べ替え、結合、検索、値の設定などの配列操作の実行

Stream Analytics の JavaScript ユーザー定義関数では実行できないことを次に示します。
* 外部の REST エンドポイントの呼び出し (IP の逆引き参照の実行、外部ソースからの参照データのプルなど)
* 入力/出力におけるカスタム イベントフォーマットのシリアル化または逆シリアル化の実行
* カスタム集計の作成

関数定義において禁止されているわけではありませんが、**Date.GetDate()** や **Math.random()** などの関数の使用は避ける必要があります。 これらの関数では呼び出すたびに同じ結果が **返らず**、Azure Stream Analytics サービスは関数呼び出しや戻り値のジャーナルを保持しません。 関数が同じイベントで異なる結果を返す場合、ユーザーや Stream Analytics サービスによってジョブが再起動された際の再現性は保証されません。

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>JavaScript のユーザー定義関数をジョブに追加する

> [!NOTE]
> これらの手順は、クラウド内で実行するように構成された Stream Analytics ジョブに対して有効です。 お使いの Stream Analytics ジョブが Azure IoT Edge 上で実行されるように構成されている場合は、代わりに Visual Studio を使用し、[C# を使ってユーザー定義関数を記述](stream-analytics-edge-csharp-udf.md)します。

Stream Analytics ジョブに JavaScript のユーザー定義関数を作成するには、 **[ジョブ トポロジ]** の **[関数]** を選択します。 次に、 **[+ 追加]** ドロップダウン メニューから **[JavaScript UDF]** を選択します。 

![JavaScript UDF を追加する](./media/javascript/stream-analytics-jsudf-add.png)

さらに次のプロパティを入力して、 **[保存]** を選択する必要があります。

|プロパティ|説明|
|--------|-----------|
|関数のエイリアス|クエリ内で関数を呼び出すための名前を入力します。|
|出力の種類|JavaScript のユーザー定義関数によって Stream Analytics クエリに返される型。|
|関数の定義|クエリから UDF が呼び出されるたびに実行される JavaScript 関数の実装。|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript UDF のテストとトラブルシューティング 

JavaScript UDF ロジックは、任意のブラウザーでテストおよびデバッグできます。 これらのユーザー定義関数のロジックのデバッグとテストは、現在、Stream Analytics ポータルではサポートされていません。 関数が想定どおりに動作したら、前述のように Stream Analytics ジョブに追加してから、クエリから直接呼び出すことができます。 JavaScript UDF を使ったクエリのロジックは、[Visual Studio 用 Stream Analytics ツール](./stream-analytics-tools-for-visual-studio-install.md)を使用してテストすることができます。

JavaScript ランタイム エラーは致命的とみなされ、アクティビティ ログに表示されます。 Azure Portal からログを取得するには、ジョブに移動し、 **[アクティビティ ログ]** を選択します。

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>クエリでの JavaScript ユーザー定義関数の呼び出し

JavaScript 関数は、**udf** というプレフィックスで始まる関数のエイリアスを使用して、クエリから簡単に呼び出すことができます。 以下に示したのは、16 進値を整数に変換する JavaScript UDF を、Stream Analytics のクエリから呼び出す例です。

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>サポートされている JavaScript オブジェクト

Azure Stream Analytics の JavaScript ユーザー定義関数は、JavaScript の標準のビルトイン オブジェクトをサポートしています。 これらのオブジェクトの一覧については、[グローバル オブジェクト](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)に関する記事を参照してください。

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics と JavaScript の型変換

Stream Analytics クエリ言語と JavaScript でサポートされる型の間には違いがあります。 次の表は、2 つの型の変換マッピングの一覧です。

Stream Analytics | JavaScript
--- | ---
bigint | Number (JavaScript では最大 2^53 の精度の整数しか表現できません)
DateTime | Date (JavaScript ではミリ秒のみサポートされています)
double | Number
nvarchar(MAX) | String
Record | Object
Array | Array
NULL | [Null]

JavaScript から Stream Analytics への変換を以下に示します。

JavaScript | Stream Analytics
--- | ---
Number | Bigint (値が四捨五入され、long.MinValue と long.MaxValue の間の場合。それ以外の場合は double)
Date | DateTime
String | nvarchar(MAX)
Object | Record
Array | Array
Null, Undefined | NULL
他のすべての種類 (関数やエラーなど) | サポート対象外 (ランタイム エラーが発生します)

JavaScript 言語は大文字と小文字を区別し、JavaScript コード内のオブジェクト フィールドの大文字と小文字は、受信データのフィールドの大文字と小文字に一致する必要があります。 互換性レベル 1.0 のジョブでは SQL SELECT ステートメントのフィールドが小文字に変換されます。 互換性レベル 1.1 以降では、SELECT ステートメントのフィールドが SQL クエリでの指定と同じ大文字と小文字になります。

## <a name="other-javascript-user-defined-function-patterns"></a>その他の JavaScript ユーザー定義関数のパターン

### <a name="write-nested-json-to-output"></a>入れ子になった JSON を記述して出力する

Stream Analytics ジョブ出力を入力として使用し、その入力が JSON フォーマットを必要とするフォローアップ処理手順の場合は、JSON 文字列を記述して出力することができます。 次の例では **JSON.stringify()** 関数を呼び出し、入力のすべての名前/値のペアをまとめ、単一の文字列値として出力します。

**JavaScript ユーザー定義関数の定義:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**サンプル クエリ:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>文字列を処理できるように JSON オブジェクトにキャストする

JSON の文字列フィールドがあり、それを JSON オブジェクトに変換して JavaScript UDF で処理する場合は、**JSON.parse()** 関数を使って JSON オブジェクトを作成し、これを使用することができます。

**JavaScript ユーザー定義関数の定義:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**サンプル クエリ:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>エラー処理に try/catch を使用する

try/catch ブロックは、JavaScript UDF に渡される、形式が正しくない入力データに関する問題を特定するのに役立ちます。

**JavaScript ユーザー定義関数の定義:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**サンプル クエリ: レコード全体を最初のパラメーターとして渡して、エラーが発生した場合にそれを返すことができるようにします。**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
JavaScript の **toLocaleString** メソッドを使用すると、このメソッドが呼び出された日時データを表す、言語に依存する文字列を返すことができます。
Azure Stream Analtyics でシステム タイムスタンプとして受け入れられるのは UTC 日時のみですが、このメソッドを使用して、システム タイムスタンプを別のロケールとタイムゾーンに変換できます。
このメソッドは、Internet Explorer に用意されているものと同じ実装動作に従います。

**JavaScript ユーザー定義関数の定義:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**サンプル クエリ: datetime を入力値として渡す**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

このクエリの出力は、options が指定された **de-DE** の入力 datetime になります。
```
Samstag, 28. Dezember 2019
```

## <a name="user-logging"></a>ユーザーのログ記録
ログ記録メカニズムを使用すると、ジョブの実行中にカスタム情報をキャプチャできます。 ログ データを使用して、カスタム コードの正確性をリアルタイムでデバッグまたは評価することができます。 このメカニズムは、Console.Log() メソッドを通じて使用できます。

```javascript
console.log('my error message');
```

ログ メッセージにアクセスするには、[診断ログ](data-errors.md)を使用します。
## <a name="next-steps"></a>次のステップ

* [Machine Learning UDF](./machine-learning-udf.md)
* [C# UDF](./stream-analytics-edge-csharp-udf-methods.md)
