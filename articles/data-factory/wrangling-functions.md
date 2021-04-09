---
title: Azure Data Factory でのデータ ラングリング関数
description: Azure Data Factory で使用可能なデータ ラングリング関数の概要
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 659f6527d43e1b45a11fddf774050ca6d42bfe12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896665"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>データ ラングリングのための Power Query の変換関数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory のデータ ラングリングを使用すると、Power Query ```M``` スクリプトを Data Flow スクリプトに変換することにより、コード不要のアジャイルなデータ準備とラングリングをクラウド規模で実行できます。 ADF は [Power Query Online](/powerquery-m/power-query-m-reference) と統合されており、データ フローの Spark インフラストラクチャを使用した Spark の実行を介して、Power Query ```M``` 関数をデータ ラングリングで使用できるようにします。 

> [!NOTE]
> ADF の Power Query は現在、パブリック プレビューで提供されています。

現時点では、Power Query M 関数は、作成中に利用可能であっても、そのすべてがデータ ラングリングでサポートされているわけではありません。 マッシュアップを作成しているとき、関数がサポートされていない場合は、次のエラー メッセージが表示されます。

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

サポートされている Power Query M 関数の一覧を次に示します。

## <a name="column-management"></a>列の管理

* 選択:[Table.SelectColumns](/powerquery-m/table-selectcolumns)
* 削除:[Table.RemoveColumns](/powerquery-m/table-removecolumns)
* 名前変更:[Table.RenameColumns](/powerquery-m/table-renamecolumns)、[Table.PrefixColumns](/powerquery-m/table-prefixcolumns)、[Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* 並べ替え:[Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>行のフィルタリング

次の条件でフィルター処理するには、M 関数 [Table.SelectRows](/powerquery-m/table-selectrows) を使用します。

* 等値および非等値
* 数値、テキスト、および日付の比較 (ただし DateTime は除く)
* [Number.IsEven](/powerquery-m/number-iseven)/[Odd](/powerquery-m/number-iseven) などの数値情報
* [Text.Contains](/powerquery-m/text-contains)、[Text.StartsWith](/powerquery-m/text-startswith)、または [Text.EndsWith](/powerquery-m/text-endswith) を使用したテキストの包含
* すべての 'IsIn' [日付関数](/powerquery-m/date-functions)を含む日付範囲 
* and、or、または not 条件を使用したこれらの組み合わせ

## <a name="adding-and-transforming-columns"></a>列の追加と変換

次の M 関数は、列を追加または変換します。[Table.AddColumn](/powerquery-m/table-addcolumn)、[Table.TransformColumns](/powerquery-m/table-transformcolumns)、[Table.ReplaceValue](/powerquery-m/table-replacevalue)、[Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn)。 サポートされている変換関数を以下に示します。

* 数値演算
* テキスト連結
* 日付と時刻の演算 (算術演算子、[Date.AddDays](/powerquery-m/date-adddays)、[Date.AddMonths](/powerquery-m/date-addmonths)、[Date.AddQuarters](/powerquery-m/date-addquarters)、[Date.AddWeeks](/powerquery-m/date-addweeks)、[Date.AddYears](/powerquery-m/date-addyears))
* Duration は日付と時刻の演算に使用できますが、シンクに書き込まれる前に別の型に変換する必要があります (算術演算子、[#duration](/powerquery-m/sharpduration)、[Duration.Days](/powerquery-m/duration-days)、[Duration.Hours](/powerquery-m/duration-hours)、[Duration.Minutes](/powerquery-m/duration-minutes)、[Duration.Seconds](/powerquery-m/duration-seconds)、[Duration.TotalDays](/powerquery-m/duration-totaldays)、[Duration.TotalHours](/powerquery-m/duration-totalhours)、[Duration.TotalMinutes](/powerquery-m/duration-totalminutes)、[Duration.TotalSeconds](/powerquery-m/duration-totalseconds))    
* ほとんどの標準、科学的、および三角関数の数値関数 (Number.Factorial、Number.Permutations、および Number.Combinations *を除く*[演算](/powerquery-m/number-functions#operations)、[丸め](/powerquery-m/number-functions#rounding)、および [三角関数](/powerquery-m/number-functions#trigonometry)の下のすべての関数)
* 置換 ([Replacer.ReplaceText](/powerquery-m/replacer-replacetext)、[Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue)、[Text.Replace](/powerquery-m/text-replace)、[Text.Remove](/powerquery-m/text-remove))
* 位置指定テキスト抽出 ([Text.PositionOf](/powerquery-m/text-positionof)、[Text.Length](/powerquery-m/text-length)、[Text.Start](/powerquery-m/text-start)、[Text.End](/powerquery-m/text-end)、[Text.Middle](/powerquery-m/text-middle)、[Text.ReplaceRange](/powerquery-m/text-replacerange)、[Text.RemoveRange](/powerquery-m/text-removerange))
* 基本的なテキストの書式設定 ([Text.Lower](/powerquery-m/text-lower)、[Text.Upper](/powerquery-m/text-upper)、[Text.Trim](/powerquery-m/text-trim)/[Start](/powerquery-m/text-trimstart)/[End](/powerquery-m/text-trimend)、[Text.PadStart](/powerquery-m/text-padstart)/[End](/powerquery-m/text-padend)、[Text.Reverse](/powerquery-m/text-reverse))
* 日付/時刻関数 ([Date.Day](/powerquery-m/date-day)、[Date.Month](/powerquery-m/date-month)、[Date.Year](/powerquery-m/date-year)[Time.Hour](/powerquery-m/time-hour)、[Time.Minute](/powerquery-m/time-minute)、[Time.Second](/powerquery-m/time-second)、[Date.DayOfWeek](/powerquery-m/date-dayofweek)、[Date.DayOfYear](/powerquery-m/date-dayofyear)、[Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* If 式 (ただし分岐は一致する型を持つ必要があります)
* 論理列としての行フィルター
* 数値、テキスト、論理、日付、および日時の定数

<a name="mergingjoining-tables"></a>テーブルのマージ/結合
----------------------
* Power Query は入れ子になった結合を生成します (Table.NestedJoin。ユーザーは [Table.AddJoinColumn](/powerquery-m/table-addjoincolumn) を手動で書き込むこともできます)。
    ユーザーはその後、入れ子になった結合列を入れ子になっていない結合に展開する必要があります (Table.ExpandTableColumn、他のコンテキストではサポートされていない)。
* M 関数 [Table.Join](/powerquery-m/table-join) を直接記述することで、追加の拡張手順は不要になりますが、結合されたテーブル間に重複する列名がないことをユーザーが確認する必要があります
* サポートされている結合の種類: [Inner](/powerquery-m/joinkind-inner)、   [LeftOuter](/powerquery-m/joinkind-leftouter)、   [RightOuter](/powerquery-m/joinkind-rightouter)、   [FullOuter](/powerquery-m/joinkind-fullouter)
* [Value.Equals](/powerquery-m/value-equals) および [Value.NullableEquals](/powerquery-m/value-nullableequals) は、両方とも重要な等値比較関数としてサポートされます

## <a name="group-by"></a>グループ化

[Table.Group](/powerquery-m/table-group) を使用して値を集計します。
* 集計関数と共に使用する必要があります
* サポートされている集計関数: [List.Sum](/powerquery-m/list-sum)、[List.Count](/powerquery-m/list-count)、[List.Average](/powerquery-m/list-average)、[List.Min](/powerquery-m/list-min)、[List.Max](/powerquery-m/list-max)、[List.StandardDeviation](/powerquery-m/list-standarddeviation)、[List.First](/powerquery-m/list-first)、[List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>並べ替え

[Table.Sort](/powerquery-m/table-sort) を使用して値を並べ替えます。

## <a name="reducing-rows"></a>行の削減

上位の保持と削除、範囲の保持 (対応する M 関数、条件ではなくカウントのみサポート:[Table.FirstN](/powerquery-m/table-firstn)、[Table.Skip](/powerquery-m/table-skip)、[Table.RemoveFirstN](/powerquery-m/table-removefirstn)、[Table.Range](/powerquery-m/table-range)、[Table.MinN](/powerquery-m/table-minn)、[Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>既知のサポートされていない関数

| 機能 | Status |
| -- | -- |
| Table.PromoteHeaders | サポートされていません。 データセット内で "1 行目をヘッダーとして" 設定することにより、同じ結果を得ることができます。 |
| Table.CombineColumns | これは、直接サポートされてはいなくても、特定の 2 つの列を連結する新しい列を追加することによって実現できる一般的なシナリオです。  例: Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | ほとんどの場合、これはサポートされています。 次のシナリオはサポートされていません: 文字列から通貨型への変換、文字列から時刻型への変換、文字列からパーセント型への変換。 |
| Table.NestedJoin | 結合を行うだけでは、検証エラーが発生します。 機能させるには、列を展開する必要があります。 |
| Table.Distinct | 重複する行の削除はサポートされていません。 |
| Table.RemoveLastN | 下端の行の削除はサポートされていません。 |
| Table.RowCount | サポートされていませんが、値 1 が含まれるカスタム列を追加した後、その列を List.Sum で集計することによって実現できます。 Table.Group はサポートされています。 | 
| 行レベルのエラー処理 | 行レベルのエラー処理は現在サポートされていません。 たとえば、列から数値以外の値を除外する方法の 1 つは、テキスト列を数値に変換することです。 変換が失敗したすべてのセルはエラー状態になり、フィルター処理する必要があります。 このシナリオは、スケールアウトされた M では実現できません。 |
| Table.Transpose | サポートされていません |
| Table.Pivot | サポートされていません |

## <a name="next-steps"></a>次のステップ

[ADF でデータ ラングリング Power Query を作成する](wrangling-tutorial.md)方法について説明します。
