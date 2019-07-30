---
title: Azure Monitor ログ クエリ内の文字列を操作する | Microsoft Docs
description: この記事では、Azure Monitor ログ クエリの文字列に対して、編集、比較、検索、およびさまざまなその他の操作を実行する方法について説明します。
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: f53d3bd64b4f837fe29baa338cd338158d59d95d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466948"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリ内の文字列を操作する


> [!NOTE]
> このチュートリアルを完了する前に、「[Azure Monitor ログ分析の使用を開始する](get-started-portal.md)」と、[Azure Monitor ログ クエリの使用の開始](get-started-queries.md)に関するチュートリアルを完了しておく必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

この記事では、文字列での編集、比較、検索、およびその他さまざまな操作を実行する方法について説明します。

文字列内の文字にはそれぞれ、その場所に従ってインデックス番号が付いています。 最初の文字はインデックス 0、次の文字は 1、以降、同様に続きます。 以降のセクションで示すように、さまざまな文字列関数でインデックス番号が使用されています。 特定のデータ ソースを使用せずに文字列操作を示すために、次の例の多くで **print** コマンドが使用されています。


## <a name="strings-and-escaping-them"></a>文字列とそのエスケープ
文字列の値は、一重引用符または二重引用符のいずれかで囲まれています。 バックスラッシュ (\) はエスケープ文字で、その次の文字をエスケープします。たとえば、\t はタブを、\n は改行を、そして \" は引用符自体を表します。

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

"\\" がエスケープ文字として機能しないようにするには、文字列に "\@" をプレフィックスとして追加します。

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>文字列の比較

演算子       |説明                         |大文字と小文字の区別|例 (`true` になる)
---------------|------------------------------------|--------------|-----------------------
`==`           |等しい                              |はい           |`"aBc" == "aBc"`
`!=`           |等しくない                          |はい           |`"abc" != "ABC"`
`=~`           |等しい                              |いいえ            |`"abc" =~ "ABC"`
`!~`           |等しくない                          |いいえ            |`"aBc" !~ "xyz"`
`has`          |右辺が左辺の完全な用語として含まれる |いいえ|`"North America" has "america"`
`!has`         |右辺が左辺の完全な用語として含まれない       |いいえ            |`"North America" !has "amer"` 
`has_cs`       |右辺が左辺の完全な用語として含まれる |はい|`"North America" has_cs "America"`
`!has_cs`      |右辺が左辺の完全な用語として含まれない       |はい            |`"North America" !has_cs "amer"` 
`hasprefix`    |右辺が左辺の用語のプレフィックスとして含まれる         |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`   |右辺が左辺の用語のプレフィックスとして含まれない     |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右辺が左辺の用語のプレフィックスとして含まれる         |はい            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右辺が左辺の用語のプレフィックスとして含まれない     |はい            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右辺が左辺の用語のサフィックスとして含まれる         |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`   |右辺が左辺の用語のサフィックスに含まれない     |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右辺が左辺の用語のサフィックスとして含まれる         |はい            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右辺が左辺の用語のサフィックスに含まれない     |はい            |`"North America" !hassuffix_cs "icA"`
`contains`     |右辺が左辺のサブシーケンスとして出現する  |いいえ            |`"FabriKam" contains "BRik"`
`!contains`    |右辺が左辺のサブシーケンスとして出現しない           |いいえ            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右辺が左辺のサブシーケンスとして出現する  |はい           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |右辺が左辺のサブシーケンスとして出現しない           |はい           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右辺が左辺の先頭のサブシーケンスである|いいえ            |`"Fabrikam" startswith "fab"`
`!startswith`  |右辺が左辺の先頭のサブシーケンスでない|いいえ        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右辺が左辺の先頭のサブシーケンスである|はい            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右辺が左辺の先頭のサブシーケンスでない|はい        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右辺が左辺の末尾のサブシーケンスである|いいえ             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右辺が左辺の末尾のサブシーケンスでない|いいえ         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右辺が左辺の末尾のサブシーケンスである|はい             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右辺が左辺の末尾のサブシーケンスでない|はい         |`"Fabrikam" !endswith "brik"`
`matches regex`|左辺には右辺の一致が含まれている        |はい           |`"Fabrikam" matches regex "b.*k"`
`in`           |要素のいずれかに等しい       |はい           |`"abc" in ("123", "345", "abc")`
`!in`          |要素のいずれとも等しくない   |はい           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

文字列内の部分文字列の出現回数をカウントします。 プレーン文字列と照合できます。または正規表現を使用できます。 プレーン文字列の一致は重複する可能性があります。正規表現の一致は重複しません。

### <a name="syntax"></a>構文
```
countof(text, search [, kind])
```

### <a name="arguments"></a>引数:
- `text` - 入力文字列 
- `search` - text 内で一致するプレーン文字列または正規表現。
- `kind` - _normal_ | _regex_ (既定: normal)。

