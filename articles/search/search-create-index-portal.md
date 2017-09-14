---
title: "インデックスを作成する (ポータル - Azure Search) | Microsoft Docs"
description: "Azure ポータルを利用してインデックスを作成する"
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Azure ポータルを利用して Azure Search インデックスを作成する
> [!div class="op_single_selector"]
> * [概要](search-what-is-an-index.md)
> * [ポータル](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST ()](search-create-index-rest-api.md)
> 
> 

Azure Portal の組み込みのインデックス デザイナーを使用して、Azure Search サービスで実行するための[検索インデックス](search-what-is-an-index.md)をプロトタイプ作成または作成します。 

## <a name="prerequisites"></a>前提条件

この記事では、[Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)と [Azure Search サービス](search-create-service-portal.md)を前提にしています。  

## <a name="find-your-search-service"></a>検索サービスを見つける
1. Azure Portal ページにサインインし、[自分のサブスクリプション用の検索サービス](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)を確認します。
2. Azure Search サービスを選択します。

## <a name="name-the-index"></a>インデックスに名前を付ける

1. ページの上部にあるコマンド バーの **[Add index] \(インデックスの追加)** ボタンをクリックします。
2. Azure Search インデックスに名前を付けます。 
   * 文字で始めます。
   * 小文字、数字、またはダッシュ ("-") のみを使用してください。
   * 名前を 60 文字に制限します。

  インデックス名は、そのインデックスへの接続や、Azure Search REST API で HTTP 要求を送信するために使用されるエンドポイント URL の一部になります。

## <a name="define-the-fields-of-your-index"></a>インデックスのフィールドを定義する

インデックスの構成には、インデックス内の検索可能なデータを定義する*フィールド コレクション*が含まれています。 より具体的には、これは個別にアップロードするドキュメントの構造を指定します。 フィールド コレクションには、必須フィールドと省略可能フィールド (名前および型指定付き) が、そのフィールドを使用する方法を決定するインデックス属性と共に含まれています。

1. **[Add index] \(インデックスの追加)** ブレードで、**[Fields >] \(フィールド>)** をクリックしてフィールド定義ブレードをスライドさせて開きます。 

2. 型 Edm.String の生成された*キー* フィールドを受け入れます。 既定では、このフィールドには *id* という名前が付けられますが、文字列が[名前付け規則](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)を満たす限り、その名前を変更できます。 キー フィールドはどの Azure Search インデックスにも必須であり、かつ文字列である必要があります。

