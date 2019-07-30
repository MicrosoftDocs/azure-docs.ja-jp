---
title: 検索結果を操作する方法 - Azure Search
description: Azure Search において、検索結果の構成と並べ替え、ドキュメント数の取得、および検索結果へのコンテンツ ナビゲーションの追加を行います。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb86a75be464cb78a16170626bc96778d43bb8b6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67974620"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Azure Search での検索結果の操作方法
この記事では、検索結果ページの標準的な要素である合計数、ドキュメント取得、並べ替え順序、およびナビゲーションなどを実装する方法のガイダンスを提供します。 データまたは情報を検索結果に表示するためのページ関連オプションは、Azure Search サービスに送信される [検索ドキュメント](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)の要求によって指定します。 

REST API では、要求には GET コマンド、パス、要求内容をサービスに伝えるクエリ パラメーター、および応答の作成方法が含まれます。 .NET SDK では、同等の API は[DocumentSearchResult クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)です。

複数のコード サンプルに Web フロントエンド インターフェイスが含まれており、次でも見つけることができます: [ニューヨーク市のジョブ デモ アプリ](https://azjobsdemo.azurewebsites.net/)および [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。

> [!NOTE]
> 有効な要求には、サービス URL とパスや HTTP 動詞、`api-version` などがあります。 簡潔にまとめ、改ページに関連する構文だけに焦点を当てられるように例の記載を省きました。 要求の構文について詳しくは、[Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice) に関する記事をご覧ください。
>

## <a name="total-hits-and-page-counts"></a>合計ヒット数とページ数

クエリから返される検索結果の合計数を表示し、それらの結果を一定の数ごとにページにまとめて返すことは、ほぼすべての検索ページでの基本機能です。

![][1]

Azure Search では、`$count`、`$top`、および `$skip` のパラメーターを使用すると、これらの値が返されます。 次の例では、"online-catalog" というインデックス上での合計ヒット数のサンプル要求が `@odata.count` として返されていることがわかります。

    GET /indexes/online-catalog/docs?$count=true

最初のページから 15 項目ずつにまとめられたドキュメントが取得され、合計ヒット数も表示されます。

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

改ページの結果には `$top` と `$skip` の両方が必須です。 ここで `$top` は 1 回に返す項目数を、`$skip` はスキップする項目数をそれぞれ指定します。 次の例では、各ページに次の 15 項目が表示され、`$skip` パラメーターによって何項目ずつジャンプするかが指定されています。

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>レイアウト

検索結果ページでは、サムネイル画像、フィールドのサブセット、製品のフルページへのリンクを表示することができます。

 ![][2]

Azure Search では、`$select` と [Search API 要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)を使用してこれらを表示します。

タイル化されたレイアウトになるようにフィールドのサブセットを返すには、次のように記述します。

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

イメージとメディア ファイルは直接検索できないため、Azure BLOB ストレージなどの別の記憶域プラットフォームに格納すると、コストを削減できます。 インデックスとドキュメントで、外部コンテンツの URL アドレスを格納するフィールドを定義します。 こうすることで、そのフィールドをイメージ リファレンスとして使用できます。 イメージへの URL はこのドキュメントに格納してください。

**onClick** イベントで製品説明ページを取得するには、 [Lookup Document](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) を使用して、取得するドキュメントをキーに渡します。 キーのデータ型は `Edm.String`です。 この例でのデータでは *246810*です。

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>関連性、評価、または価格による並べ替え

並べ替え順の既定基準として関連性が設定されている場合が多いですが、ユーザーが既存の結果をすばやく再シャッフルして、別のランク付けで並べ替えられる選択肢があるのが一般的です。

 ![][3]

Azure Search では、`"Sortable": true.` としてインデックス付けされたすべてのフィールドにおいて、並べ替えは `$orderby` 式に基づきます。`$orderby` 句は OData 式です。 構文について詳しくは、[filters 句と order-by 句のための OData 式の構文](query-odata-filter-orderby-syntax.md)に関する記事をご覧ください。

関連性は、スコアリング プロファイルに深くかかわっています。 既定のスコアリングを使用すると、すべての結果がテキスト分析と統計情報に基づいて順に並べられます。このとき、検索用語との一致内容が多い、あるいは一致レベルが高いドキュメントに高いスコアが付けられます。

別の並べ替え順序は通常、並べ替え順を生成するメソッドをコールバックする **onClick** イベントが関わっています。 たとえば次のようなページ要素を使用した場合を示します。

 ![][4]

選択した並べ替えオプションを入力として使用でき、そのオプションに関連付けられた基準によって並べ替えたリストを返すメソッドが作成されます。

 ![][5]

> [!NOTE]
> 既定のスコアリングは多くのシナリオに使用可能ですが、カスタムのスコアリング プロファイルに基づいた関連性を使用することをお勧めします。 カスタムのスコアリング プロファイルを使用すると、ユーザーのビジネスに有益な項目をブーストすることができます。 詳しくは、[スコアリング プロファイルの追加](index-add-scoring-profiles.md)に関する記事をご覧ください。
>

## <a name="faceted-navigation"></a>ファセット ナビゲーション

検索ナビゲーションは結果ページによく使用され、ページの端または上部に表示される場合が多いです。 Azure Search では、ファセット ナビゲーションを使用すると、定義済みのフィルターに基づいた自動検索ができます。 詳細については、「 [Azure Search のファセット ナビゲーション](search-faceted-navigation.md) 」を参照してください。

## <a name="filters-at-the-page-level"></a>ページ レベルでのフィルター

ソリューションの設計に、特定種類のコンテンツ (たとえば、ページ上部に売り場リストを表示させるオンライン ショッピング用アプリケーション) 専用の検索ページが組み込まれている場合、**onClick** イベントと共に[フィルター式](search-filters.md)を挿入すると、あらかじめフィルター処理された状態でページが開きます。

フィルターは、検索式が挿入されているかどうかにかかわらず送信できます。 たとえば、次の要求ではブランド名でフィルター処理され、それに一致するドキュメントのみが返されます。

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

`$filter`式の詳細については、「[ドキュメントの検索 (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)」を参照してください。

## <a name="see-also"></a>関連項目

- [Azure Search サービス REST API](https://docs.microsoft.com/rest/api/searchservice)
- [インデックス操作](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [ドキュメントの操作](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Azure Search のファセット ナビゲーション](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
