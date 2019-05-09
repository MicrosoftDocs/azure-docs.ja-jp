---
title: Splunk ユーザーのための Azure Monitor ログ クエリ | Microsoft Docs
description: Splunk のことをよく知っているユーザーが Azure Monitor のログ クエリを学習するのを手助けします。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: fb637197139001c67a4cfa773f897e6701dc1e9c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100651"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk ユーザーのための Azure Monitor ログ クエリ

この記事の目的は、Splunk を使い慣れているユーザーが、Azure Monitor でログ クエリを記述するための Kusto クエリ言語を学習するのを支援することです。 2 つを直接比較し、主要な相違点と、既存の知識を活用できる類似点を明らかにします。

## <a name="structure-and-concepts"></a>構造と概念

次の表では、Splunk と Azure Monitor ログの概念とデータ構造を比較します。

 | 概念  | Splunk | Azure Monitor |  Comment (コメント)
 | --- | --- | --- | ---
 | 展開単位  | cluster |  cluster |  Azure Monitor では、任意のクロス クラスター クエリが可能です。 Splunk ではできません。 |
 | データ キャッシュ |  バケット  |  キャッシュおよび保持ポリシー |  データの期間とキャッシュ レベルを制御します。 この設定は、クエリのパフォーマンスと展開のコストに直接影響します。 |
 | データの論理パーティション  |  Index  |  database  |  データの論理的な分離を可能にします。 どちらの実装でも、これらのパーティション間の和集合と結合が可能です。 |
 | 構造化されたイベント メタデータ | 該当なし | table |  Splunk には、イベント メタデータの検索言語に対して公開される概念はありません。 Azure Monitor ログには、列を含むテーブルの概念があります。 各イベント インスタンスは行にマップされます。 |
 | データ レコード | event | 行 |  用語の変更のみです。 |
 | データ レコード属性 | フィールド |  列 |  Azure Monitor では、テーブル構造の一部として事前に定義されます。 Splunk では、イベントごとに固有のフィールドのセットがあります。 |
 | 型 | データ型 |  データ型 |  Azure Monitor のデータ型は、列に設定されるので、より明示的です。 どちらにもデータ型を動的に操作する機能があり、JSON のサポートを含めて、データ型のセットはほぼ同等です。 |
 | クエリと検索  | 検索 | query |  概念は、Azure Monitor でも Splunk でも基本的に同じです。 |
 | イベント取り込み時刻 | システム時刻 | ingestion_time() |  Splunk では、イベントのインデックスが作成された時刻のシステム タイムスタンプが各イベントに設定されます。 Azure Monitor では、ingestion_time() 関数を通じて参照できるシステム列を公開する ingestion_time という名前のポリシーを定義できます。 |

## <a name="functions"></a>Functions

次の表では、Splunk の関数と同等の Azure Monitor の関数を指定します。

|Splunk | Azure Monitor |Comment (コメント)
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| replace | replace() | (1)<br> どちらの製品も `replace()` が受け取るパラメーターの数は 3 つですが、パラメーターは異なることにも注意してください。 |
| substr | substring() | (1)<br>Splunk では 1 から始まるインデックスを使用することにも注意してください。 Azure Monitor のインデックスは 0 から始まります。 |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | matches regex |  (2)  |
| regex | matches regex | Splunk では、`regex` は演算子です。 Azure Monitor では関係演算子です。 |
| searchmatch | == | Splunk の `searchmatch` では、厳密な文字列を検索できます。
| random | rand()<br>rand(n) | Splunk の関数は、0 から 2<sup>31</sup>-1 までの値を返します。 Azure Monitor の場合は、0.0 ～ 1.0 の数値、またはパラメーターが提供された場合は 0 ～ n-1 の数値を返します。
| now | now() | (1)
| relative_time | totimespan() | (1)<br>Azure Monitor の relative_time(datetimeVal, offsetVal) と同等の Splunk の式は datetimeVal + totimespan(offsetVal) です。<br>たとえば、<code>search &#124; eval n=relative_time(now(), "-1d@d")</code> は <code>...  &#124; extend myTime = now() - totimespan("1d")</code> になります。

(1) Splunk では、関数は `eval` 演算子で呼び出されます。 Azure Monitor では、`extend` または `project` の一部として使用されます。<br>(2) Splunk では、関数は `eval` 演算子で呼び出されます。 Azure Monitor では、`where` 演算子で使用できます。


## <a name="operators"></a>演算子

以下のセクションでは、Splunk と Azure Monitor で異なる演算子を使用する例を示します。

> [!NOTE]
> 次の例で、Splunk のフィールド _rule_ は Azure Monitor のテーブルにマップし、Splunk の既定のタイムスタンプは Log Analytics の _ingestion_time()_ 列にマップします。

### <a name="search"></a>Search
Splunk では、`search` キーワードを省略し、引用符なしの文字列を指定することができます。 Azure Monitor では、各クエリを `find` で始める必要があります。また、引用符なしの文字列は列名であり、検索値は引用符で囲まれた文字列である必要があります。 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>filter
Azure Monitor のクエリは、フィルターの表形式の結果セットから開始します。 Splunk では、フィルター処理は現在のインデックスに対する既定の操作です。 Splunk でも `where` 演算子を使用できますが、推奨されません。

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>検査のために n 個のイベント/行を取得する 
Azure Monitor のログ クエリでは、`limit` に対する別名として `take` もサポートされます。 Splunk では、結果が順序付けされている場合、`head` は最初の n 個の結果を返します。 Azure Monitor では、limit は順序付けされませんが、見つかった最初の n 行を返します。

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>フィールド/列で順序づけされた最初の n イベント/行を取得する
下位の結果の場合、Splunk では `tail` を使用します。 Azure Monitor では、並べ替えの方向を `asc` で指定できます。

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>新しいフィールド/列で結果セットを拡張する
Splunk には `eval` 関数もありますが、`eval` 演算子と同等ではありません。 Splunk の `eval` 演算子と Azure Monitor の `extend` 演算子はどちらも、スカラー関数と算術演算子のみをサポートします。

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>名前の変更 
Azure Monitor では、フィールドの名前変更と新しいフィールドの作成に同じ演算子が使用されます。 Splunk には、2 つの異なる演算子 `eval` と `rename` があります。

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>形式の書式設定/プロジェクション
Splunk には、`project-away` と似た演算子はないようです。 UI を使用してフィールドをフィルター処理できます。

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>集計
各種集計関数については、「[Azure Monitor ログ クエリの集計](aggregations.md)」をご覧ください。

| |  | |
|:---|:---|:---|
| Splunk | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>結合
Splunk での結合には重要な制限があります。 サブクエリには 10000 件の結果の制限があり (展開構成ファイルで設定)、結合の種類の数に制限があります。

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>並べ替え
Splunk では、昇順に並べ替えるには、`reverse` 演算子を使用する必要があります。 Azure Monitor では、null を格納する場所の定義もサポートされています (先頭または末尾)。

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>複数値の展開
この演算子は Splunk と Azure Monitor で似ています。

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Results facets, interesting fields
Azure portal での Log Analytics では、最初の列のみが展開されます。 すべての列は、API を介して利用します。

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>重複除去
選択されたレコードの順序を逆にする代わりに、`summarize arg_min()` を使用できます。

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>次の手順

- [Azure Monitor でのログ クエリの記述](get-started-queries.md)に関するレッスンをご覧ください。
