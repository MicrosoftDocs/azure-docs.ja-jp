---
title: "Azure Search でのページ検索結果の表示方法 | Microsoft Docs"
description: "Microsoft Azure のホスト型クラウド検索サービスである Azure Search の改ページ。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: a0a1d315-8624-4cdf-b38e-ba12569c6fcc
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1054e15a2751c53aad5dbc8054c4cec41102dee9


---
# <a name="how-to-page-search-results-in-azure-search"></a>Azure Search でのページ検索結果の表示方法
この記事では、検索結果ページの標準的な要素である合計数、ドキュメント取得、並べ替え順序、およびナビゲーションなどを、Azure Search サービス REST API を使用して実装する方法を説明します。

以下に示すべての例において、データまたは情報を検索結果ページに表示するためのページ関連オプションは、Azure Search サービスに送信される [検索ドキュメント](http://msdn.microsoft.com/library/azure/dn798927.aspx) 要求で指定します。 要求には、GET コマンド、パス、要求内容をサービスに伝えるクエリ パラメーター、および応答の作成方法が含まれます。

> [!NOTE]
> 有効な要求には、サービス URL とパスや HTTP 動詞、`api-version` などがあります。 簡潔にまとめ、改ページに関連する構文だけに焦点を当てられるように例の記載を省きました。 要求構文の詳細については、「 [Azure Search サービス REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) 」ドキュメントを参照してください。
> 
> 

## <a name="total-hits-and-page-counts"></a>合計ヒット数とページ数
クエリから返される検索結果の合計数を表示し、それらの結果を一定の数ごとにページにまとめて返すことは、ほぼすべての検索ページでの基本機能です。

![][1]

Azure Search では、`$count`、`$top`、および `$skip` のパラメーターを使用すると、これらの値が返されます。 次に示すのは、合計ヒット数のサンプル要求が `@OData.count`として返された例です。

        GET /indexes/onlineCatalog/docs?$count=true

最初のページから 15 項目ずつにまとめられたドキュメントが取得され、合計ヒット数も表示されます。

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

改ページの結果には `$top` と `$skip` の両方が必須です。 ここで `$top` は 1 回に返す項目数を、`$skip` はスキップする項目数をそれぞれ指定します。 次の例では、各ページに次の 15 項目が表示され、`$skip` パラメーターによって何項目ずつジャンプするかが指定されています。

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>レイアウト
検索結果ページでは、サムネイル画像、フィールドのサブセット、製品のフルページへのリンクを表示することができます。

 ![][2]

Azure Search では、 `$select` と参照コマンドを使用してこれらを表示します。

タイル化されたレイアウトになるようにフィールドのサブセットを返すには、次のように記述します。

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

イメージとメディア ファイルは直接検索できないため、Azure BLOB ストレージなどの別の記憶域プラットフォームに格納すると、コストを削減できます。 インデックスとドキュメントで、外部コンテンツの URL アドレスを格納するフィールドを定義します。 こうすることで、そのフィールドをイメージ リファレンスとして使用できます。 イメージへの URL はこのドキュメントに格納してください。

**onClick** イベントで製品説明ページを取得するには、 [Lookup Document](http://msdn.microsoft.com/library/azure/dn798929.aspx) を使用して、取得するドキュメントをキーに渡します。 キーのデータ型は `Edm.String`です。 この例でのデータでは *246810*です。 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>関連性、評価、または価格による並べ替え
並べ替え順の既定基準として関連性が設定されている場合が多いですが、ユーザーが既存の結果をすばやく再シャッフルして、別のランク付けで並べ替えられる選択肢があるのが一般的です。

 ![][3]

Azure Search では、`"Sortable": true.` とインデックス付けされたすべてのフィールドで、`$orderby` 式に基づいた並べ替えが実行されます。

関連性は、スコアリング プロファイルに深くかかわっています。 既定のスコアリングを使用すると、すべての結果がテキスト分析と統計情報に基づいて順に並べられます。このとき、検索用語との一致内容が多い、あるいは一致レベルが高いドキュメントに高いスコアが付けられます。

別の並べ替え順序は通常、並べ替え順を生成するメソッドをコールバックする **onClick** イベントが関わっています。 たとえば次のようなページ要素を使用した場合を示します。

 ![][4]

選択した並べ替えオプションを入力として使用でき、そのオプションに関連付けられた基準によって並べ替えたリストを返すメソッドが作成されます。

 ![][5]

> [!NOTE]
> 既定のスコアリングは多くのシナリオに使用可能ですが、カスタムのスコアリング プロファイルに基づいた関連性を使用することをお勧めします。 カスタムのスコアリング プロファイルを使用すると、ユーザーのビジネスに有益な項目をブーストすることができます。 詳細は、「 [スコアリング プロファイルの追加](http://msdn.microsoft.com/library/azure/dn798928.aspx) 」を参照してください。 
> 
> 

## <a name="faceted-navigation"></a>ファセット ナビゲーション
検索ナビゲーションは結果ページによく使用され、ページの端または上部に表示される場合が多いです。 Azure Search では、ファセット ナビゲーションを使用すると、定義済みのフィルターに基づいた自動検索ができます。 詳細については、「 [Azure Search のファセット ナビゲーション](search-faceted-navigation.md) 」を参照してください。

## <a name="filters-at-the-page-level"></a>ページ レベルでのフィルター
ソリューションの設計に、特定種類のコンテンツ (たとえば、ページ上部に売り場リストを表示させるオンライン ショッピング用アプリケーション) 専用の検索ページが組み込まれている場合、 **onClick** イベントとともにフィルター式を挿入すると、あらかじめフィルター処理された状態でページが開きます。 

フィルターは、検索式が挿入されているかどうかにかかわらず送信できます。 たとえば、次の要求ではブランド名でフィルター処理され、それに一致するドキュメントのみが返されます。

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

`$filter`式の詳細については、「[ドキュメントの検索 (Azure Search API)](http://msdn.microsoft.com/library/azure/dn798927.aspx)」を参照してください。

## <a name="see-also"></a>関連項目
* [Azure Search サービス REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)
* [インデックス操作](http://msdn.microsoft.com/library/azure/dn798918.aspx)
* [ドキュメントの操作](http://msdn.microsoft.com/library/azure/dn800962.aspx)
* [Azure Search: チュートリアル、ビデオ デモ、サンプル](search-video-demo-tutorial-list.md)
* [Azure Search のファセット ナビゲーション](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 



<!--HONumber=Dec16_HO2-->