### <a name="returns"></a>戻り値

コンテナー内で検索文字列が一致する回数。 プレーン文字列の一致は重複する可能性があります。正規表現の一致は重複しません。

### <a name="examples"></a>例

#### <a name="plain-string-matches"></a>プレーン文字列の一致

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>正規表現の一致

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

指定された文字列から正規表現との一致を取得します。 必要に応じて、抽出された部分文字列を、指定された型に変換することもできます。

### <a name="syntax"></a>構文

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>引数

- `regex` - 正規表現。
- `captureGroup` - 抽出するキャプチャ グループを示す正の整数の定数。 0 は一致全体、1 は正規表現の最初のかっこで囲まれた部分と一致した値、2 以上は後続のかっこを示します。
- `text` - 検索する文字列。
- `typeLiteral` - オプションの type リテラル (例: typeof(long))。 指定した場合、抽出された部分文字列はこの型に変換されます。

### <a name="returns"></a>戻り値
指定されたキャプチャ グループ captureGroup に対応する部分文字列。オプションで、typeLiteral に変換できます。
一致がないか、型変換が失敗した場合は、null 値を返します。

### <a name="examples"></a>例

次の例では、ハートビート レコードから *ComputerIP* の最終オクテットを抽出します。
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

次の例では、最終オクテットを抽出して *real* 型 (数値) にキャストし、次の IP 値を計算します
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

次の例では、文字列 *Trace* で "Duration" の定義を検索します。 一致は *real* にキャストされた後、"*Duration を timespan 型にキャストする*" 時間定数 (1 s) で乗算されます。
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty、isnotempty、notempty

- *isempty* は、引数が空の文字列または null 値の場合に true を返します (*isnull* も参照)。
- *isnotempty* は、引数が空の文字列または null 値でない場合に true を返します (*isnotnull* も参照)。 別名: *notempty*。

### <a name="syntax"></a>構文

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>例

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

URL をパーツ (プロトコル、ホスト、ポートなど) に分割し、そのパーツが文字列として含まれるディクショナリ オブジェクトを返します。

### <a name="syntax"></a>構文

```
parseurl(urlstring)
```

### <a name="examples"></a>例

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

結果は次のとおりです。
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>replace

正規表現のすべての一致を別の文字列で置き換えます。 

### <a name="syntax"></a>構文

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>引数

- `regex` - 照合する正規表現。 複数のキャプチャ グループをかっこ内に含めることができます。
- `rewrite` - 照合する正規表現と一致したものすべてが置き換えられた後の正規表現。 完全一致を参照する場合は \0、最初のキャプチャ グループの場合は \1、後続のキャプチャ グループの場合は \2 などを使用します。
- `input_text` - 検索場所を示す入力文字列。

### <a name="returns"></a>戻り値
regex とのすべての一致が rewrite の評価で置き換えられた後のテキスト。 一致が重複することはありません。

### <a name="examples"></a>例

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

結果は次のとおりです。

アクティビティ                                        |replaced
------------------------------------------------|----------------------------------------------------------
4663 - オブジェクトへのアクセスが試行されました  |アクティビティ ID 4663: オブジェクトへのアクセスが試行されました。


## <a name="split"></a>split

指定された文字列を、指定された区切り記号に従って分割し、結果の部分文字列の配列を返します。

### <a name="syntax"></a>構文
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>引数:

- `source` - 指定された区切り記号に従って分割する文字列。
- `delimiter` - ソース文字列の分割に使用される区切り記号。
- `requestedIndex` - 0 から始まるインデックス (省略可能)。 指定した場合、返される文字列配列にはその項目のみが含まれます (存在する場合)。


### <a name="examples"></a>例

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

文字列引数を連結します (1 から 16 の引数をサポート)。

### <a name="syntax"></a>構文
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>例
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

文字列の長さを返します。

### <a name="syntax"></a>構文
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>例
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

指定されたソース文字列から、指定されたインデックス位置以降にある部分文字列を抽出します。 必要に応じて、要求する部分文字列の長さを指定できます。

### <a name="syntax"></a>構文
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>引数:

- `source` - 部分文字列の取得元となるソース文字列。
- `startingIndex` - 要求する部分文字列の、0 から始まる開始文字位置。
- `length` - 要求する部分文字列に対して返される文字数の指定に使用できる省略可能なパラメーター。

### <a name="examples"></a>例
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower、toupper

指定された文字列を、すべて小文字またはすべて大文字に変換します。

### <a name="syntax"></a>構文
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>例
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>次の手順
高度なチュートリアルに進みます。
* [集計関数](aggregations.md)
* [高度な集計](advanced-aggregations.md)
* [グラフとダイアグラム](charts.md)
* [JSON とデータ構造の操作](json-data-structures.md)
* [詳細クエリ書き込み](advanced-query-writing.md)
* [結合 - クロス分析](joins.md)
