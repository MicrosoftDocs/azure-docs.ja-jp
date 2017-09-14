---
title: "Azure Search の Lucene クエリ例 | Microsoft Docs"
description: "あいまい検索、近接検索、用語ブースト、正規表現検索、ワイルドカード検索の Lucene クエリ構文。"
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: Lucene query analyzer syntax
ms.assetid: 147f360d-a5ce-4d7b-a909-c8b65bfb748c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: liamca
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: bfd044f333087d8e3e9526820196be6eaec2f18f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/22/2017

---

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Azure Search でクエリを作成するための Lucene クエリ構文例
Azure Search のクエリを構築するときは、既定の[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)または代替の [Azure Search の Lucene Query Parser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) を使用できます。 Lucene Query Parser は、フィールド スコープ クエリ、あいまい検索、近接検索、用語ブースト、正規表現検索など、複雑なクエリ構文に対応しています。

この記事では、完全な構文を使用するときに、利用できるクエリ操作をデモンストレーションする例について詳しく説明します。

## <a name="viewing-the-examples-in-jsfiddle"></a>JSFiddle の例を表示する

この記事のすべての例は、[JSFiddle](https://jsfiddle.net) に事前に読み込んでおいた検索インデックスに対して実行される実行可能クエリです。JSFiddle は、スクリプトと HTML をテストするためのオンライン コード エディターです。 

クエリを実行するには、クエリの例の URL を右クリックし、JSFiddle を新しいブラウザー ウィンドウで開きます。

> [!NOTE]
> 次の例では、 [City of New York OpenData](https://nycopendata.socrata.com/) イニシアティブが提供するデータセットのジョブで構成されている検索インデックスを活用します。 このデータが最新のものであるとか、完全であるとはお考えにならないでください。 このインデックスは、Microsoft が提供するサンドボックス サービスにあります。 Azure サブスクリプションや Azure Search がなくてもこれらのクエリを試すことができます。
>


## <a name="how-to-invoke-full-lucene-parsing"></a>完全な Lucene の解析を呼び出す方法

この記事のすべての例で、Lucene Query Parser によって処理される完全な構文を示す、**queryType=full** 検索パラメーターを指定します。 

**例 1** -- 次のクリエ スニペットを右クリックし、新しいブラウザー ページで開きます。新しいページが JSFiddle を読み込み、クエリを実行します。

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

新しいブラウザー ウィンドウで、JavaScript のソースと HTML 出力が横並びで表示されます。 スクリプトは、(リンクで示されるように、スニペットだけではなく) 完全なクエリを参照します。 完全なクエリは、それぞれの例の URL で示されます。 

このクエリは、ニューヨーク市のジョブ インデックス (サンドボックス サービスに読み込まれている nycjobs) からドキュメントを返します。 簡潔にするため、このクエリでは肩書きのみが返されます。 完全な基になるクエリは、次のとおりです。

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**searchFields** パラメーターは、検索範囲をビジネス タイトル フィールドだけに制限します。 **queryType** は **full** に設定されています。これによって、このクエリで Lucene Query Parser を使用するように Azure Search に指示しています。

> [!NOTE]
> クエリ処理の背景については、「[Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。 検索パラメーターの詳細については、「[Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)」 (検索ドキュメント (Azure Search サービスの REST API)) を参照してください。
>

### <a name="fielded-query-operation"></a>フィールド クエリ操作
この記事の例を変更するには、**fieldname:searchterm** 構造を指定し、フィールド クエリ操作を定義します。フィールドは 1 つの単語であり、検索語句も 1 つの単語または語句です。ブール演算子を利用することもあります。 たとえば、次のようになります。

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

複数の文字列を 1 つのエンティティとして評価するのであれば、複数の文字列を引用符で囲ってください。この例では、場所フィールドで 2 つの異なる都市を検索しています。 また、NOT や AND のように、演算子は大文字表記になります。

**fieldname:searchterm** に指定されたフィールドは検索可能フィールドである必要があります。 フィールド定義におけるインデックス属性の利用方法に関する詳細については、「 [Create Index (Azure Search Service REST API) (インデックスの作成 (Azure Search サービス REST API))](https://docs.microsoft.com/rest/api/searchservice/create-index) 」を参照してください。

**例 2** -- 次のクエリ スニペットを右クリックします。このクエリは、junior ではなく、senior という表現を含む肩書きを検索します。

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>あいまい検索の例
あいまい検索では、似たような構造の言い回しの一致が検索されます。 [Lucene ドキュメント](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)によると、あいまい検索は [Damerau-Levenshtein Distance](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance) を基盤としています。

あいまい検索を実行するには、1 つの言葉の終わりにチルダ記号 "~" を付けます。任意で編集距離を指定するパラメーターとして 0 ～ 2 の値を指定します。 たとえば、"blue~" または "blue~1" は blue、blues、glue を返します。

**例 3** -- 次のクエリ スニペットを右クリックします。 このクエリは、associate という言葉を含む仕事を検索します (スペルが間違っています)。

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> あいまいクエリは[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)されません。これは、ステミングやレンマ化を想定している場合は、驚くべきことかもしれません。 語彙の分析は、完全な用語でのみ実行されます (用語クエリまたは語句クエリ)。 不完全な語句 (プレフィックス クエリ、ワイルドカードのクエリ、正規表現のクエリ、あいまいクエリ) でのクエリの種類は、解析ステージをバイパスして、クエリ ツリーに直接追加されます。 不完全なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="proximity-search-example"></a>近接検索の例
近接検索は、ある文書で互いに近くにある言葉を検索します。 言葉の終わりにチルダ記号 "~" を挿入し、近接境界となる語数を続けます。 たとえば、"hotel airport"~5 と指定すると、ある文書で hotel という言葉と airport という言葉が互いに 5 語以内にある箇所が検索されます。

**例 4** -- クエリを右クリックします。 "senior analyst" という言い回しを含む仕事を検索します。間に 1 語だけ含まれます。

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**例 5** -- "senior analyst" の間の言葉を削除してもう一度試します。

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>用語ブーストの例
用語ブーストでは、指定用語を含む文書に含まない文書より高い順位が設定されます (ブーストされます)。 これはスコアリング プロファイルとは違います。スコアリング プロファイルは、特定の用語ではなく、特定のフィールドをブーストします。 次の例はその違いを示しています。

musicstoreindex の例の **genre** など、特定のフィールドの一致がブーストされるスコアリング プロファイルについて考えてみましょう。 用語ブーストでは、特定の用語に他の用語より高い順位を与えます。 たとえば、"rock^2 electronic" と指定した場合、 **genre** フィールドに検索語句を含む文書に、インデックスの他の検索可能フィールドより高い順位が与えられます。 さらに、用語のブースト値 (2) により、"rock" という検索用語を含む文書に、"electronic" というもう 1 つの用語よりも高い順位が与えられます。

用語をブーストするには、キャレット記号 "^" とブースト係数 (数字) を検索語句の終わりに付けます。 ブースト係数が高ければ高いほど、その語句の関連性が他の検索語句に比べて大きくなります。 既定のブースト係数は 1 です。 ブースト係数は整数にする必要がありますが、1 に満たない (0.2 など) 数字にすることができます。

**例 6** -- クエリを右クリックします。 "computer analyst" という言葉を含む仕事を検索します。computer と analyst の両方を含む結果は出ませんが、アナリストの仕事が結果の上位に表示されています。

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**例 7** -- もう一度試します。今度は、両方の言葉が存在しない場合、computer という言葉に analyst という言葉より高い優先順位を与えます。

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>正規表現の例
正規表現検索では、スラッシュ "/" の間のコンテンツに基づいて一致が検索されます。[RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html) クラスに詳細があります。

**例 8** -- クエリを右クリックします。 Senior または Junior という言葉を含む仕事を検索します。

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

この例の URL はページで正しく表示されません。 回避策として、下の URL をコピーし、ブラウザーの URL アドレスに貼り付けます。`http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>ワイルドカード検索の例
一般的に認められている構文を利用できます。複数の場合は (\*) を、単数の場合は (?) をワイルドカード文字として検索できます。 Lucene Query Parser では、これらの文字を語句ではなく 1 つの用語に利用することにご注意ください。

**例 9** -- クエリを右クリックします。 'prog' という接頭辞を含む仕事を検索します。プログラミングやプログラマーなどの用語が肩書きに含まれる仕事が検索されます。

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

検索の最初の文字として * または ? を使用することはできません。

## <a name="next-steps"></a>次のステップ
自分のコードに Lucene Query Parser を指定してみてください。 次のリンクでは、.NET と REST API の両方の検索クエリを設定する方法について説明しています。 これらのリンクでは、既定の単純な構文を使用しています。**queryType** を指定するには、この記事で学習したことを応用する必要があります。

* [.NET SDK を使用した Azure Search インデックスの照会](search-query-dotnet.md)
* [REST API を使用した Azure Search インデックスの照会](search-query-rest-api.md)

## <a name="see-also"></a>関連項目

 [Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)
