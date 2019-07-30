---
title: 'チュートリアル: Azure Stream Analytics の JavaScript ユーザー定義関数 | Microsoft Docs '
description: このチュートリアルでは、JavaScript ユーザー定義関数を使用して高度なクエリ機構を実行します
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: c7414ee159303465d6698ce9c47d04ba37c0c46e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329376"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>チュートリアル:Azure Stream Analytics の JavaScript ユーザー定義関数
 
Azure Stream Analytics は、JavaScript で記述されたユーザー定義関数をサポートします。 JavaScript が提供する一連の豊富な **String**、**RegExp**、**Math**、**Array**、**Date** メソッドによって、Stream Analytics ジョブを伴う複雑なデータ変換の作成が容易になります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * JavaScript ユーザー定義関数を定義する
> * ポータルに関数を追加する
> * 関数を実行するクエリを定義する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="javascript-user-defined-functions"></a>JavaScript ユーザー定義関数
JavaScript ユーザー定義関数は、外部との接続を必要としない、ステートレスの計算のみのスカラー関数をサポートします。 関数の戻り値には、スカラー (単一) 値のみを指定できます。 ジョブに JavaScript ユーザー定義関数を追加した後は、組み込みのスカラー関数と同様に、クエリ内の任意の場所で関数を使用できます。

ここでは、JavaScript ユーザー定義関数が役立つ可能性があるシナリオをいくつか示します。
* 正規表現関数を用いた文字列の解析と操作 (例: **Regexp_Replace()** and **Regexp_Extract()** )
* データのデコードとエンコード (例: バイナリから 16 進数への変換)
* JavaScript の **Math** 関数を用いた数値計算の実行
* 並び替え、結合、検索、塗りつぶしなどの配列操作の実行

Stream Analytics の JavaScript ユーザー定義関数では実行できないことを次に示します。
* 外部の REST エンドポイントの呼び出し (IP の逆引き参照の実行、外部ソースからの参照データのプルなど)
* 入力/出力におけるカスタム イベントフォーマットのシリアル化または逆シリアル化の実行
* カスタム集計の作成

関数定義において禁止されているわけではありませんが、**Date.GetDate()** や **Math.random()** などの関数の使用は避ける必要があります。 これらの関数では呼び出すたびに同じ結果が**返らず**、Azure Stream Analytics サービスは関数呼び出しや戻り値のジャーナルを保持しません。 関数が同じイベントで異なる結果を返す場合、ユーザーや Stream Analytics サービスによってジョブが再起動された際の再現性は保証されません。

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Azure Portal での JavaScript ユーザー定義関数の追加
既存の Stream Analytics ジョブの下で単一の JavaScript ユーザー定義関数を作成するには、次の手順に従います。

> [!NOTE]
> これらの手順は、クラウド内で実行するように構成された Stream Analytics ジョブに対して有効です。 お使いの Stream Analytics ジョブが Azure IoT Edge 上で実行されるように構成されている場合は、代わりに Visual Studio を使用し、[C# を使ってユーザー定義関数を記述](stream-analytics-edge-csharp-udf.md)します。

1.  Azure Portal で Stream Analytics ジョブを見つけます。

2. **[ジョブ トポロジ]** 見出しの下にある **[関数]** を選択します。 空の関数一覧が表示されます。

3.  新しいユーザー定義関数を作成するには、 **[追加]** を選択します。

4.  **[新しい関数]** ブレードの **[関数の種類]** で、 **[JavaScript]** を選択します。 既定の関数テンプレートがエディターに表示されます。

5.  **[UDF alias (UDF エイリアス)]** に「**hex2Int**」と入力し、関数の実装を次のように変更します。

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  **[保存]** を選択します。 作成した関数が関数一覧に表示されます。
7.  新しい **hex2Int** 関数を選択し、関数の定義を確認します。 すべての関数で、関数のエイリアスにプレフィックス **UDF** が追加されています。 Stream Analytics クエリでは、関数を呼び出す際に*プレフィックスを含める*必要があります。 この場合は、**UDF.hex2Int** を呼び出します。

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>クエリでの JavaScript ユーザー定義関数の呼び出し

1. クエリ エディターで、 **[ジョブ トポロジ]** 見出しの下にある **[クエリ]** を選択します。
2.  クエリを編集し、次のようにユーザー定義関数を呼び出します。

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  サンプル データ ファイルをアップロードするには、ジョブ入力を右クリックします。
4.  クエリをテストするには、 **[テスト]** を選択します。


## <a name="supported-javascript-objects"></a>サポートされている JavaScript オブジェクト
Azure Stream Analytics の JavaScript ユーザー定義関数は、JavaScript の標準のビルトイン オブジェクトをサポートしています。 これらのオブジェクトの一覧については、[グローバル オブジェクト](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)に関する記事を参照してください。

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics と JavaScript の型変換

Stream Analytics クエリ言語と JavaScript でサポートされる型の間には違いがあります。 次の表は、2 つの型の変換マッピングの一覧です。

Stream Analytics | JavaScript
--- | ---
bigint | Number (JavaScript では最大 2^53 の精度の整数しか表現できません)
DateTime | Date (JavaScript ではミリ秒のみサポートされています)
double | Number
nvarchar(MAX) | string
Record | Object
Array | Array
NULL | Null


JavaScript から Stream Analytics への変換を以下に示します。


JavaScript | Stream Analytics
--- | ---
Number | Bigint (値が四捨五入され、long.MinValue と long.MaxValue の間の場合。それ以外の場合は double)
Date | DateTime
string | nvarchar(MAX)
Object | Record
Array | Array
Null, Undefined | NULL
他のすべての種類 (関数やエラーなど) | サポート対象外 (ランタイム エラーが発生します)

JavaScript 言語は大文字と小文字を区別し、JavaScript コード内のオブジェクト フィールドの大文字と小文字は、受信データのフィールドの大文字と小文字に一致する必要があります。 互換性レベル 1.0 のジョブは SQL SELECT ステートメントのフィールドを小文字に変換することに注意してください。 互換性レベル 1.1 以降では、SELECT ステートメントのフィールドが SQL クエリでの指定と同じ大文字と小文字になります。

## <a name="troubleshooting"></a>トラブルシューティング
JavaScript ランタイム エラーは致命的とみなされ、アクティビティ ログに表示されます。 Azure Portal からログを取得するには、ジョブに移動し、 **[アクティビティ ログ]** を選択します。


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
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合は、ジョブを停止し、必要なときに再起動することができます。 このジョブの使用を続けない場合は、以下の手順に従って、このクイック スタートで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、 **[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。  
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)をご覧ください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、単純な JavaScript ユーザー定義関数を実行する Stream Analytics ジョブを作成しました。 Stream Analytics についてさらに学習するには、以下のリアルタイム シナリオの記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Stream Analytics でのリアルタイム Twitter 感情分析](stream-analytics-twitter-sentiment-analysis-trends.md)