3. アップロードするドキュメントを完全に指定するためのフィールドを追加します。 ドキュメントが *id*、*ホテル名*、*住所*、*市*、および *地域*で構成されている場合は、インデックス内のそれぞれに対応するフィールドを作成します。 属性の設定に役立つ情報については、[下のセクションにある設計ガイダンス](#design)を確認してください。

4. 必要に応じて、フィルター式で内部的に使用される任意のフィールドを追加します。 フィールドを検索操作から除外するようにフィールドの属性を設定できます。

5. 完了したら、**[OK]** をクリックしてインデックスを保存および作成します。

## <a name="tips-for-adding-fields"></a>フィールドを追加するためのヒント

ポータルでのインデックスの作成では、キーボードを集中的に使用します。 次のワークフローに従うことによって、手順を最小限に抑えてください。

1. 最初に、名前を入力し、データ型を設定することによってフィールドの一覧を構築します。

2. 次に、各属性の上部にあるチェック ボックスを使用して、すべてのフィールドの設定を一括して有効にしてから、それを必要としないいくつかのフィールドのボックスを選択的にクリアします。 たとえば、文字列フィールドは一般に検索可能です。 そのため、**[取得可能]** と **[Searchable]\(検索可能)** をクリックすると、どちらも検索結果内のフィールドの値を返すだけでなく、フィールドに対するフルテキスト検索も許可されるようにできます。 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>属性を設定するための設計ガイダンス

いつでも新しいフィールドを追加できますが、既存のフィールド定義はインデックスの有効期間の間ロックされます。 このため、開発者は通常、単純なインデックスを作成したり、アイデアをテストしたり、ポータル ページを使用して設定を検索したりするためのポータルを使用します。 インデックスを容易に再構築できるようにコード ベースのアプローチに従う場合は、インデックス設計を頻繁に反復する方がより効率的です。

インデックスが保存される前に、アナライザーとサジェスターがフィールドに関連付けられます。 各タブ付きページをクリックして、インデックス定義に言語アナライザーまたはサジェスターを追加するようにしてください。

文字列フィールドは多くの場合、**検索可能**および**取得可能**としてマークされます。

検索結果を絞り込むために使用されるフィールドには、**並べ替え可能**、**フィルター可能**、および**ファセット可能**が含まれます。

フィールド属性は、フィールドがどのように使用されるか (フルテキスト検索、ファセット ナビゲーション、並べ替えなどの操作で使用されるかどうか) を決定します。 次の表は、各属性を示しています。

|Attribute|Description|  
|---------------|-----------------|  
|**検索可能**|フルテキスト検索可能であり、インデックス作成中の単語分割などの字句解析に従います。 検索可能フィールドを "sunny day" などの値に設定した場合、その値は内部的に個別のトークン "sunny" と "day" に分割されます。 詳細については、「[フルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。|  
|**フィルター可能**|**$filter** クエリで参照されます。 型 `Edm.String` または `Collection(Edm.String)` のフィルター可能フィールドは単語分割されないため、比較は完全に一致するかどうかだけになります。 たとえば、このようなフィールドを "sunny day" に設定した場合、`$filter=f eq 'sunny'` では一致が見つかりませんが、`$filter=f eq 'sunny day'` では見つかります。 |  
|**並べ替え可能**|既定では、システムは結果をスコアで並べ替えますが、ドキュメント内のフィールドに基づいて並べ替えを構成できます。 型 `Collection(Edm.String)` のフィールドを**並べ替え可能**にすることはできません。 |  
|**ファセット可能**|通常、カテゴリ (たとえば、特定の市にあるホテル) ごとのヒット カウントを含む検索結果のプレゼンテーションで使用されます。 このオプションは、 `Edm.GeographyPoint`型のフィールドでは使用できません。 **フィルター可能**、**並べ替え可能**、または**ファセット可能**である型 `Edm.String` のフィールドの長さは、最大 32 キロバイトです。 詳細については、「[Create Index (REST API) (インデックスの作成 (REST API))](https://docs.microsoft.com/rest/api/searchservice/create-index)」を参照してください。|  
|**key**|インデックス内のドキュメントの一意識別子。 キー フィールドとして正確に 1 つのフィールドを選択する必要があり、それは型 `Edm.String` である必要があります。|  
|**取得可能**|検索結果でこのフィールドを返すことができるかどうかを決定します。 これは、あるフィールド (*利幅* など) をフィルター、並べ替え、またはスコア付けのメカニズムとして使用するが、このフィールドをエンド ユーザーには表示したくない場合に役立ちます。 `true` for `key` である必要があります。|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>API セクションの例で使用されているホテル インデックスを作成する

Azure Search API のドキュメントには、単純な*ホテル* インデックスを処理するコード例が含まれています。 下のスクリーンショットでは、インデックス定義中に指定されたフランス語の言語アナライザーを含むインデックス定義を確認できます。これは、ポータルで実習問題として再作成できます。

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>次のステップ

Azure Search インデックスを作成した後、次の手順である「[upload searchable data into the index (検索可能なデータをインデックスにアップロードする)](search-what-is-data-import.md)」に移動できます。

あるいは、インデックスをより深く調査することもできます。 フィールド コレクションに加えて、インデックスはアナライザー、サジェスター、スコアリング プロファイル、CORS の各設定も指定します。 ポータルは、最も一般的な要素であるフィールド、アナライザー、およびサジェスターを定義するためのタブ付きページを提供します。 その他の要素を作成または変更するには、REST API または .NET SDK を使用できます。

## <a name="see-also"></a>関連項目

 [フルテキスト検索のしくみ](search-lucene-query-architecture.md)  
 [検索サービス REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)


