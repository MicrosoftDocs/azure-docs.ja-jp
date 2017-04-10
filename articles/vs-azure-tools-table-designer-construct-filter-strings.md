---
title: "テーブル デザイナーのフィルター文字列の作成 | Microsoft Docs"
description: "テーブル デザイナー用のフィルター文字列の作成"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 98b06b14ca7897cce884f6d80f998661cacb7ea4


---
# <a name="constructing-filter-strings-for-the-table-designer"></a>テーブル デザイナー用のフィルター文字列の作成
## <a name="overview"></a>Overview
Visual Studio **テーブル デザイナー**に表示される Azure テーブルのデータをフィルター処理するには、フィルター文字列を作成してフィルター フィールドに入力します。 フィルター文字列の構文は、WCF Data Services で定義されており、SQL の WHERE 句に似ています。ただし、文字列は HTTP 要求を介して Table サービスに送信されます。 必要なエンコード処理は**テーブル デザイナー**で自動的に行われます。したがって、目的のプロパティ値を条件としてフィルター処理するときに必要なことは、フィルター フィールドにプロパティ名、比較演算子、条件値、ブール演算子を入力するだけです (ブール演算子は省略可能)。 ストレージ サービスの REST API ([リファレンス](http://go.microsoft.com/fwlink/p/?LinkId=400447)をご覧ください) でテーブルを照会するための URL を作成する場合とは異なり、$filter クエリ オプションを含める必要はありません。

WCF Data Services は、 [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData) に基づいています。 フィルター システム クエリ オプション (**$filter**) の詳細については、 [OData URI 規則仕様](http://go.microsoft.com/fwlink/p/?LinkId=214806)に関するページを参照してください。

## <a name="comparison-operators"></a>比較演算子
次の論理演算子は、全種類のプロパティでサポートされます。

| 論理演算子 | Description | フィルター文字列の例 |
| --- | --- | --- |
| eq |等しい |City eq 'Redmond' |
| gt |より大きい |Price gt 20 |
| ge |以上 |Price ge 10 |
| lt |より小さい |Price lt 20 |
| le |以下 |Price le 100 |
| ne |等しくない |City ne 'London' |
| と |と |Price le 200 and Price gt 3.5 |
| または |または |Price le 3.5 or Price gt 200 |
| not |not |not isAvailable |

フィルター文字列を作成するときに重要となる規則は次のとおりです。

* プロパティを値と比較するには、論理演算子を使用します。 プロパティを動的な値と比較することはできません。式の 1 つの辺は定数である必要があります。
* フィルター文字列のすべての要素は大文字と小文字が区別されます。
* フィルターで有効な結果を得るためには、定数値をプロパティと同じデータ型にする必要があります。 サポートされているプロパティ型の詳細については、 [Table サービス データ モデル](http://go.microsoft.com/fwlink/p/?LinkId=400448)に関するページを参照してください。

## <a name="filtering-on-string-properties"></a>文字列プロパティのフィルター処理
文字列のプロパティを条件としてフィルター処理を行うには、文字列定数を単一引用符で囲みます。

次の例では、**PartitionKey** プロパティと **RowKey** プロパティにフィルターを適用しています。キー以外のプロパティをフィルター文字列に追加することもできます。

    PartitionKey eq 'Partition1' and RowKey eq '00001'

各フィルター式をかっこで囲むことができます。ただし、これは必須ではありません。

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

ワイルドカードによるクエリは、Table サービスでも、テーブル デザイナーでもサポートされていません。 ただし、目的のプレフィックスに対して比較演算子を使用することで、プレフィックス一致を実行できます。 次の例は、文字 'A' で始まる LastName プロパティを持つエンティティを返します。

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>数値プロパティのフィルター処理
整数または浮動小数点数を条件としてフィルター処理を行うには、引用符なしで数値を指定します。

次の例は、値が 30 より大きい Age プロパティを持つすべてのエンティティを返します。

    Age gt 30

次の例は、値が 100.25 以下の AmountDue プロパティを持つすべてのエンティティを返します。

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>ブール型プロパティのフィルター処理
ブール値を条件としてフィルター処理を行うには、引用符なしで **true** または **false** を指定します。

次の例は、IsActive プロパティが **true**に設定されているすべてのエンティティを返します。

    IsActive eq true

このフィルター式は、論理演算子なしで記述することもできます。 次の例でも、Table サービスは IsActive が **true**であるすべてのエンティティを返します。

    IsActive

IsActive が false であるすべてのエンティティを返すには、not 演算子を使用します。

    not IsActive

## <a name="filtering-on-datetime-properties"></a>DateTime プロパティのフィルター処理
DateTime 値を条件としてフィルター処理を行うには、**datetime** キーワードに続けて、単一引用符で囲んだ日付/時刻の定数を指定します。 日付/時刻の定数は、結合 UTC 形式にする必要があります。詳しくは、「[DateTime プロパティ値の書式設定](http://go.microsoft.com/fwlink/p/?LinkId=400449)」をご覧ください。

次の例は、CustomerSince プロパティが 2008 年 7 月 10 日と等しいエンティティを返します。

    CustomerSince eq datetime'2008-07-10T00:00:00Z'



<!--HONumber=Nov16_HO3-->


