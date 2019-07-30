---
title: Azure Monitor ログでの検索クエリ | Microsoft Docs
description: この記事では、Azure Monitor ログ クエリで検索の使用を開始するためのチュートリアルを提供します。
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
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: b118740f3a57e168c5dfb071c199bcf424bd5113
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295558"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Azure Monitor ログでの検索クエリ
Azure Monitor ログ クエリは、テーブル名または search コマンドで始めることができます。 このチュートリアルでは、検索ベースのクエリについて説明します。 それぞれの方法に利点があります。

テーブル ベースのクエリは、クエリの範囲を設定することから始まるので、検索クエリよりも効率的になる傾向があります。 検索クエリはあまり構造化されていないため、複数の列やテーブル全体で特定の値を検索するときに適しています。 **search** を使用すると、指定されたテーブルまたはすべてのテーブルのすべての列をスキャンし、指定した値を見つけることができます。 処理されるデータの量は膨大になる可能性があります。そのため、このようなクエリの処理には時間がかかり、非常に多数の結果セットが返される可能性があります。

## <a name="search-a-term"></a>用語を検索する
通常、**search** コマンドは特定の用語を検索するために使用されます。 次の例では、すべてのテーブルのすべての列がスキャンされ、"エラー" という用語が検索されます。

```Kusto
search "error"
| take 100
```

使いやすいクエリですが、上記のような範囲が指定されていないクエリは効率的ではなく、無関係の結果が多数返される可能性があります。 関連するテーブル内、さらには列を指定して検索することをお勧めします。

### <a name="table-scoping"></a>テーブルの範囲指定
特定のテーブルの用語を検索するには、**search** 演算子の直後に `in (table-name)` を追加します。

```Kusto
search in (Event) "error"
| take 100
```

また、複数のテーブルの場合は次のように指定します。
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>テーブルと列の範囲指定
既定で、**search** ではデータ セット内のすべての列が評価されます。 特定の列のみを検索するには、次の構文を使用します。

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> `:` ではなく `==` を使用すると、大文字と小文字の区別を含め、*Source* 列の値が "error" と完全一致するレコードが結果に含まれます。 ':' を使用すると、*Source* の値が "error code 404" や "Error" などのレコードが含まれます。

## <a name="case-sensitivity"></a>大文字と小文字の区別
既定で、用語の検索では大文字と小文字が区別されないため、"dns" を検索すると、"DNS"、"dns"、"Dns" などの結果が返されます。 大文字と小文字を区別して検索するには、`kind` オプションを使用します。

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>ワイルド カードを使用する
**search** コマンドは、用語の先頭、末尾、または中間のワイルドカードをサポートします。

"win" で始まる用語を検索するには:
```Kusto
search in (Event) "win*"
| take 100
```

".com" で終わる用語を検索するには:
```Kusto
search in (Event) "*.com"
| take 100
```

"www" を含む用語を検索するには:
```Kusto
search in (Event) "*www*"
| take 100
```

"corp" で始まり、".com" で終わる用語 ("corp.mydomain.com" など) を検索するには:

```Kusto
search in (Event) "corp*.com"
| take 100
```

また、ワイルドカード (`search in (Event) *`) を使用するだけでテーブル内のすべてのレコードを取得することもできますが、それは `Event` と記述する場合と同じです。

> [!TIP]
> `search *` を使用してすべてのテーブルからすべての列を取得できますが、常に特定のテーブルにクエリを適用することをお勧めします。 範囲が指定されていないクエリの処理には時間がかかり、多すぎる結果が返される可能性があります。

## <a name="add-and--or-to-search-queries"></a>検索クエリに *and* / *or* を追加する
複数の用語を含むレコードを検索するには、**and** を使用します。

```Kusto
search in (Event) "error" and "register"
| take 100
```

複数の用語のうち少なくとも 1 つを含むレコードを取得するには、**or** を使用します。

```Kusto
search in (Event) "error" or "register"
| take 100
```

複数の検索条件がある場合は、かっこ使用して 1 つのクエリにまとめることができます。

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

この例の結果は、用語 "error" を含み、かつ "register" を含むか、"marshal" から始まるレコードです。

## <a name="pipe-search-queries"></a>検索クエリのパイプ処理
他のコマンドと同様に **search** をパイプ処理して、検索結果のフィルター処理、並べ替え、および集計を行うことができます。 たとえば、"win" を含む *Event* レコードの数を取得するには、次のように指定します。

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>次の手順

- その他のチュートリアルについては、[Kusto クエリ言語サイト](/azure/kusto/query/)を参照してください。
