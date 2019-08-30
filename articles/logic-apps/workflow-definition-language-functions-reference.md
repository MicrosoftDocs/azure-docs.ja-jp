---
title: ワークフロー定義言語の関数のリファレンス - Azure Logic Apps と Microsoft Flow
description: Azure Logic Apps および Microsoft Flow 用のワークフロー定義言語で作成された式での関数のリファレンス ガイドです
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 07/27/2019
ms.openlocfilehash: 30123e03a686eed8df0595c8562f2f9d9351bbde
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706449"
---
# <a name="functions-reference-for-workflow-definition-language-in-azure-logic-apps-and-microsoft-flow"></a>Azure Logic Apps および Microsoft Flow でのワークフロー定義言語の関数リファレンス

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) および [Microsoft Flow](https://docs.microsoft.com/flow/getting-started) でのワークフロー定義の場合、一部の[式](../logic-apps/logic-apps-workflow-definition-language.md#expressions)では、ワークフローの実行開始時にはまだ存在しない可能性がある値が実行時のアクションから取得されます。 これらの式でこのような値を参照または処理するには、[ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)によって提供される "*関数*" を使用できます。 

> [!NOTE]
> このリファレンス ページは、Azure Logic Apps と Microsoft Flow の両方に適用されますが、Azure Logic Apps のドキュメントに記載されています。 このページでは特にロジック アプリについて参照されていますが、これらの関数はフローとロジック アプリの両方で動作します。 Microsoft Flow での関数と式について詳しくは、[条件での式の使用](https://docs.microsoft.com/flow/use-expressions-in-conditions)に関する記事をご覧ください。

たとえば、整数や浮動小数点数の合計が必要なときは、[add() 関数](../logic-apps/workflow-definition-language-functions-reference.md#add)などの数学関数を使って値を計算できます。 関数を使用して実行できるタスクの他の例を 2 つ示します。

| タスク | 関数の構文 | 結果 |
| ---- | --------------- | ------ |
| 小文字の形式で文字列を返します。 | toLower('<*text*>') <p>例: toLower('Hello') | "hello" |
| グローバル一意識別子 (GUID) を返します。 | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

[一般的な目的に基づいて](#ordered-by-purpose)関数を探すには、以降の表をご覧ください。 または、各関数の詳細については、[アルファベット順の一覧](#alphabetical-list)を参照してください。

> [!NOTE]
> パラメーターの定義の構文において、パラメーターの後に疑問符 (?) が付いている場合は、そのパラメーターが省略可能であることを意味します。
> たとえば、[getFutureTime()](#getFutureTime) をご覧ください。

## <a name="functions-in-expressions"></a>式の関数

式で関数を使用する方法を示すために、次の例では、`customerName` パラメーターから値を取得し、式の中で [parameters()](#parameters) 関数を使ってその値を `accountName` プロパティに割り当てる方法を示します。

```json
"accountName": "@parameters('customerName')"
```

式で関数を使用できる他の一般的な方法を次に示します。

| タスク | 式の中での関数の構文 |
| ---- | -------------------------------- |
| 項目を関数に渡すことにより、項目に関する作業を実行します。 | "\@<*functionName*>(<*item*>)" |
| 1.入れ子になった `parameters()` 関数を使って、*parameterName* の値を取得します。 </br>2.その値を *functionName* に渡すことによって、結果に関する作業を実行します。 | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1.入れ子になった内側の関数 *functionName* から結果を取得します。 </br>2.結果を外側の関数 *functionName2* に渡します。 | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1.*functionName* から結果を取得します。 </br>2.結果がプロパティ *propertyName* を含むオブジェクトであるとして、そのプロパティの値を取得します。 | "\@<*functionName*>(<*item*>).<*propertyName*>" |
|||

たとえば、`concat()` 関数はパラメーターとして 2 つ以上の文字列値を受け取ることができます。 この関数は、これらの文字列を 1 つの文字列に結合します。
たとえば "Sophia" と "Owen" のような文字列リテラルを渡して、結合された文字列 "SophiaOwen" を取得できます。

```json
"customerName": "@concat('Sophia', 'Owen')"
```

または、パラメーターから文字列値を取得することができます。 次の例では、各 `concat()` パラメーターおよび `firstName` パラメーターと `lastName` パラメーターで、`parameters()` 関数を使っています。 その後、結果の文字列を `concat()` 関数に渡して、結合された文字列 ("SophiaOwen" など) を取得します。

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

どちらの例でも、`customerName` プロパティに結果を割り当てています。

一般的な目的別の使用可能な関数の一覧を次に示します。[アルファベット順](#alphabetical-list)の関数の一覧も示します。

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>文字列関数

文字列を処理するには、以下の文字列関数および一部の[コレクション関数](#collection-functions)も使用できます。
文字列関数は文字列でのみ機能します。

| 文字列関数 | タスク |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | 2 つ以上の文字列を結合し、結合された文字列を返します。 |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | 文字列が指定された部分文字列で終わっているかどうかを調べます。 |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | 文字列としてグローバル一意識別子 (GUID) を生成します。 |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | 部分文字列の開始位置を返します。 |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | 部分文字列の最後の出現箇所の開始位置を返します。 |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | 部分文字列を指定した文字列で置換し、更新された文字列を返します。 |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | 元の文字列で指定された区切り文字に基づいたより大きい文字列から、コンマで区切られた部分文字列を含む配列を返します。 |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | 文字列が特定の部分文字列で始まっているかどうかを調べます。 |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | 文字列から、指定された位置から始まる文字を返します。 |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | 小文字の形式で文字列を返します。 |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | 大文字の形式で文字列を返します。 |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | 文字列から先頭と末尾の空白を削除し、更新された文字列を返します。 |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>コレクション関数

コレクション (通常は配列や文字列、場合によってはディクショナリ) を操作するには、以下のコレクション関数を使用できます。

| コレクション関数 | タスク |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | コレクションに特定の項目があるかどうかを確認します。 |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | コレクションが空かどうかを調べます。 |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | コレクションから最初の項目を返します。 |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | 指定したコレクションすべてに共通する項目 "*のみ*" を含むコレクションを返します。 |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 配列に対する繰り返しアクションの内部で使うと、アクションの現在の繰り返しの間に配列の現在の項目を返します。 |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | 配列の "*すべて*" の項目を含み、指定された区切り記号で各項目が区切られた、文字列を返します。 |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | コレクションから最後の項目を返します。 |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | 文字列または配列内の項目の数を返します。 |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | コレクションの先頭から項目を削除し、"*他のすべて*" の項目を返します。 |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | コレクションの先頭から項目を返します。 |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | 指定した複数のコレクションの "*すべての*" 項目を含む 1 つのコレクションを返します。 |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>論理比較関数

条件の処理、値と式の結果の比較、さまざまな種類のロジックの評価などを行うには、以下の論理比較関数を使用できます。
各関数の完全なリファレンスについては、[アルファベット順の一覧](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)を参照してください。

| 論理比較関数 | タスク |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | すべての式が true かどうかを調べます。 |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | 両方の値が等しいかどうかを調べます。 |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | 1 番目の値が 2 番目の値より大きいかどうかを調べます。 |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | 1 番目の値が 2 番目の値以上かどうかを調べます。 |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | 式が true か false かを調べます。 結果に基づき、指定された値を返します。 |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | 1 番目の値が 2 番目の値より小さいかどうかを調べます。 |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | 1 番目の値が 2 番目の値以下かどうかを調べます。 |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | 式が false かどうかを調べます。 |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | 少なくとも 1 つの式が true かどうかを調べます。 |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>変換関数

値の型または形式を変更するには、以下の変換関数を使用できます。
たとえば、値をブール値から整数に変更できます。
Logic Apps が変換時にコンテンツ タイプを処理する方法の詳細については、「[コンテンツ タイプを処理する](../logic-apps/logic-apps-content-type.md)」を参照してください。
各関数の完全なリファレンスについては、[アルファベット順の一覧](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)を参照してください。

| 変換関数 | タスク |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | 指定した 1 つの入力から配列を返します。 複数の入力の場合は、[createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) をご覧ください。 |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | 文字列の base64 エンコード バージョンを返します。 |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | base64 エンコード文字列のバイナリ バージョンを返します。 |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | base64 エンコード文字列の文字列バージョンを返します。 |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | 入力値のバイナリ バージョンを返します。 |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | 入力値のブール値バージョンを返します。 |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | 複数の入力から配列を作成して返します。 |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | 入力値のデータ URI を返します。 |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | データ URI のバイナリ バージョンを返します。 |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | データ URI の文字列バージョンを返します。 |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | base64 エンコード文字列の文字列バージョンを返します。 |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | データ URI のバイナリ バージョンを返します。 |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | エスケープ文字をデコード バージョンに置き換えた文字列を返します。 |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | URL の安全でない文字をエスケープ文字に置き換えた文字列を返します。 |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | 入力値の浮動小数点数を返します。 |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | 文字列の整数バージョンを返します。 |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | 文字列または XML に対する JSON (JavaScript Object Notation) 型の値またはオブジェクトを返します。 |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | 入力値の文字列バージョンを返します。 |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | URL の安全でない文字がエスケープ文字に置き換えられた、入力値の URI エンコード バージョンを返します。 |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | URI エンコード文字列のバイナリ バージョンを返します。 |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | URI エンコード文字列の文字列バージョンを返します。 |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | 文字列の XML バージョンを返します。 |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>算術関数

整数と浮動小数点数を操作するには、以下の算術関数を使用できます。
各関数の完全なリファレンスについては、[アルファベット順の一覧](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)を参照してください。

| 算術関数 | タスク |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | 2 つの数値を加算した結果を返します。 |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | 2 つの数値を除算した結果を返します。 |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | 数値のセットまたは配列から最大の値を返します。 |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | 数値のセットまたは配列から最小の値を返します。 |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | 2 つの数値を除算した剰余を返します。 |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | 2 つの数値を乗算した積を返します。 |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | 指定された範囲からランダムな整数を返します。 |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | 指定した整数から始まる整数の配列を返します。 |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | 1 番目の数値から 2 番目の数値を減算して、結果を返します。 |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>日付と時刻関数

日付と時刻を操作するには、以下の日付と時刻の関数を使用できます。
各関数の完全なリファレンスについては、[アルファベット順の一覧](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)を参照してください。

| 日付または時刻の関数 | タスク |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | タイムスタンプに日数を加算します。 |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | タイムスタンプに時間数を加算します。 |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | タイムスタンプに分数を加算します。 |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | タイムスタンプに秒数を加算します。 |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | タイムスタンプに時間単位数を加算します。 [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) もご覧ください。 |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | タイムスタンプを協定世界時 (UTC) からターゲット タイム ゾーンに変換します。 |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。 |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | タイムスタンプをソース タイム ゾーンから協定世界時 (UTC) に変換します。 |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | タイムスタンプから月コンポーネントの日付を返します。 |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | タイムスタンプから曜日を返します。 |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | タイムスタンプから年の何日目かを返します。 |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | タイムスタンプから日付を返します。 |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | 現在のタイムスタンプに指定した時刻単位を加えて返します。 [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) もご覧ください。 |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | 現在のタイムスタンプから指定した時刻単位を引いて返します。 [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) もご覧ください。 |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | タイムスタンプの日の開始日時を返します。 |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | タイムスタンプの時刻の開始を返します。 |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | タイムスタンプの月の開始を返します。 |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | タイムスタンプから時間単位数を減算します。 [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) もご覧ください。 |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | 指定したタイムスタンプの `ticks` プロパティの値を返します。 |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | 現在のタイムスタンプを文字列として返します。 |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>ワークフロー関数

以下のワークフロー関数を使うと次のことができます。

* 実行時にワークフロー インスタンスの詳細を取得します。
* ロジック アプリまたはフローのインスタンス化に使われた入力を処理します。
* トリガーやアクションからの出力を参照します。

たとえば、あるアクションからの出力を参照し、後のアクションでそのデータを使用できます。
各関数の完全なリファレンスについては、[アルファベット順の一覧](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)を参照してください。

| ワークフロー関数 | タスク |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | 実行時に現在のアクションの出力を返すか、または他の JSON の名前と値のペアの値を返します。 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) もご覧ください。 |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | 実行時のアクションの `body` 出力を返します。 [body](../logic-apps/workflow-definition-language-functions-reference.md#body) もご覧ください。 |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | 実行時のアクションの出力を返します。 [outputs](../logic-apps/workflow-definition-language-functions-reference.md#outputs) と [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) を参照してください。 |
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | 実行時にアクションの出力を返すか、または他の JSON の名前と値のペアの値を返します。 [action](../logic-apps/workflow-definition-language-functions-reference.md#action) もご覧ください。  |
| [body](#body) | 実行時のアクションの `body` 出力を返します。 [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) もご覧ください。 |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | *form-data* または *form-encoded* アクション出力内のキー名と一致する値を含む配列を作成します。 |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | アクションの *form-data* 出力または *form-encoded* 出力内のキー名と一致する単一の値を返します。 |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 配列に対する繰り返しアクションの内部で使うと、アクションの現在の繰り返しの間に配列の現在の項目を返します。 |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Foreach ループまたは Until ループ内で使用すると、指定したループから現在の項目を返します。|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Until ループ内で使用すると、現在の繰り返しのインデックス値を返します。 この関数は、入れ子になった Until ループ内で使用できます。 |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | トリガーまたはアクションを呼び出す "コールバック URL" を返します。 |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | 複数の部分を持つアクションの出力の特定の部分に対する本文を返します。 |
| [outputs](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | 実行時のアクションの出力を返します。 |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | ワークフローの定義で記述されているパラメーターの値を返します。 |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | 実行時に、または他の JSON の名前と値のペアから、トリガーの出力を返します。 [triggerOutputs](#triggerOutputs) および [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) もご覧ください。 |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | 実行時にトリガーの `body` 出力を返します。 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) をご覧ください。 |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | *form-data* または *form-encoded* トリガー出力のキー名と一致する単一の値を返します。 |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | トリガーの複数部分からなる出力内の特定の部分の本文を返します。 |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | *form-data* または *form-encoded* トリガー出力内のキー名と一致する値を含む配列を作成します。 |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | 実行時のトリガーの出力を返すか、または他の JSON の名前と値のペアの値を返します。 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) をご覧ください。 |
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | 指定した変数の値を返します。 |
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | 実行時にワークフロー自体に関するすべての詳細を返します。 |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI 解析関数

URI (Uniform Resource Identifier) を処理して、URI のさまざまなプロパティ値を取得します。以下の URI 解析関数を使用できます。
各関数の完全なリファレンスについては、[アルファベット順の一覧](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)を参照してください。

| URI 解析関数 | タスク |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | URI (Uniform Resource Identifier) の `host` 値を返します。 |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | URI (Uniform Resource Identifier) の `path` 値を返します。 |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | URI (Uniform Resource Identifier) の `path` と `query` の値を返します。 |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | URI (Uniform Resource Identifier) の `port` 値を返します。 |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | URI (Uniform Resource Identifier) の `query` 値を返します。 |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | URI (Uniform Resource Identifier) の `scheme` 値を返します。 |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>操作関数:JSON と XML

JSON オブジェクトと XML ノードを処理するには、以下の操作関数を使用できます。
各関数の完全なリファレンスについては、[アルファベット順の一覧](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)を参照してください。

| 操作関数 | タスク |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | JSON オブジェクトにプロパティとその値または名前と値のペアを追加し、更新されたオブジェクトを返します。 |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | 1 つまたは複数のパラメーターから、最初の null 以外の値を返します。 |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | JSON オブジェクトからプロパティを削除し、更新されたオブジェクトを返します。 |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | JSON オブジェクトのプロパティの値を設定し、更新されたオブジェクトを返します。 |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | XML で XPath (XML Path Language) 式と一致するノードまたは値を調べて、一致するノードまたは値を返します。 |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>すべての関数 (アルファベット順)

このセクションでは、使用可能なすべての関数をアルファベット順に示します。

<a name="action"></a>

### <a name="action"></a>action

実行時の "*現在の*" アクションの出力を返すか、または式に割り当てることができる他の JSON の名前と値のペアの値を返します。
既定では、この関数はアクション オブジェクト全体を参照しますが、必要に応じて値を取得するプロパティを指定することができます。
[actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions) も参照してください。

`action()` 関数は、次の場所でのみ使うことができます。

* 元の `subscribe` 要求の結果にアクセスできるように、webhook アクションの `unsubscribe` プロパティ
* アクションの `trackedProperties` プロパティ
* アクションの `do-until` ループ条件

```
action()
action().outputs.body.<property>
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*property*> | いいえ | string | 値が必要なアクション オブジェクトのプロパティの名前: **name**、**startTime**、**endTime**、**inputs**、**outputs**、**status**、**code**、**trackingId**、**clientTrackingId**。 Azure portal では、特定の実行履歴の詳細を調べることで、これらのプロパティを確認できます。 詳しくは、[REST API のワークフロー実行アクション](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)に関するページをご覧ください。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*action-output*> | string | 現在のアクションまたはプロパティからの出力 |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

実行時のアクションの `body` 出力を返します。
`actions('<actionName>').outputs.body` の短縮形です。
[body()](#body) および [actions()](#actions) をご覧ください。

```
actionBody('<actionName>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 取得するアクションの `body` 出力の名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*action-body-output*> | string | 指定したアクションからの `body` 出力 |
||||

*例*

この例は、Twitter アクション `Get user` からの `body` 出力を取得します。

```
actionBody('Get_user')
```

返される結果:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

実行時のアクションの出力を返します。  `actions('<actionName>').outputs` の短縮形です。 [actions()](#actions) をご覧ください。 `actionOutputs()` 関数はロジック アプリ デザイナーでは `outputs()` に解決されるため、`actionOutputs()` ではなく [outputs()](#outputs) を使用することを検討してください。 どちらの関数も機能は同じですが、`outputs()` をお勧めします。

```
actionOutputs('<actionName>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 取得するアクションの出力の名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*output*> | string | 指定したアクションからの出力 |
||||

*例*

この例は、Twitter アクション `Get user` からの出力を取得します。

```
actionOutputs('Get_user')
```

返される結果:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>actions

実行時のアクションの出力を返すか、または式に割り当てることができる他の JSON の名前と値のペアの値を返します。 既定では、この関数はアクション オブジェクト全体を参照しますが、必要に応じて値を取得するプロパティを指定することができます。
短縮バージョンについては、[actionBody()](#actionBody)、[actionOutputs()](#actionOutputs)、[body()](#body) をご覧ください。
現在のアクションの場合は、[action()](#action) をご覧ください。

> [!NOTE]
> 以前は、`actions()` 関数を使うか、またはアクションが別のアクションからの出力を基にして実行したことを指定する場合は `conditions` 要素を使いました。 ただし、現在は、アクション間の依存関係を明示的に宣言するには、依存アクションの `runAfter` プロパティを使い必要があります。
> `runAfter` プロパティについて詳しくは、[runAfter プロパティを使ってエラーをキャッチして処理する方法](../logic-apps/logic-apps-workflow-definition-language.md)に関するページをご覧ください。

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 出力を取得するアクション オブジェクトの名前  |
| <*property*> | いいえ | string | 値が必要なアクション オブジェクトのプロパティの名前: **name**、**startTime**、**endTime**、**inputs**、**outputs**、**status**、**code**、**trackingId**、**clientTrackingId**。 Azure portal では、特定の実行履歴の詳細を調べることで、これらのプロパティを確認できます。 詳しくは、[REST API のワークフロー実行アクション](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)に関するページをご覧ください。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*action-output*> | string | 指定したアクションまたはプロパティからの出力 |
||||

*例*

この例は、実行時に Twitter アクション `Get user` から `status` プロパティの値を取得します。

```
actions('Get_user').outputs.body.status
```

返される結果: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

2 つの数値を加算した結果を返します。

```
add(<summand_1>, <summand_2>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>、<*summand_2*> | はい | 整数、浮動小数点数、または混合 | 加算する数値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*result-sum*> | 整数または浮動小数点数 | 指定した数値を加算した結果 |
||||

*例*

この例は、指定した数値を加算します。

```
add(1, 1.5)
```

返される結果: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

タイムスタンプに日数を加算します。

```
addDays('<timestamp>', <days>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*days*> | はい | 整数 | 追加する正または負の日数 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | タイムスタンプに指定した日数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 10 日を加算します。

```
addDays('2018-03-15T13:00:00Z', 10)
```

返される結果: `"2018-03-25T00:00:0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 日を減算します。

```
addDays('2018-03-15T00:00:00Z', -5)
```

返される結果: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

タイムスタンプに時間数を加算します。

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*hours*> | はい | 整数 | 追加する正または負の時間数 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | タイムスタンプに指定した時間数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 10 時間を加算します。

```
addHours('2018-03-15T00:00:00Z', 10)
```

返される結果: `"2018-03-15T10:00:0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 時間を減算します。

```
addHours('2018-03-15T15:00:00Z', -5)
```

返される結果: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

タイムスタンプに分数を加算します。

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*minutes*> | はい | 整数 | 追加する正または負の分数 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | タイムスタンプに指定した分数を加えた値 |
||||

*例 1*

この例は、指定したタイムスタンプに 10 分を加算します。

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

返される結果: `"2018-03-15T00:20:00.0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 分を減算します。

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

返される結果: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

JSON オブジェクトにプロパティとその値または名前と値のペアを追加し、更新されたオブジェクトを返します。 実行時にオブジェクトが既に存在する場合、関数はエラーをスローします。

```
addProperty(<object>, '<property>', <value>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object*> | はい | Object | プロパティを追加する JSON オブジェクト |
| <*property*> | はい | string | 追加するプロパティの名前 |
| <*value*> | はい | Any | プロパティの値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | 指定したプロパティを含む更新された JSON オブジェクト |
||||

*例*

この例は、`accountNumber` プロパティを `customerProfile` オブジェクトに追加します。オブジェクトは、[JSON()](#json) 関数で JSON に変換されます。
この関数は、[guid()](#guid) 関数によって生成された値を代入し、更新されたオブジェクトを返します。

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

タイムスタンプに秒数を加算します。

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*seconds*> | はい | 整数 | 追加する正または負の秒数 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | タイムスタンプに指定した秒数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 10 秒を加算します。

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

返される結果: `"2018-03-15T00:00:10.0000000Z"`

*例 2*

この例は、指定したタイムスタンプから 5 秒を減算します。

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

返される結果: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

タイムスタンプに時間単位数を加算します。
[getFutureTime()](#getFutureTime) もご覧ください。

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*interval*> | はい | 整数 | 追加する指定した時間単位の数 |
| <*timeUnit*> | はい | string | *間隔*と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | タイムスタンプに指定した時間単位数を加えた値  |
||||

*例 1*

この例は、指定したタイムスタンプに 1 日を加算します。

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

返される結果: `"2018-01-02T00:00:00.0000000Z"`

*例 2*

この例は、指定したタイムスタンプに 1 日を加算します。

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

省略可能な "D" 形式を使用して返される結果: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>and

すべての式が true かどうかを調べます。
すべての式が true の場合は true を返し、少なくとも 1 つの式が false の場合は false を返します。

```
and(<expression1>, <expression2>, ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | はい | Boolean | 調べる式 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| true または false | Boolean | すべての式が true の場合は true を返します。 少なくとも 1 つの式が false の場合は false を返します。 |
||||

*例 1*

これらの例は、指定したブール値がすべて true かどうかを調べます。

```
and(true, true)
and(false, true)
and(false, false)
```

次の結果を返します。

* 1 番目の例:両方の式が true なので、`true` を返します。
* 2 番目の例:片方の式が false なので、`false` を返します。
* 3 番目の例:両方の式が false なので、`false` を返します。

*例 2*

これらの例は、指定した式がすべて true かどうかを調べます。

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

次の結果を返します。

* 1 番目の例:両方の式が true なので、`true` を返します。
* 2 番目の例:片方の式が false なので、`false` を返します。
* 3 番目の例:両方の式が false なので、`false` を返します。

<a name="array"></a>

### <a name="array"></a>array

指定した 1 つの入力から配列を返します。
複数の入力の場合は、[createArray()](#createArray) をご覧ください。

```
array('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 配列を作成するための文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | 指定した 1 つの入力を含む配列 |
||||

*例*

この例は、"hello" という文字列から配列を作成します。

```
array('hello')
```

返される結果: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

文字列の base64 エンコード バージョンを返します。

```
base64('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 入力文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*base64-string*> | string | 入力文字列の base64 エンコード バージョン |
||||

*例*

この例は、"hello" という文字列を base64 エンコード文字列に変換します。

```
base64('hello')
```

返される結果: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

base64 エンコード文字列のバイナリ バージョンを返します。

```
base64ToBinary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換する base64 エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | string | base64 エンコード文字列のバイナリ バージョン |
||||

*例*

この例は、"aGVsbG8=" という base64 エンコード文字列をバイナリ文字列に変換します。

```
base64ToBinary('aGVsbG8=')
```

返される結果:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

base64 エンコード文字列の文字列バージョンを返し、実質的に base64 の文字列をデコードします。
[decodeBase64()](#decodeBase64) ではなく、この関数を使用してください。
どちらの関数も機能は同じですが、`base64ToString()` をお勧めします。

```
base64ToString('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | デコードする base64 エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | string | base64 エンコード文字列の文字列バージョン。 |
||||

*例*

この例は、"aGVsbG8=" という base64 エンコード文字列を単なる文字列に変換します。

```
base64ToString('aGVsbG8=')
```

返される結果: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

文字列のバイナリ バージョンを返します。

```
binary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | string | 指定した文字列のバイナリ バージョン |
||||

*例*

この例は、"hello" という文字列をバイナリ文字列に変換します。

```
binary('hello')
```

返される結果:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

実行時のアクションの `body` 出力を返します。
`actions('<actionName>').outputs.body` の短縮形です。
[actionBody()](#actionBody) および [actions()](#actions) をご覧ください。

```
body('<actionName>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 取得するアクションの `body` 出力の名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*action-body-output*> | string | 指定したアクションからの `body` 出力 |
||||

*例*

この例は、Twitter アクション `Get user` からの `body` 出力を取得します。

```
body('Get_user')
```

返される結果:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

値のブール値バージョンを返します。

```
bool(<value>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | Any | 変換する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 指定した値のブール値バージョン |
||||

*例*

これらの例は、指定した値をブール値に変換します。

```
bool(1)
bool(0)
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

1 つまたは複数のパラメーターから、最初の null 以外の値を返します。
空の文字列、空の配列、空のオブジェクトは null ではありません。

```
coalesce(<object_1>, <object_2>, ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | はい | 任意、型が混在してもかまいません | null かどうか調べる 1 つまたは複数の項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Any | null ではない最初の項目または値。 すべてのパラメーターが null の場合、この関数は null を返します。 |
||||

*例*

これらの例は、指定した値から最初の null 以外の値を返し、すべての値が null のときは null を返します。

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `"hello"`
* 3 番目の例: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

2 つ以上の文字列を結合し、結合された文字列を返します。

```
concat('<text1>', '<text2>', ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | はい | string | 結合する少なくとも 2 つの文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*text1text2...* > | string | 入力文字列を結合して作成された文字列 |
||||

*例*

この例は、文字列 "Hello" と "World" を結合します。

```
concat('Hello', 'World')
```

返される結果: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

コレクションに特定の項目があるかどうかを確認します。
項目が見つかった場合は true を返し、見つからない場合は false を返します。
この関数は、大文字と小文字を区別します。

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

具体的には、この関数は次のコレクション型で動作します。

* "*文字列*" からの "*部分文字列*" の検索
* "*配列*" からの "*値*" の検索
* "*ディクショナリ*" からの "*キー*" の検索

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列、ディクショナリ | 調べるコレクション |
| <*value*> | はい | それぞれ文字列、配列、ディクショナリ | 検索する項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 項目が見つかった場合は true を返します。 見つからなかった場合は false を返します。 |
||||

*例 1*

この例は、文字列 "hello world" で部分文字列 "world" を調べて、true を返します。

```
contains('hello world', 'world')
```

*例 2*

この例は、文字列 "hello world" で部分文字列 "universe" を調べて、false を返します。

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

タイムスタンプを協定世界時 (UTC) からターゲット タイム ゾーンに変換します。

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*destinationTimeZone*> | はい | string | ターゲット タイム ゾーンの名前。 詳しくは、「[Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))」(タイム ゾーン ID) をご覧ください。 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | ターゲット タイム ゾーンに変換されたタイムスタンプ |
||||

*例 1*

この例は、タイムスタンプを指定したタイム ゾーンに変換します。

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

返される結果: `"2018-01-01T00:00:00.0000000"`

*例 2*

この例は、タイムスタンプを指定したタイム ゾーンに変換して形式を設定します。

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

返される結果: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*sourceTimeZone*> | はい | string | ソース タイム ゾーンの名前。 詳しくは、「[Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))」(タイム ゾーン ID) をご覧ください。 |
| <*destinationTimeZone*> | はい | string | ターゲット タイム ゾーンの名前。 詳しくは、「[Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))」(タイム ゾーン ID) をご覧ください。 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | ターゲット タイム ゾーンに変換されたタイムスタンプ |
||||

*例 1*

この例は、ソース タイム ゾーンをターゲット タイム ゾーンに変換します。

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

返される結果: `"2018-01-01T00:00:00.0000000"`

*例 2*

この例は、タイム ゾーンを指定したタイム ゾーンに変換して形式を設定します。

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

返される結果: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

タイムスタンプをソース タイム ゾーンから協定世界時 (UTC) に変換します。

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*sourceTimeZone*> | はい | string | ソース タイム ゾーンの名前。 詳しくは、「[Time Zone IDs](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))」(タイム ゾーン ID) をご覧ください。 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | UTC に変換されたタイムスタンプ |
||||

*例 1*

この例は、タイムスタンプを UTC に変換します。

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

返される結果: `"2018-01-01T08:00:00.0000000Z"`

*例 2*

この例は、タイムスタンプを UTC に変換します。

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

返される結果: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

複数の入力から配列を作成して返します。
単一入力の配列については、[array()](#array) をご覧ください。

```
createArray('<object1>', '<object2>', ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | はい | 任意、ただし混在していてはなりません | 配列を作成する少なくとも 2 つの項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | すべての入力項目から作成された配列 |
||||

*例*

この例は、以下の入力から配列を作成します。

```
createArray('h', 'e', 'l', 'l', 'o')
```

返される結果: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

文字列のデータ URI (Uniform Resource Identifier) を返します。

```
dataUri('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*data-uri*> | string | 入力文字列に対するデータ URI |
||||

*例*

この例は、"hello" という文字列に対する URI を作成します。

```
dataUri('hello')
```

返される結果: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

データ URI (Uniform Resource Identifier) のバイナリ バージョンを返します。
[decodeDataUri()](#decodeDataUri) ではなく、この関数を使用してください。
どちらの関数も機能は同じですが、`dataUriBinary()` をお勧めします。

```
dataUriToBinary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換するデータ URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | string | データ URI のバイナリ バージョン |
||||

*例*

この例は、次のデータ URI のバイナリ バージョンを作成します。

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

返される結果:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

データ URI (Uniform Resource Identifier) の文字列バージョンを返します。

```
dataUriToString('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換するデータ URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | string | データ URI の文字列バージョン |
||||

*例*

この例は、次のデータ URI の文字列を作成します。

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

返される結果: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

タイムスタンプから月の日付を返します。

```
dayOfMonth('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*day-of-month*> | 整数 | 指定したタイムスタンプの月の日付 |
||||

*例*

この例は、次のタイムスタンプから月の日付を返します。

```
dayOfMonth('2018-03-15T13:27:36Z')
```

返される結果: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

タイムスタンプから曜日を返します。

```
dayOfWeek('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*day-of-week*> | 整数 | 指定したタイムスタンプの曜日。日曜日は 0、月曜日は 1、などとなります。 |
||||

*例*

この例は、次のタイムスタンプから曜日を返します。

```
dayOfWeek('2018-03-15T13:27:36Z')
```

返される結果: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

タイムスタンプから年の何日目かを返します。

```
dayOfYear('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*day-of-year*> | 整数 | 指定したタイムスタンプの年初からの通算日数 |
||||

*例*

この例は、次のタイムスタンプから年の何日目かを返します。

```
dayOfYear('2018-03-15T13:27:36Z')
```

返される結果: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

base64 エンコード文字列の文字列バージョンを返し、実質的に base64 の文字列をデコードします。
`decodeBase64()` ではなく、[base64ToString()](#base64ToString) を使うようにしてください。
どちらの関数も機能は同じですが、`base64ToString()` をお勧めします。

```
decodeBase64('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | デコードする base64 エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | string | base64 エンコード文字列の文字列バージョン。 |
||||

*例*

この例は、base64 エンコード文字列の文字列を作成します。

```
decodeBase64('aGVsbG8=')
```

返される結果: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

データ URI (Uniform Resource Identifier) のバイナリ バージョンを返します。
`decodeDataUri()` ではなく、[dataUriToBinary()](#dataUriToBinary) を使うようにしてください。
どちらの関数も機能は同じですが、`dataUriToBinary()` をお勧めします。

```
decodeDataUri('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | デコードするデータ URI 文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | string | データ URI 文字列のバイナリ バージョン |
||||

*例*

この例は、次のデータ URI のバイナリ バージョンを返します。

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

返される結果:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

エスケープ文字をデコード バージョンに置き換えた文字列を返します。

```
decodeUriComponent('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | エスケープ文字をデコードする文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | string | エスケープ文字がデコードされた更新後の文字列 |
||||

*例*

この例は、次の文字列内のエスケープ文字をデコード バージョンに置き換えます。

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

返される結果: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

2 つの数値を除算した結果の整数値を返します。
結果の残りの部分を取得するには、[mod()](#mod) をご覧ください。

```
div(<dividend>, <divisor>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | はい | 整数または浮動小数点数 | *divisor* によって除算される値。 |
| <*divisor*> | はい | 整数または浮動小数点数 | *dividend* を除算する値。0 にすることはできません。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*quotient-result*> | 整数 | 1 番目の数値を 2 番目の数値で除算した結果の整数値 |
||||

*例*

どちらの例も、1 番目の数値を 2 番目の数値で除算します。

```
div(10, 5)
div(11, 5)
```

返される結果: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

URL の安全でない文字がエスケープ文字に置き換えられた、文字列の URI (Uniform Resource Identifier) エンコード バージョンを返します。
`encodeUriComponent()` ではなく、[uriComponent()](#uriComponent) を使うようにしてください。
どちらの関数も機能は同じですが、`uriComponent()` をお勧めします。

```
encodeUriComponent('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | URI エンコード形式に変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | string | エスケープ文字が使われている URI エンコード文字列 |
||||

*例*

この例は、次の文字列の URI エンコード バージョンを作成します。

```
encodeUriComponent('https://contoso.com')
```

返される結果: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

コレクションが空かどうかを調べます。
コレクションが空の場合は true を返し、空でない場合は false を返します。

```
empty('<collection>')
empty([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列、オブジェクト | 調べるコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | コレクションが空の場合は true を返します。 空でない場合は false を返します。 |
||||

*例*

これらの例は、指定したコレクションが空かどうかを調べます。

```
empty('')
empty('abc')
```

次の結果を返します。

* 1 番目の例:空の文字列を渡しているので、関数は `true` を返します。
* 2 番目の例:文字列 "abc" を渡しているので、関数は `false` を返します。

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

文字列が特定の部分文字列で終わっているかどうかを調べます。
部分文字列が見つかった場合は true を返し、見つからない場合は false を返します。
この関数は、大文字と小文字を区別しません。

```
endsWith('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 調べる文字列。 |
| <*searchText*> | はい | string | 検索する末尾の部分文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false  | Boolean | 末尾の部分文字列が見つかった場合は true を返します。 見つからなかった場合は false を返します。 |
||||

*例 1*

この例は、文字列 "hello world" が文字列 "world" で終わっているかどうかを調べます。

```
endsWith('hello world', 'world')
```

返される結果: `true`

*例 2*

この例は、文字列 "hello world" が文字列 "universe" で終わっているかどうかを調べます。

```
endsWith('hello world', 'universe')
```

返される結果: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

両方の値、式、またはオブジェクトが等しいかどうかを調べます。
両方が等しい場合は true を返し、等しくない場合は false を返します。

```
equals('<object1>', '<object2>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | はい | 各種 | 比較する値、式、またはオブジェクト |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 両方が等しい場合は true を返します。 等しくない場合は false を返します。 |
||||

*例*

これらの例は、指定した入力が等しいかどうかを調べます。

```
equals(true, 1)
equals('abc', 'abcd')
```

次の結果を返します。

* 1 番目の例:両方の値が等しいので、関数は `true` を返します。
* 2 番目の例:両方の値が等しくないので、関数は `false` を返します。

<a name="first"></a>

### <a name="first"></a>first

文字列または配列から最初の項目を返します。

```
first('<collection>')
first([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 最初の項目を検索するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Any | コレクション内の最初の項目 |
||||

*例*

これらの例は、以下のコレクション内の最初の項目を検索します。

```
first('hello')
first(createArray(0, 1, 2))
```

次の結果を返します。

* 1 番目の例: `"h"`
* 2 番目の例: `0`

<a name="float"></a>

### <a name="float"></a>float

浮動小数点数の文字列バージョンを実際の浮動小数点数に変換します。
この関数は、ロジック アプリやフローなどのアプリにカスタム パラメーターを渡す場合にのみ使用できます。

```
float('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換する有効な浮動小数点数を含む文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | 指定した文字列の浮動小数点数 |
||||

*例*

この例は、次の浮動小数点数の文字列バージョンを作成します。

```
float('10.333')
```

返される結果: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

指定した形式でタイムスタンプを返します。

```
formatDateTime('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | string | 指定した形式に更新されたタイムスタンプ。 |
||||

*例*

この例は、タイムスタンプを指定した形式に変換します。

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

返される結果: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

アクションの *form-data* 出力または *form-encoded* 出力内のキー名と一致する値の配列を返します。

```
formDataMultiValues('<actionName>', '<key>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 出力が指定したキー値であるアクション |
| <*key*> | はい | string | 値を指定するキーの名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | 指定したキーと一致するすべての値で構成される配列 |
||||

*例*

この例は、指定したアクションの form-data 出力または form-encoded 出力内の "Subject" キーの値から配列を作成します。

```
formDataMultiValues('Send_an_email', 'Subject')
```

配列で件名のテキストを返します (例: `["Hello world"]`)

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

アクションの *form-data* 出力または *form-encoded* 出力内のキー名と一致する単一の値を返します。
複数の一致が見つかった場合、関数はエラーをスローします。

```
formDataValue('<actionName>', '<key>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 出力が指定したキー値であるアクション |
| <*key*> | はい | string | 値を指定するキーの名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*key-value*> | string | 指定したキーの値  |
||||

*例*

この例は、指定したアクションの form-data 出力または form-encoded 出力内の "Subject" キーの値から文字列を作成します。

```
formDataValue('Send_an_email', 'Subject')
```

文字列として件名のテキストを返します (例: `"Hello world"`)

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

現在のタイムスタンプに指定した時刻単位を加えて返します。

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | はい | 整数 | 減算する指定した時間単位の数 |
| <*timeUnit*> | はい | string | *間隔*と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 現在のタイムスタンプに指定した時間単位数を加えた値 |
||||

*例 1*

現在のタイムスタンプが "2018-03-01T00:00:00.0000000Z" であるものとします。
この例は、そのタイムスタンプに 5 日を加算します。

```
getFutureTime(5, 'Day')
```

返される結果: `"2018-03-06T00:00:00.0000000Z"`

*例 2*

現在のタイムスタンプが "2018-03-01T00:00:00.0000000Z" であるものとします。
この例は、5 日を加算して、結果を "D" 形式に変換します。

```
getFutureTime(5, 'Day', 'D')
```

返される結果: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

現在のタイムスタンプから指定した時刻単位を引いて返します。

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | はい | 整数 | 減算する指定した時間単位の数 |
| <*timeUnit*> | はい | string | *間隔*と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 現在のタイムスタンプから指定した時間単位数を引いた値 |
||||

*例 1*

現在のタイムスタンプが "2018-02-01T00:00:00.0000000Z" であるものとします。
この例は、そのタイムスタンプから 5 日を減算します。

```
getPastTime(5, 'Day')
```

返される結果: `"2018-01-27T00:00:00.0000000Z"`

*例 2*

現在のタイムスタンプが "2018-02-01T00:00:00.0000000Z" であるものとします。
この例は、5 日を減算して、結果を "D" 形式に変換します。

```
getPastTime(5, 'Day', 'D')
```

返される結果: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

1 番目の値が 2 番目の値より大きいかどうかを調べます。
1 番目の値の方が大きい場合は true を返し、大きくない場合は false を返します。

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値より大きいかどうかを調べる 1 番目の値 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 1 番目の値が 2 番目の値より大きい場合は true を返します。 1 番目の値が 2 番目の値以下の場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値より大きいかどうかを調べます。

```
greater(10, 5)
greater('apple', 'banana')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

1 番目の値が 2 番目の値以上かどうかを調べます。
1 番目の値が大きいか等しい場合は true を返し、小さい場合は false を返します。

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値以上かどうかを調べる 1 番目の値。 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 1 番目の値が 2 番目の値より大きいか等しい場合は true を返します。 1 番目の値が 2 番目より小さい場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値以上かどうかを調べます。

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

グローバル一意識別子 (GUID) を文字列として生成します (例: "c2ecc88d-88c8-4096-912c-d6f2e2b138ce")。

```
guid()
```

既定の "D" 形式 (ハイフンで区切られた 32 桁) 以外の GUID 形式を指定することもできます。

```
guid('<format>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*format*> | いいえ | string | 返される GUID の単一の[形式指定子](https://msdn.microsoft.com/library/97af8hh4)。 規定の形式は "D" ですが、"N"、"D"、"B"、"P"、"X" も指定できます。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*GUID-value*> | string | ランダムに生成された GUID |
||||

*例*

この例は同じ GUID を生成しますが、32 桁で、ハイフンによって区切られており、かっこで囲まれています。

```
guid('P')
```

返される結果: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

式が true か false かを調べます。
結果に基づき、指定された値を返します。

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | はい | Boolean | 調べる式。 |
| <*valueIfTrue*> | はい | Any | 式が true の場合に返す値 |
| <*valueIfFalse*> | はい | Any | 式が false の場合に返す値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Any | 式が true か false かに基づいて返すように指定された値 |
||||

*例*

この例は、指定した式が true を返すため `"yes"` を返します。
それ以外の場合、この例は `"no"` を返します。

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

部分文字列の開始位置またはインデックス値を返します。
この関数は大文字と小文字を区別せず、インデックスは値 0 から始まります。

```
indexOf('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 検索する部分文字列を含む文字列 |
| <*searchText*> | はい | string | 検索する部分文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*index-value*>| 整数 | 指定した部分文字列の開始位置またはインデックス値。 <p>文字列が見つからない場合は、値 -1 を返します。 |
||||

*例*

この例は、文字列 "hello world" 内で部分文字列 "world" の開始インデックス値を検索します。

```
indexOf('hello world', 'world')
```

返される結果: `6`

<a name="int"></a>

### <a name="int"></a>int

文字列の整数バージョンを返します。

```
int('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*integer-result*> | 整数 | 指定した文字列の整数バージョン |
||||

*例*

この例は、文字列 "10" の整数バージョンを作成します。

```
int('10')
```

返される結果: `10`

<a name="item"></a>

### <a name="item"></a>item

配列に対する繰り返しアクションの内部で使うと、アクションの現在の繰り返しの間に配列の現在の項目を返します。
その項目のプロパティから値を取得することもできます。

```
item()
```

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Any | アクションの現在の繰り返しに対する配列内の現在の項目 |
||||

*例*

この例は、for-each ループの現在の繰り返しの内部で "Send_an_email" アクションに対する現在のメッセージから `body` 要素を取得します。

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

for-each ループの各サイクルから現在の項目を返します。
この関数は、for-each ループの内部で使います。

```
items('<loopName>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | はい | string | for-each ループの名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*item*> | Any | 指定した for-each ループの現在のサイクルからの項目 |
||||

*例*

この例は、指定した for-each ループから現在の項目を取得します。

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Until ループ内の現在の繰り返しのインデックス値を返します。 この関数は、入れ子になった Until ループ内で使用できます。 

```
iterationIndexes('<loopName>')
```

| パラメーター | 必須 | Type | 説明 | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | はい | string | Until ループの名前 | 
||||| 

| 戻り値 | Type | 説明 | 
| ------------ | ---- | ----------- | 
| <*index*> | 整数 | 指定された Until ループ内の現在の繰り返しのインデックス値 | 
|||| 

*例* 

この例では、カウンター変数を作成し、カウンター値が 5 に達するまで、Until ループ内の各繰り返しの間に 1 つずつその変数を増分します。 また、この例では各繰り返しの現在のインデックスを追跡する変数も作成します。 Until ループで、各繰り返しの間にカウンターを増分し、カウンターの値を現在のインデックス値に割り当ててから、カウンターを増分します。 いつでも、現在のインデックス値を取得することによって、現在の繰り返しの回数を判別できます。

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

文字列または XML に対する JSON (JavaScript Object Notation) 型の値またはオブジェクトを返します。

```
json('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 文字列、XML | 変換する文字列または XML |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*JSON-result*> | JSON ネイティブの型またはオブジェクト | 指定した文字列または XML に対する JSON ネイティブの型の値またはオブジェクト。 文字列が null の場合、この関数は空のオブジェクトを返します。 |
||||

*例 1*

この例は、次の文字列を JSON 値に変換します。

```
json('[1, 2, 3]')
```

返される結果: `[1, 2, 3]`

*例 2*

この例は、次の文字列を JSON に変換します。

```
json('{"fullName": "Sophia Owen"}')
```

返される結果:

```
{
  "fullName": "Sophia Owen"
}
```

*例 3*

この例は、次の XML を JSON に変換します。

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

返される結果:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>intersection

指定したコレクションすべてに共通する項目 "*のみ*" を含むコレクションを返します。
結果に含まれるためには、この関数に渡されるすべてのコレクションに項目が含まれる必要があります。
1 つまたは複数の項目が同じ名前である場合は、その名前を持つ最後の項目が結果に含まれます。

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | はい | 配列またはオブジェクト、両方ともは不可 | 共通項目 "*のみ*" を抽出するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*common-items*> | それぞれ、配列またはオブジェクト | 指定したコレクションすべてに共通する項目のみを含むコレクション |
||||

*例*

この例は、次のすべての配列に共通する項目を検索します。

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

そして、それらの項目 `[1, 2]` "*のみ*" を含む配列を返します。

<a name="join"></a>

### <a name="join"></a>join

配列のすべての項目を含み、各文字が "*区切り記号*" で区切られた文字列を返します。

```
join([<collection>], '<delimiter>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | Array | 結合する項目を含む配列 |
| <*delimiter*> | はい | string | 結果の文字列内の各文字の間に挿入される区切り記号 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | string | 指定した配列内のすべての項目から作成された結果の文字列 |
||||

*例*

この例は、指定した文字で区切られた、次の配列のすべての項目を含む文字列を作成します。

```
join(createArray('a', 'b', 'c'), '.')
```

返される結果: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

コレクションから最後の項目を返します。

```
last('<collection>')
last([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 最後の項目を検索するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | それぞれ文字列、配列 | コレクション内の最後の項目 |
||||

*例*

これらの例は、以下のコレクション内の最後の項目を検索します。

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

次の結果を返します。

* 1 番目の例: `"d"`
* 2 番目の例: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

部分文字列の最後の出現箇所の開始位置またはインデックス値を返します。
この関数は大文字と小文字を区別せず、インデックスは値 0 から始まります。

```
lastIndexOf('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 検索する部分文字列を含む文字列 |
| <*searchText*> | はい | string | 検索する部分文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | 整数 | 指定された部分文字列の最後の出現箇所の開始位置またはインデックス値。 <p>文字列が見つからない場合は、値 -1 を返します。 |
||||

*例*

この例は、文字列 "hello world" 内の部分文字列 "world" の最後の出現箇所の開始インデックス値を検索します。

```
lastIndexOf('hello world', 'world')
```

返される結果: `6`

<a name="length"></a>

### <a name="length"></a>length

コレクション内の項目の数を返します。

```
length('<collection>')
length([<collection>])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 項目を数えるコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*length-or-count*> | 整数 | コレクション内の項目数 |
||||

*例*

これらの例は、次のコレクション内の項目の数をカウントします。

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

返される結果: `4`

<a name="less"></a>

### <a name="less"></a>less

1 番目の値が 2 番目の値より小さいかどうかを調べます。
1 番目の値の方が小さい場合は true を返し、小さくないい場合は false を返します。

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値より小さいかどうかを調べる 1 番目の値 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 1 番目の値の方が 2 番目より小さい場合は true を返します。 1 番目の値が 2 番目の値以上の場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値より小さいかどうかを調べます。

```
less(5, 10)
less('banana', 'apple')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

1 番目の値が 2 番目の値以下かどうかを調べます。
1 番目の値が小さいか等しい場合は true を返し、大きい場合は false を返します。

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | 整数、浮動小数点数、混合 | 2 番目の値以下かどうかを調べる 1 番目の値。 |
| <*compareTo*> | はい | それぞれ整数、浮動小数点数、混合 | 比較する項目 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false  | Boolean | 1 番目の値が 2 番目の値より小さいか等しい場合は true を返します。 1 番目の値が 2 番目の値より大きい場合は false を返します。 |
||||

*例*

これらの例は、1 番目の値が 2 番目の値以下かどうかを調べます。

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

次の結果を返します。

* 1 番目の例: `true`
* 2 番目の例: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

トリガーまたはアクションを呼び出す "コールバック URL" を返します。
この関数は、**HttpWebhook** および **ApiConnectionWebhook** コネクタ型に対するトリガーとアクションでのみ機能し、**Manual**、**Recurrence**、**HTTP**、および **APIConnection** 型では機能しません。

```
listCallbackUrl()
```

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*callback-URL*> | string | トリガーまたはアクションに対するコールバック URL |
||||

*例*

この例は、この関数が返すことのあるコールバック URL の例を示します。

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

両端を含む数値のリストまたは配列から最大の値を返します。

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | はい | 整数、浮動小数点数、または両方 | 最大値を取得する数値のセット |
| [<*number1*>, <*number2*>, ...] | はい | 配列 - 整数、浮動小数点数、または両方 | 最大値を取得する数値の配列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*max-value*> | 整数または浮動小数点数 | 指定した配列内または数値セット内で最大の値 |
||||

*例*

これらの例は、数値のセットと配列から最大値を取得します。

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

返される結果: `3`

<a name="min"></a>

### <a name="min"></a>min

数値のセットまたは配列から最小の値を返します。

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | はい | 整数、浮動小数点数、または両方 | 最小値を取得する数値のセット |
| [<*number1*>, <*number2*>, ...] | はい | 配列 - 整数、浮動小数点数、または両方 | 最小値を取得する数値の配列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*min-value*> | 整数または浮動小数点数 | 指定した数値セット内または配列内で最小の値 |
||||

*例*

これらの例は、数値のセットと配列で最小の値を取得します。

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

返される結果: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

2 つの数値を除算した剰余を返します。
整数の結果を取得するには、[div()](#div) をご覧ください。

```
mod(<dividend>, <divisor>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | はい | 整数または浮動小数点数 | *divisor* によって除算される値。 |
| <*divisor*> | はい | 整数または浮動小数点数 | *dividend* を除算する値。0 にすることはできません。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*modulo-result*> | 整数または浮動小数点数 | 1 番目の数値を 2 番目の数値で除算した剰余 |
||||

*例*

この例は、1 番目の数値を 2 番目の数値で除算します。

```
mod(3, 2)
```

返される結果: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

2 つの数値を乗算した積を返します。

```
mul(<multiplicand1>, <multiplicand2>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | はい | 整数または浮動小数点数 | *multiplicand2* と乗算する値 |
| <*multiplicand2*> | はい | 整数または浮動小数点数 | *multiplicand1* と乗算する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*product-result*> | 整数または浮動小数点数 | 1 番目の数値と 2 番目の数値を乗算した積 |
||||

*例*

これらの例は、1 番目の数値と 2 番目の数値を乗算します。

```
mul(1, 2)
mul(1.5, 2)
```

次の結果を返します。

* 1 番目の例: `2`
* 2 番目の例: `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

複数の部分を持つアクションの出力の特定の部分に対する本文を返します。

```
multipartBody('<actionName>', <index>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 複数の部分を含む出力を持つアクションの名前 |
| <*index*> | はい | 整数 | 取得する部分のインデックス値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*body*> | string | 指定した部分の本文 |
||||

<a name="not"></a>

### <a name="not"></a>not

式が false かどうかを調べます。
式が false の場合は true を返し、true の場合は false を返します。

```json
not(<expression>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | はい | Boolean | 調べる式。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 式が false の場合は true を返します。 式が true の場合は false を返します。 |
||||

*例 1*

これらの例は、指定した式が false かどうかを調べます。

```json
not(false)
not(true)
```

次の結果を返します。

* 1 番目の例:式が false なので、関数は `true` を返します。
* 2 番目の例:式が true なので、関数は `false` を返します。

*例 2*

これらの例は、指定した式が false かどうかを調べます。

```json
not(equals(1, 2))
not(equals(1, 1))
```

次の結果を返します。

* 1 番目の例:式が false なので、関数は `true` を返します。
* 2 番目の例:式が true なので、関数は `false` を返します。

<a name="or"></a>

### <a name="or"></a>or

少なくとも 1 つの式が true かどうかを調べます。
少なくとも 1 つの式が true の場合は true を返し、すべての式が false の場合は false を返します。

```
or(<expression1>, <expression2>, ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | はい | Boolean | 調べる式 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false | Boolean | 少なくとも 1 つの式が true の場合は true を返します。 すべての式が false の場合は false を返します。 |
||||

*例 1*

これらの例は、少なくとも 1 つの式が true かどうかを調べます。

```json
or(true, false)
or(false, false)
```

次の結果を返します。

* 1 番目の例:少なくとも 1 つの式が true なので、関数は `true` を返します。
* 2 番目の例:両方の式が false なので、関数は `false` を返します。

*例 2*

これらの例は、少なくとも 1 つの式が true かどうかを調べます。

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

次の結果を返します。

* 1 番目の例:少なくとも 1 つの式が true なので、関数は `true` を返します。
* 2 番目の例:両方の式が false なので、関数は `false` を返します。

<a name="outputs"></a>

### <a name="outputs"></a>outputs

実行時のアクションの出力を返します。 ロジック アプリ デザイナーで `outputs()` に解決される `actionOutputs()` ではなく、この関数を使用してください。 どちらの関数も機能は同じですが、`outputs()` をお勧めします。

```
outputs('<actionName>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | はい | string | 取得するアクションの出力の名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | -----| ----------- |
| <*output*> | string | 指定したアクションからの出力 |
||||

*例*

この例は、Twitter アクション `Get user` からの出力を取得します。

```
outputs('Get_user')
```

返される結果:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

ワークフローの定義で記述されているパラメーターの値を返します。

```
parameters('<parameterName>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | はい | string | 値を取得するパラメーターの名前。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Any | 指定したパラメーターの値 |
||||

*例*

次のような JSON 値があるものとします。

```json
{
  "fullName": "Sophia Owen"
}
```

この例は、指定したパラメーターの値を取得します。

```
parameters('fullName')
```

返される結果: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

指定した範囲からランダムな整数を返します。開始側の端のみを含みます。

```
rand(<minValue>, <maxValue>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | はい | 整数 | 範囲に含まれる最小の整数 |
| <*maxValue*> | はい | 整数 | 関数が返すことのできる範囲内で最も大きい整数の次の整数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*random-result*> | 整数 | 指定した範囲から返されるランダムな整数 |
||||

*例*

この例は、指定した範囲 (最大値は除きます) からランダムな整数を取得します。

```
rand(1, 5)
```

結果として次のいずれかの整数が返ります: `1`、`2`、`3`、`4`

<a name="range"></a>

### <a name="range"></a>range

指定した整数から始まる整数の配列を返します。

```
range(<startIndex>, <count>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | はい | 整数 | 最初の項目として配列を開始する整数値 |
| <*count*> | はい | 整数 | 配列内の整数の数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | 指定したインデックスから始まる整数の配列 |
||||

*例*

この例は、指定したインデックスから始まり、指定された数の整数を含む、整数の配列を作成します。

```
range(1, 4)
```

返される結果: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

部分文字列を指定した文字列で置換し、結果の文字列を返します。 この関数は、大文字と小文字を区別します。

```
replace('<text>', '<oldText>', '<newText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 置換する部分文字列を含む文字列 |
| <*oldText*> | はい | string | 置換前の部分文字列 |
| <*newText*> | はい | string | 置換後の文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-text*> | string | 部分文字列を置換した後の更新された文字列 <p>部分文字列が見つからない場合は、元の文字列を返します。 |
||||

*例*

この例は、文字列 "the old string" で部分文字列 "old" を検索し、"old" を "new" に置き換えます。

```
replace('the old string', 'old', 'new')
```

返される結果: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

オブジェクトからプロパティを削除し、更新されたオブジェクトを返します。

```
removeProperty(<object>, '<property>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object*> | はい | Object | プロパティを削除する JSON オブジェクト |
| <*property*> | はい | string | 削除するプロパティの名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | 指定したプロパティを含まない更新された JSON オブジェクト |
||||

*例*

この例は、`"customerProfile"` オブジェクトから `"accountLocation"` プロパティを削除し、[JSON()](#json) 関数で JSON に変換した後、更新されたオブジェクトを返します。

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

オブジェクトのプロパティの値を設定し、更新されたオブジェクトを返します。
新しいプロパティを追加するには、この関数または [addProperty()](#addProperty) 関数を使うことができます。

```
setProperty(<object>, '<property>', <value>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*object*> | はい | Object | プロパティを設定する JSON オブジェクト |
| <*property*> | はい | string | 設定する既存または新規のプロパティの名前 |
| <*value*> | はい | Any | 指定したプロパティに設定する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | プロパティが設定された更新後の JSON オブジェクト |
||||

*例*

この例は、`"customerProfile"` オブジェクトの `"accountNumber"` プロパティを設定します。オブジェクトは、[JSON()](#json) 関数で JSON に変換されます。
この関数は、[guid()](#guid) 関数によって生成された値を代入し、更新された JSON オブジェクトを返します。

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

### <a name="skip"></a>skip

コレクションの先頭から項目を削除し、"*他のすべて*" の項目を返します。

```
skip([<collection>], <count>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | Array | 項目を削除するコレクション |
| <*count*> | はい | 整数 | 先頭から削除する項目の数を示す正の整数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | 指定した項目を削除した後の更新されたコレクション |
||||

*例*

この例は、指定した配列の先頭から 1 つの項目 (番号 0) を削除します。

```
skip(createArray(0, 1, 2, 3), 1)
```

そして、残りの項目を含む配列 `[1,2,3]` を返します。

<a name="split"></a>

### <a name="split"></a>split

元の文字列で指定された区切り文字に基づいて、コンマで区切られた部分文字列を含む配列を返します。

```
split('<text>', '<delimiter>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 元の文字列で指定された区切り記号に基づいて部分文字列に分割する文字列 |
| <*delimiter*> | はい | string | 区切り記号として使用する、元の文字列内の文字 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | コンマで区切られた、元の文字列からの部分文字列を含む配列 |
||||

*例*

この例では、区切り記号として指定した文字に基づいて指定された文字列からの部分文字列を含む配列を作成します。

```
split('a_b_c', '_')
```

返される配列の結果: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

タイムスタンプの日の開始日時を返します。

```
startOfDay('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 指定したタイムスタンプの日の午前 0 時を表す日時文字列 |
||||

*例*

この例は、次のタイムスタンプの日の開始を取得します。

```
startOfDay('2018-03-15T13:30:30Z')
```

返される結果: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

タイムスタンプの時刻の開始を返します。

```
startOfHour('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 指定したタイムスタンプの時刻の 0 分を表す日時文字列 |
||||

*例*

この例は、次のタイムスタンプの時刻の開始を取得します。

```
startOfHour('2018-03-15T13:30:30Z')
```

返される結果: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

タイムスタンプの月の開始を返します。

```
startOfMonth('<timestamp>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 指定したタイムスタンプの月の開始日の午前 0 時を表す文字列 |
||||

*例*

この例は、次のタイムスタンプの月の開始を返します。

```
startOfMonth('2018-03-15T13:30:30Z')
```

返される結果: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

文字列が特定の部分文字列で始まっているかどうかを調べます。
部分文字列が見つかった場合は true を返し、見つからない場合は false を返します。
この関数は、大文字と小文字を区別しません。

```
startsWith('<text>', '<searchText>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 調べる文字列。 |
| <*searchText*> | はい | string | 検索する開始文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| true または false  | Boolean | 先頭の部分文字列が見つかった場合は true を返します。 見つからなかった場合は false を返します。 |
||||

*例 1*

この例は、文字列 "hello world" が部分文字列 "hello" で始まっているかどうかを調べます。

```
startsWith('hello world', 'hello')
```

返される結果: `true`

*例 2*

この例は、文字列 "hello world" が部分文字列 "greetings" で始まっているかどうかを調べます。

```
startsWith('hello world', 'greetings')
```

返される結果: `false`

<a name="string"></a>

### <a name="string"></a>string

値の文字列バージョンを返します。

```
string(<value>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | Any | 変換する値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*string-value*> | string | 指定した値の文字列バージョン |
||||

*例 1*

この例は、次の数値の文字列バージョンを作成します。

```
string(10)
```

返される結果: `"10"`

*例 2*

この例は、指定した JSON オブジェクトの文字列を作成します。二重引用符 (") のエスケープ文字として、バックスラッシュ文字 (\\) を使います。

```
string( { "name": "Sophie Owen" } )
```

返される結果: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

1 番目の数値から 2 番目の数値を減算して、結果を返します。

```
sub(<minuend>, <subtrahend>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | はい | 整数または浮動小数点数 | *subtrahend* を引く数値 |
| <*subtrahend*> | はい | 整数または浮動小数点数 | *minuend* から引く数値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*result*> | 整数または浮動小数点数 | 1 番目の数値から 2 番目の数値を減算した結果 |
||||

*例*

この例は、1 番目の数値から 2 番目の数値を減算します。

```
sub(10.3, .3)
```

返される結果: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

文字列から、指定された位置またはインデックスから始まる文字を返します。
インデックス値は 0 から始まります。

```
substring('<text>', <startIndex>, <length>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 文字を取得する文字列 |
| <*startIndex*> | はい | 整数 | 開始位置またはインデックスの値として使用する 0 以上の正の数 |
| <*length*> | はい | 整数 | 取得する部分文字列の文字数を示す正の値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*substring-result*> | string | ソース文字列の指定したインデックス位置から始まる、指定した文字数を含む部分文字列 |
||||

*例*

この例は、指定した文字列のインデックス値 6 から始まる 5 文字を含む部分文字列を作成します。

```
substring('hello world', 6, 5)
```

返される結果: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

タイムスタンプから時間単位数を減算します。
[getPastTime](#getPastTime) もご覧ください。

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプを含む文字列。 |
| <*interval*> | はい | 整数 | 減算する指定した時間単位の数 |
| <*timeUnit*> | はい | string | *間隔*と共に使用する時間単位:"Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | タイムスタンプから指定した時間単位数を引いた値 |
||||

*例 1*

この例は、次のタイムスタンプから 1 日を減算します。

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

返される結果: `"2018-01-01T00:00:00:0000000Z"`

*例 2*

この例は、次のタイムスタンプから 1 日を減算します。

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

省略可能な "D" 形式を使用して返される結果: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

コレクションの先頭から項目を返します。

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | はい | 文字列、配列 | 項目を取得するコレクション |
| <*count*> | はい | 整数 | 先頭から取得する項目の数を示す正の整数 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*subset*> または [<*subset*>] | それぞれ文字列、配列 | 元のコレクションの先頭から取得された指定個数の項目を含む文字列または配列 |
||||

*例*

これらの例は、次のコレクションの先頭から指定した数の項目を取得します。

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

次の結果を返します。

* 1 番目の例: `"abc"`
* 2 番目の例: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

指定したタイムスタンプの `ticks` プロパティの値を返します。
"*ティック*" は 100 ナノ秒の間隔です。

```
ticks('<timestamp>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | はい | string | タイムスタンプの文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*ticks-number*> | 整数 | 指定したタイムスタンプからのティック数 |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

小文字の形式で文字列を返します。 文字列内の文字に小文字バージョンがない場合、その文字は返される文字列に変更されないまま残ります。

```
toLower('<text>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 小文字形式で返される文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | string | 元の文字列の小文字形式 |
||||

*例*

この例は、次の文字列を小文字に変換します。

```
toLower('Hello World')
```

返される結果: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

大文字の形式で文字列を返します。 文字列内の文字に大文字バージョンがない場合、その文字は返される文字列に変更されないまま残ります。

```
toUpper('<text>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 大文字形式で返される文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | string | 元の文字列の大文字形式 |
||||

*例*

この例は、次の文字列を大文字に変換します。

```
toUpper('Hello World')
```

返される結果: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>trigger

実行時のトリガーの出力を返すか、または式に割り当てることができる他の JSON の名前と値のペアの値を返します。

* トリガーの入力の内部では、この関数は前の実行からの出力を返します。

* トリガーの条件の内部では、この関数は現在の実行からの出力を返します。

既定では、この関数はトリガー オブジェクト全体を参照しますが、必要に応じて値を取得するプロパティを指定することができます。
この関数には短縮バージョンがあります。[triggerOutputs()](#triggerOutputs) および [triggerBody()](#triggerBody) をご覧ください。

```
trigger()
```

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*trigger-output*> | string | 実行時のトリガーからの出力 |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

実行時にトリガーの `body` 出力を返します。
`trigger().outputs.body` の短縮形です。
[trigger()](#trigger) をご覧ください。

```
triggerBody()
```

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | string | トリガーからの `body` 出力 |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

トリガーの *form-data* 出力または *form-encoded* 出力内のキー名と一致する値の配列を返します。

```
triggerFormDataMultiValues('<key>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*key*> | はい | string | 値を指定するキーの名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | 指定したキーと一致するすべての値で構成される配列 |
||||

*例*

この例は、RSS トリガーの form-data 出力または form-encoded 出力内の "feedUrl" キーの値から配列を作成します。

```
triggerFormDataMultiValues('feedUrl')
```

例の結果として返される配列: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

トリガーの *form-data* 出力または *form-encoded* 出力内のキー名と一致する単一の値の文字列を返します。
複数の一致が見つかった場合、関数はエラーをスローします。

```
triggerFormDataValue('<key>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*key*> | はい | string | 値を指定するキーの名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*key-value*> | string | 指定したキーの値 |
||||

*例*

この例は、RSS トリガーの form-data 出力または form-encoded 出力内の "feedUrl" キーの値から文字列を作成します。

```
triggerFormDataValue('feedUrl')
```

例の結果として返される文字列: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

複数の部分を持つトリガーの出力の特定の部分に対する本文を返します。

```
triggerMultipartBody(<index>)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*index*> | はい | 整数 | 取得する部分のインデックス値 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*body*> | string | トリガーの複数の部分からなる出力内の指定した部分の本文 |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

実行時のトリガーの出力を返すか、または他の JSON の名前と値のペアの値を返します。
`trigger().outputs` の短縮形です。
[trigger()](#trigger) をご覧ください。

```
triggerOutputs()
```

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*trigger-output*> | string | 実行時のトリガーからの出力  |
||||

<a name="trim"></a>

### <a name="trim"></a>trim

文字列から先頭と末尾の空白を削除し、更新された文字列を返します。

```
trim('<text>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*text*> | はい | string | 先頭と末尾に削除する空白を含む文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updatedText*> | string | 先頭または末尾の空白が削除された、元の文字列の更新バージョン |
||||

*例*

この例は、文字列 " Hello World  " から先頭と末尾の空白を削除します。

```
trim(' Hello World  ')
```

返される結果: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

指定した複数のコレクションの "*すべての*" 項目を含む 1 つのコレクションを返します。
この関数に渡されるいずれかのコレクションに含まれる項目は、結果にも含まれます。 1 つまたは複数の項目が同じ名前である場合は、その名前を持つ最後の項目が結果に含まれます。

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | はい | 配列またはオブジェクト、両方ともは不可 | "*すべての*" 項目を取得するコレクション |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | それぞれ、配列またはオブジェクト | 指定したコレクションのすべての項目を含むコレクション。重複はありません。 |
||||

*例*

この例は、以下のコレクションから "*すべての*" 項目を取得します。

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

返される結果: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

URL の安全でない文字がエスケープ文字に置き換えられた、文字列の URI (Uniform Resource Identifier) エンコード バージョンを返します。
[encodeUriComponent()](#encodeUriComponent) ではなく、この関数を使用してください。
どちらの関数も機能は同じですが、`uriComponent()` をお勧めします。

```
uriComponent('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | URI エンコード形式に変換する文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | string | エスケープ文字が使われている URI エンコード文字列 |
||||

*例*

この例は、次の文字列の URI エンコード バージョンを作成します。

```
uriComponent('https://contoso.com')
```

返される結果: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

URI (Uniform Resource Identifier) コンポーネントのバイナリ バージョンを返します。

```
uriComponentToBinary('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換する URI エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | string | URI エンコード文字列のバイナリ バージョン バイナリ コンテンツは base64 でエンコードされ、`$content` によって表されます。 |
||||

*例*

この例は、次の URI エンコード文字列のバイナリ バージョンを作成します。

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

返される結果:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

URI (Uniform Resource Identifier) エンコード文字列の文字列バージョンを返します。実質的に、URI エンコード文字列をデコードします。

```
uriComponentToString('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | デコードする URI エンコード文字列 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | string | URI エンコード文字列のデコード バージョン |
||||

*例*

この例は、次の URI エンコード文字列のデコードされた文字列バージョンを作成します。

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

返される結果: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

URI (Uniform Resource Identifier) の `host` 値を返します。

```
uriHost('<uri>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*uri*> | はい | string | `host` 値を取得する URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*host-value*> | string | 指定した URI の `host` 値 |
||||

*例*

この例は、次の URI の `host` 値を検索します。

```
uriHost('https://www.localhost.com:8080')
```

返される結果: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

URI (Uniform Resource Identifier) の `path` 値を返します。

```
uriPath('<uri>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*uri*> | はい | string | `path` 値を取得する URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*path-value*> | string | 指定した URI の `path` 値 `path` に値がない場合は、"/" 文字を返します。 |
||||

*例*

この例は、次の URI の `path` 値を検索します。

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

返される結果: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

URI (Uniform Resource Identifier) の `path` と `query` の値を返します。

```
uriPathAndQuery('<uri>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*uri*> | はい | string | `path` と `query` の値を取得する URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*path-query-value*> | string | 指定した URI の `path` と `query` の値。 `path` の値が指定されていない場合は、"/" 文字を返します。 |
||||

*例*

この例は、次の URI の `path` と `query` の値を検索します。

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

返される結果: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

URI (Uniform Resource Identifier) の `port` 値を返します。

```
uriPort('<uri>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*uri*> | はい | string | `port` 値を取得する URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*port-value*> | 整数 | 指定した URI の `port` 値 `port` の値が指定されていない場合は、プロトコルの既定のポートを返します。 |
||||

*例*

この例は、次の URI の `port` 値を返します。

```
uriPort('http://www.localhost:8080')
```

返される結果: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

URI (Uniform Resource Identifier) の `query` 値を返します。

```
uriQuery('<uri>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*uri*> | はい | string | `query` 値を取得する URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*query-value*> | string | 指定した URI の `query` 値 |
||||

*例*

この例は、次の URI の `query` 値を返します。

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

返される結果: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

URI (Uniform Resource Identifier) の `scheme` 値を返します。

```
uriScheme('<uri>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*uri*> | はい | string | `scheme` 値を取得する URI |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*scheme-value*> | string | 指定した URI の `scheme` 値 |
||||

*例*

この例は、次の URI の `scheme` 値を返します。

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

返される結果: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

現在のタイムスタンプを返します。

```
utcNow('<format>')
```

必要に応じて、<*format*> パラメーターで異なる形式を指定できます。


| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*format*> | いいえ | string | [単一の書式指定子](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式パターン](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 timestamp の既定の形式は ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK) です。これは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠し、タイム ゾーン情報を保持します。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | string | 現在の日付と時刻 |
||||

*例 1*

現在の日時が 2018 年 4 月 15 日午後 1 時 0 分 0 秒であるものとします。
この例は、現在のタイムスタンプを取得します。

```
utcNow()
```

返される結果: `"2018-04-15T13:00:00.0000000Z"`

*例 2*

現在の日時が 2018 年 4 月 15 日午後 1 時 0 分 0 秒であるものとします。
この例は、オプションの "D" 形式を使って現在のタイムスタンプを取得します。

```
utcNow('D')
```

返される結果: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variables

指定した変数の値を返します。

```
variables('<variableName>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | はい | string | 値を取得する変数の名前 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*variable-value*> | Any | 指定した変数の値 |
||||

*例*

"numItems" 変数の現在の値が 20 であるものとします。
この例は、この変数の整数値を取得します。

```
variables('numItems')
```

返される結果: `20`

<a name="workflow"></a>

### <a name="workflow"></a>workflow

実行時にワークフロー自体に関するすべての詳細を返します。

```
workflow().<property>
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*property*> | いいえ | string | 値を取得するワークフロー プロパティの名前 <p>ワークフロー オブジェクトのプロパティ: **name**、**type**、**id**、**location**、**run**。 **run** プロパティの値は、次のプロパティを持つオブジェクトでもあります: **name**、**type**、**id**。 |
|||||

*例*

この例は、ワークフローの現在の実行の名前を返します。

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>Xml

JSON オブジェクトを含む文字列の XML バージョンを返します。

```
xml('<value>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*value*> | はい | string | 変換する JSON オブジェクトを含む文字列 <p>JSON オブジェクトのルート プロパティは 1 つに限る必要があり、配列にはできません。 <br>二重引用符 (") のエスケープ文字としてはバックスラッシュ文字 (\\) を使います。 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | 指定した文字列または JSON オブジェクトのエンコードされた XML |
||||

*例 1*

この例は、JSON オブジェクトを含む次の文字列の XML バージョンを作成します。

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

返される結果の XML:

```xml
<name>Sophia Owen</name>
```

*例 2*

次のような JSON オブジェクトがあるものとします。

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

この例は、この JSON オブジェクトを含む文字列の XML を作成します。

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

返される結果の XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

XML で XPath (XML Path Language) 式と一致するノードまたは値を調べて、一致するノードまたは値を返します。 XPath 式または単に "XPath" は、XML コンテンツ内のノードまたは計算値を選択できるように、XML ドキュメント構造内の移動を補助します。

```
xpath('<xml>', '<xpath>')
```

| パラメーター | 必須 | Type | 説明 |
| --------- | -------- | ---- | ----------- |
| <*xml*> | はい | Any | XPath 式の値に一致するノードまたは値を検索する XML 文字列 |
| <*xpath*> | はい | Any | 一致する XML ノードまたは値の検索に使用する XPath 式 |
|||||

| 戻り値 | Type | 説明 |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | 1 つのノードだけが指定した XPath 式と一致するときの XML ノード |
| <*value*> | Any | 1 つの値だけが指定した XPath 式と一致するときの XML ノードの値 |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>または </br>[<*value1*>, <*value2*>, ...] | Array | 指定した XPath 式と一致する XML ノードまたは値の配列 |
||||

*例 1*

この例は、指定した引数内の `<name></name>` ノードと一致するノードを検索し、一致するノードの値の配列を返します。

`xpath(xml(parameters('items')), '/produce/item/name')`

引数は次のとおりです。

* この XML を含んでいる "items" の文字列:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  この例は、[parameters()](#parameters) 関数を使って "items" 引数から XML 文字列を取得しますが、[xml()](#xml) 関数を使って文字列を XML 形式に変換する必要もあります。

* この XPath 式は、文字列として渡されます。

  `"/produce/item/name"`

次に示すのは、`<name></name` と一致するノードを含む結果の配列です。

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*例 2*

例 1 に続き、この例では `<count></count>` ノードと一致するノードを検索し、`sum()` 関数でそれらのノードの値を追加します。

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

返される結果: `30`

*例 3*

この例の式はどちらも、名前空間を含む XML が格納されている指定した引数で、`<location></location>` ノードと一致するノードを検索します。 式では、二重引用符 (") のエスケープ文字としてバックスラッシュ文字 (\\) を使います。

* *式 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *式 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

引数は次のとおりです。

* XML ドキュメントの名前空間 `xmlns="http://contoso.com"` を含む次の XML:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* どちらかの XPath 式:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

`<location></location>` ノードと一致する結果のノード:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*例 4*

例 3 に続き、この例では `<location></location>` ノードの値を検索します。

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

返される結果: `"Paris"`

## <a name="next-steps"></a>次の手順

[ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)について学習してください
