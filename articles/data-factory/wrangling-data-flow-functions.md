---
title: Azure Data Factory でのラングリング データ フロー変換関数
description: Azure Data Factory で使用可能なラングリング データ フロー関数の概要
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e63c3f329cb9c1fd5ca91274540f5145c3ad098a
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921542"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>ラングリング データ フローの変換関数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory のラングリング データ フローを使用すると、コードなしのアジャイルなデータ準備とラングリングをクラウド規模で実行できます。 ラングリング データ フローは [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) と統合されており、Spark 実行を通して Power Query M 関数をデータ ラングリングに対して使用可能にします。 

> [!NOTE]
> ラングリング データ フローは、現在パブリック プレビューで提供されています

現時点では、Power Query M 関数は、作成中に利用可能であっても、そのすべてがデータ ラングリングでサポートされているわけではありません。 ラングリング データ フローを構築しているときに、関数がサポートされていない場合は、次のエラー メッセージが表示されます。

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

サポートされている Power Query M 関数の一覧を次に示します。

## <a name="column-management"></a>列の管理

* 選択:[Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* 削除:[Table.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* 名前変更:[Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns)、[Table.PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns)、[Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* 並べ替え:[Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>行のフィルタリング

次の条件でフィルター処理するには、M 関数 [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) を使用します。

* 等値および非等値
* 数値、テキスト、および日付の比較 (ただし DateTime は除く)
* [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven) などの数値情報
* [Text.Contains](https://docs.microsoft.com/powerquery-m/text-contains)、[Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)、または [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith) を使用したテキストの包含
* すべての 'IsIn' [日付関数](https://docs.microsoft.com/powerquery-m/date-functions)を含む日付範囲 
* and、or、または not 条件を使用したこれらの組み合わせ

## <a name="adding-and-transforming-columns"></a>列の追加と変換

次の M 関数は、列を追加または変換します。[Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn)、[Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns)、[Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue)、[Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn)。 サポートされている変換関数を以下に示します。

* 数値演算
* テキスト連結
* 日付と時刻の演算 (算術演算子、[Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays)、[Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths)、[Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters)、[Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks)、[Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Duration は日付と時刻の演算に使用できますが、シンクに書き込まれる前に別の型に変換する必要があります (算術演算子、[#duration](https://docs.microsoft.com/powerquery-m/sharpduration)、[Duration.Days](https://docs.microsoft.com/powerquery-m/duration-days)、[Duration.Hours](https://docs.microsoft.com/powerquery-m/duration-hours)、[Duration.Minutes](https://docs.microsoft.com/powerquery-m/duration-minutes)、[Duration.Seconds](https://docs.microsoft.com/powerquery-m/duration-seconds)、[Duration.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays)、[Duration.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours)、[Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes)、[Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* ほとんどの標準、科学的、および三角関数の数値関数 (Number.Factorial、Number.Permutations、および Number.Combinations *を除く*[演算](https://docs.microsoft.com/powerquery-m/number-functions#operations)、[丸め](https://docs.microsoft.com/powerquery-m/number-functions#rounding)、および[三角関数](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry)の下のすべての関数)
* 置換 ([Replacer.ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext)、[Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue)、[Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace)、[Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* 位置指定テキスト抽出 ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof)、[Text.Length](https://docs.microsoft.com/powerquery-m/text-length)、[Text.Start](https://docs.microsoft.com/powerquery-m/text-start)、[Text.End](https://docs.microsoft.com/powerquery-m/text-end)、[Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle)、[Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange)、[Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* 基本的なテキストの書式設定 ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower)、[Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper)、[Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend)、[Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend)、[Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* 日付/時刻関数 ([Date.Day](https://docs.microsoft.com/powerquery-m/date-day)、[Date.Month](https://docs.microsoft.com/powerquery-m/date-month)、[Date.Year](https://docs.microsoft.com/powerquery-m/date-year)[Time.Hour](https://docs.microsoft.com/powerquery-m/time-hour)、[Time.Minute](https://docs.microsoft.com/powerquery-m/time-minute)、[Time.Second](https://docs.microsoft.com/powerquery-m/time-second)、[Date.DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek)、[Date.DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear)、[Date.DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* If 式 (ただし分岐は一致する型を持つ必要があります)
* 論理列としての行フィルター
* 数値、テキスト、論理、日付、および日時の定数

<a name="mergingjoining-tables"></a>テーブルのマージ/結合
----------------------
* Power Query は入れ子になった結合を生成します (Table.NestedJoin。ユーザーは [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn) を手動で書き込むこともできます)。
    ユーザーはその後、入れ子になった結合列を入れ子になっていない結合に展開する必要があります (Table.ExpandTableColumn、他のコンテキストではサポートされていない)。
* M 関数 [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) を直接記述することで、追加の拡張手順は不要になりますが、結合されたテーブル間に重複する列名がないことをユーザーが確認する必要があります
* サポートされている結合の種類: [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner)、   [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter)、   [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter)、   [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [Value.Equals](https://docs.microsoft.com/powerquery-m/value-equals) および [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) は、両方とも重要な等値比較関数としてサポートされます

## <a name="group-by"></a>グループ化

[Table.Group](https://docs.microsoft.com/powerquery-m/table-group) を使用して値を集計します。
* 集計関数と共に使用する必要があります
* サポートされている集計関数: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount)、   [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum)、   [List.Count](https://docs.microsoft.com/powerquery-m/list-count)、   [List.Average](https://docs.microsoft.com/powerquery-m/list-average)、   [List.Min](https://docs.microsoft.com/powerquery-m/list-min)、   [List.Max](https://docs.microsoft.com/powerquery-m/list-max)、   [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation)、   [List.First](https://docs.microsoft.com/powerquery-m/list-first)、   [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>並べ替え

[Table.Sort](https://docs.microsoft.com/powerquery-m/table-sort) を使用して値を並べ替えます。

## <a name="reducing-rows"></a>行の削減

上位の保持と削除、範囲の保持 (対応する M 関数、条件ではなくカウントのみサポート:[Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn)、[Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip)、[Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn)、[Table.Range](https://docs.microsoft.com/powerquery-m/table-range)、[Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn)、[Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>既知のサポートされていない関数

| 機能 | Status |
| -- | -- |
| Table.PromoteHeaders | サポートされていません。 データセット内で "1 行目をヘッダーとして" 設定することにより、同じ結果を得ることができます。 |
| Table.CombineColumns | これは、直接サポートされてはいなくても、特定の 2 つの列を連結する新しい列を追加することによって実現できる一般的なシナリオです。  例: Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | ほとんどの場合、これはサポートされています。 次のシナリオはサポートされていません: 文字列から通貨型への変換、文字列から時刻型への変換、文字列からパーセント型への変換。 |
| Table.NestedJoin | 結合を行うだけでは、検証エラーが発生します。 機能させるには、列を展開する必要があります。 |
| Table.Distinct | 重複する行の削除はサポートされていません。 |
| Table.RemoveLastN | 下端の行の削除はサポートされていません。 |
| Table.RowCount | サポートされていませんが、すべてのセルが空である列を追加し (条件列を使用できます)、その後、その列で group by を使用することによって実現できます。 Table.Group はサポートされています。 | 
| 行レベルのエラー処理 | 行レベルのエラー処理は現在サポートされていません。 たとえば、列から数値以外の値を除外する方法の 1 つは、テキスト列を数値に変換することです。 変換が失敗したすべてのセルはエラー状態になり、フィルター処理する必要があります。 このシナリオは、ラングリング データ フローでは使用できません。 |
| Table.Transpose | サポートされていません |
| Table.Pivot | サポートされていません |

## <a name="next-steps"></a>次のステップ

[ラングリング データ フローの作成](wrangling-data-flow-tutorial.md)方法について確認します。