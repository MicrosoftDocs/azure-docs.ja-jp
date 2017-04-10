---
title: "Azure Stream Analytics の JavaScript ユーザー定義関数 | Microsoft Docs"
description: "ユーザー定義関数を使用して高度なクエリ機構を実行します。"
keywords: "JavaScript、ユーザー定義関数、UDF"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: a8334d146877ccc3988784c7a74a1c887dba68ab
ms.openlocfilehash: 57027b97cebf8accccd91f135a13825047fd211e
ms.lasthandoff: 02/09/2017

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure Stream Analytics の JavaScript ユーザー定義関数
Azure Stream Analytics は、JavaScript で記述されたユーザー定義関数をサポートします。 JavaScript が提供する一連の豊富な **String**、**RegExp**、**Math**、**Array**、**Date** メソッドによって、Stream Analytics ジョブを伴う複雑なデータ変換の作成が容易になります。

## <a name="javascript-user-defined-functions"></a>JavaScript ユーザー定義関数
JavaScript ユーザー定義関数は、外部との接続を必要としない、ステートレスの計算のみのスカラー関数をサポートします。 関数の戻り値には、スカラー (単一) 値のみを指定できます。 ジョブに JavaScript ユーザー定義関数を追加した後は、組み込みのスカラー関数と同様に、クエリ内の任意の場所で関数を使用できます。

ここでは、JavaScript ユーザー定義関数が役立つ可能性があるシナリオをいくつか示します。
* 正規表現関数を用いた文字列の解析と操作 (例: **Regexp_Replace()** and **Regexp_Extract()**)
* データのデコードとエンコード (例: バイナリから&16; 進数への変換)
* JavaScript の **Math** 関数を用いた数値計算の実行
* 並び替え、結合、検索、塗りつぶしなどの配列操作の実行

Stream Analytics の JavaScript ユーザー定義関数では実行できないことを次に示します。
* 外部の REST エンドポイントの呼び出し (IP の逆引き参照の実行、外部ソースからの参照データのプルなど)
* 入力/出力におけるカスタム イベントフォーマットのシリアル化または逆シリアル化の実行
* カスタム集計の作成

関数定義において禁止されているわけではありませんが、**Date.GetDate()** や **Math.random()** などの関数の使用は避ける必要があります。 これらの関数では呼び出すたびに同じ結果が**返らず**、Azure Stream Analytics サービスは関数呼び出しや戻り値のジャーナルを保持しません。 関数が同じイベントで異なる結果を返す場合、ユーザーや Stream Analytics サービスによってジョブが再起動された際の再現性は保証されません。

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Azure Portal での JavaScript ユーザー定義関数の追加
既存の Stream Analytics ジョブの下で単一の JavaScript ユーザー定義関数を作成するには、次の手順を実行します。

1.    Azure Portal で Stream Analytics ジョブを見つけます。
2.  **[ジョブ トポロジ]** で、関数を選択します。 空の関数一覧が表示されます。
3.    新しいユーザー関数を作成するには、**[追加]** を選択します。
4.    **[新しい関数]** ブレードの **[関数の種類]** で、**[JavaScript]** を選択します。 既定の関数テンプレートがエディターに表示されます。
5.    **[UDF alias (UDF エイリアス)]** に「**hex2Int**」と入力し、関数の実装を次のように変更します。

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.    [ **保存**] を選択します。 作成した関数が関数一覧に表示されます。
7.    新しい **hex2Int** 関数を選択し、関数の定義を確認します。 すべての関数で、関数のエイリアスにプレフィックス **UDF** が追加されています。 Stream Analytics クエリでは、関数を呼び出す際に*プレフィックスを含める*必要があります。 この場合は、**UDF.hex2Int** を呼び出します。

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>クエリでの JavaScript ユーザー定義関数の呼び出し

1. クエリ エディターの **[ジョブ トポロジ]** で、**[クエリ]** を選択します。
2.    クエリを編集し、次のようにユーザー定義関数を呼び出します。

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.    サンプル データ ファイルをアップロードするには、ジョブ入力を右クリックします。
4.    クエリをテストするには、**[テスト]** を選択します。


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
レコード | オブジェクト
array | array
NULL | Null


JavaScript から Stream Analytics への変換を以下に示します。


JavaScript | Stream Analytics
--- | ---
Number | Bigint (値が四捨五入され、long.MinValue と long.MaxValue の間の場合。それ以外の場合は double)
日付 | DateTime
String | nvarchar(MAX)
オブジェクト | レコード
array | array
Null、未定義 | NULL
他のすべての種類 (関数やエラーなど) | サポート対象外 (ランタイム エラーが発生します)

## <a name="troubleshooting"></a>トラブルシューティング
JavaScript ランタイム エラーは致命的とみなされ、アクティビティ ログに表示されます。 Azure Portal からログを取得するには、ジョブに移動し、**[アクティビティ ログ]** を選択します。


## <a name="other-javascript-user-defined-function-patterns"></a>その他の JavaScript ユーザー定義関数のパターン

### <a name="write-nested-json-to-output"></a>入れ子になった JSON を記述して出力する
Stream Analytics ジョブ出力を入力として使用し、その入力が JSON フォーマットを必要とするフォローアップ処理手順の場合は、JSON 文字列を記述して出力することができます。 次の例では **JSON.stringify()** 関数を呼び出し、入力のすべての名前/値のペアをまとめ、単一の文字列値として出力します。

**JavaScript ユーザー定義関数の定義:**

```
function main(x) {
return JSON.stringify(x);
}
```

**サンプル クエリ:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)をご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics の管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)

