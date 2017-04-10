---
title: "DocumentDB の設計パターン: ソーシャル メディア アプリ | Microsoft Docs"
description: "DocumentDB のストレージの柔軟性と他の Azure サービスを活用したソーシャル ネットワークの設計パターンについて説明します。"
keywords: "ソーシャル メディア アプリ"
services: documentdb
author: ealsur
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 2dbf83a7-512a-4993-bf1b-ea7d72e095d9
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a49021d7887ee91da902e5c3dea8cbc6cb3de29d
ms.lasthandoff: 03/17/2017


---
# <a name="going-social-with-documentdb"></a>DocumentDB によるソーシャル化
大規模に相互接続された社会で生きていると、日々の生活の中で **ソーシャル ネットワーク**に参加することになります。 ソーシャル ネットワークを使用して、友人や同僚、家族と交流し、共通の関心を持つ人々と情熱を分かち合うこともあります。

エンジニアや開発者は、これらのネットワークがデータをどのように保存し、相互接続しているのか疑問に思っているかもしれません。また、特定のニッチ市場向けの新しいソーシャル ネットワークの構築や設計を任されている場合もあるでしょう。 そこで、"このすべてのデータはどのように保存されているのか" という大きな疑問が生じます。

写真、ビデオ、音楽などの関連メディアと共に記事を投稿できる新しいソーシャル ネットワークを構築しようとしていると仮定しましょう。 ユーザーは投稿にコメントしたり、投稿を評価したりできます。 メインの Web サイト ランディング ページに表示され、ユーザーが操作できる投稿のフィードもあります。 これは (最初は) それほど複雑ではないように思われますが、簡潔にするために、ここまでにしておきましょう (関係性の影響を受けるカスタム ユーザー フィードを深く掘り下げて考えることもできましたが、それはこの記事の目的ではありません)。

では、データはどこにどのように保存すればよいのでしょうか。

皆さんの多くは、SQL データベースの使用経験があるか、少なくとも [データのリレーショナル モデリング](https://en.wikipedia.org/wiki/Relational_model) をご存知だと思いますので、次のようなものを作成したくなるかもしれません。

![相対リレーショナル モデルを示すダイアグラム](./media/documentdb-social-media-apps/social-media-apps-sql.png) 

なるほど完全に正規化された見事なデータ構造ですが、 ここには拡張性がありません。 

誤解しないでください。私は SQL データベースを長年使用してきました。SQL データベースは優れていますが、他のあらゆるパターン、手法、ソフトウェア プラットフォームと同様に、すべてのシナリオに最適というわけではありません。

このシナリオで SQL が最適な選択肢でないのはなぜでしょうか。 1 つの投稿の構造を見てみましょう。Web サイトやアプリケーションでその投稿を表示する場合、たった 1 つの投稿を表示するために  8 つのテーブルを結合してクエリを実行しなければなりません。動的に読み込まれ、画面に表示される投稿のストリームを想像してみてください。そうすれば、私が目指しているものがおわかりいただけると思います。

コンテンツを提供するために、こうした多数の結合を使用する何千ものクエリを解決できるだけの能力を備えた巨大な SQL インスタンスを使用することもできますが、実際のところ、よりシンプルなソリューションが存在するのに、そのようなインスタンスをわざわざ使用する必要があるでしょうか。

## <a name="the-nosql-road"></a>NoSQL への道
[Azure で実行](http://neo4j.com/developer/guide-cloud-deployment/#_windows_azure) できる特殊なグラフ データベースがありますが、これらは安価ではなく、IaaS サービス (サービスとしてのインフラストラクチャ、主に Virtual Machines) とメンテナンスを必要とします。 この記事では、Azure の NoSQL データベースである [DocumentDB](https://azure.microsoft.com/services/documentdb/)で実行され、ほとんどのシナリオに対応できる低コストのソリューションに照準を合わせます。 [NoSQL](https://en.wikipedia.org/wiki/NoSQL) のアプローチの採用、JSON 形式でのデータの保存、[非正規化](https://en.wikipedia.org/wiki/Denormalization)の適用により、これまで複雑であった投稿を次のような 1 つの[ドキュメント](https://en.wikipedia.org/wiki/Document-oriented_database)に変換できます。

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

また、投稿を 1 つのクエリで結合なしに取得できます。 これははるかにシンプルでわかりやすい方法です。予算的にも、必要なリソースを減らして大きな成果を上げることができます。

Azure DocumentDB では、[カスタマイズ](documentdb-indexing-policies.md)も可能な自動インデックス作成機能によって、すべてのプロパティのインデックスが作成されます。 このスキーマフリーのアプローチにより、さまざまな動的構造でドキュメントを保存できます。将来的には、カテゴリのリストや投稿に関連付けられたハッシュタグを投稿に含めたいと考えています。DocumentDB では、余分な作業を必要とせずに、追加された属性を使用して新しいドキュメントを処理します。

投稿へのコメントは、親プロパティを持つ他の投稿と同様に処理できます (これにより、オブジェクト マッピングが簡素化されます)。 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

また、すべてのソーシャル インタラクションをカウンターとして別のオブジェクトに保存できます。

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

フィードの作成は、特定の関連度順で投稿 ID のリストを保持できるドキュメントを作成するだけのことです。

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

作成日順に並べ替えられた投稿を含む "最新の" ストリームや、過去 24 時間により多くの「いいね」を獲得した投稿を含む "最もホットな" ストリームを取得することもできます。また、フォロワーや関心事などのロジックに基づいたユーザーごとのカスタム ストリームも実装できますが、やはり投稿のリストに過ぎません。 これは、これらのリストの構築方法の問題ですが、読み取りパフォーマンスは引き続き制約を受けていません。 これらのリストのいずれかを取得したら、[IN 演算子](documentdb-sql-query.md#WhereClause)を使用して DocumentDB に対して 1 つのクエリを発行し、投稿のページを一度に取得します。

フィードのストリームは、[Azure App Services](https://azure.microsoft.com/services/app-service/) のバックグラウンド プロセス ([Webjobs](../app-service-web/web-sites-create-web-jobs.md)) を使用して構築できました。 投稿が作成されたら、[Azure Storage](https://azure.microsoft.com/services/storage/) [Queues](../storage/storage-dotnet-how-to-use-queues.md) を使用してバックグラウンド処理をトリガーし、[Azure Webjobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) を使用して Webjobs をトリガーすることで、独自のカスタム ロジックに基づいてストリーム内での投稿の伝達を実装できます。 

この同じ手法を使用して最終的に一貫した環境を構築することで、投稿に対する評価と「いいね」を遅延的に処理できます。

フォロワーの処理はさらに複雑です。 DocumentDB にはドキュメントの最大サイズの制限があり、サイズの大きいドキュメントの読み取りと書き込みは、アプリケーションのスケーラビリティに影響を与えます。 よって次のような構造のドキュメントとしてフォロワーを保存する方法が考えられます。

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

この方法では、フォロワーが数千人のユーザーであれば問題なく機能しますが、セレブのユーザーを加えることになった場合、ドキュメント サイズが大きくなり、最終的にはドキュメント サイズの制限に達することになります。

これを解決するには、混成方式を使います。 ユーザー統計情報ドキュメントの一部として、次のようにフォロワーの数を保存します。

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

さらに、フォロワーの実際のグラフは、単純な "A-follows-B" のようなグラフの保存と取得を可能にする [拡張機能](https://github.com/richorama/AzureStorageExtensions#azuregraphstore) を使用して、Azure Storage テーブルに保存することができます。 この方法では、正確なフォロワー リストを必要なときに取得するプロセスを Azure Storage テーブルに委任できますが、簡単な数値の検索には引き続き DocumentDB を使用します。

## <a name="the-ladder-pattern-and-data-duplication"></a>"ラダー (梯子)" パターンとデータの重複
投稿を参照する JSON ドキュメントでお気付きかと思いますが、同じユーザーが何度も出現します。 皆さんの推測どおり、この非正規化を考慮すると、これはユーザーを表す情報が複数の場所に存在する可能性があることを意味します。

クエリの高速化を可能するために、データの重複が発生しています。 この副作用による問題は、何らかの操作によってユーザーのデータが変更された場合に、そのユーザーがこれまでに実行したすべてのアクティビティを見つけ、そのすべてを更新する必要があることです。 あまり現実的ではなさそうですよね。

グラフ データベースは独自の方法でこの問題を解決します。私たちは、アクティビティごとにアプリケーションで表示するユーザーのキー属性を識別することでこの問題を解決しようとしています。 アプリケーションで投稿を視覚的に示し、作成者の名前と写真だけを表示する場合、ユーザーのすべてのデータを "createdBy" 属性に保存するのはなぜでしょうか。 各コメントにユーザーの写真を表示するだけなら、そのユーザーのその他の情報は実際には不要です。 ここで、私が "ラダー パターン" と呼ぶものが効果を発揮し始めます。

例として、ユーザー情報を取得してみましょう。

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

この情報を調べることで、重要な情報とそうでない情報をすぐに見つけることができるため、次のように "ラダー" を作成できます。

![ラダー パターンの図](./media/documentdb-social-media-apps/social-media-apps-ladder.png)

最小の段はユーザーチャンクと呼ばれます。これは、ユーザーを識別する情報の最小単位であり、データの重複に対して使用されます。 重複するデータのサイズを "表示する" 情報だけに減らすことで、大規模な更新の可能性を低減しています。

真ん中の段は "ユーザー" と呼ばれます。これは、DocumentDB での最もパフォーマンスに依存するクエリで使用される完全なデータであり、最も頻繁にアクセスされる重要なデータです。 ここには、ユーザーチャンクで表される情報が含まれます。

最大の段が "拡張ユーザー" です。 拡張ユーザーには、すべての重要なユーザー情報と、実際には迅速に読み取る必要のないデータや (ログイン プロセスのように) 最終的に使用するデータが含まれます。 このデータは、DocumentDB の外部、Azure SQL Database、または Azure Storage Tables に保存できます。

ユーザーを分割し、この情報をさまざまな場所に保存するのはなぜでしょうか。 それは、DocumentDB の記憶域は無限ではないからであり、パフォーマンスの観点から言えば、ドキュメントのサイズが大きくなるほど、クエリのコストが高くなるからです。 ソーシャル ネットワークでパフォーマンスに依存するすべてのクエリを実行するための適切な情報を含めてドキュメントをスリムに保ち、その他の情報は、完全なプロファイル編集、ログイン、利用状況分析のためのデータ マイニング、ビッグ データへの取り組みなどの最終的なシナリオ用に保存します。 データ マイニングのためのデータ収集は、Azure SQL Database で実行されるので、実際には収集に時間がかかってもかまいません。私たちが気に掛けているのは、ユーザーに高速で軽快なエクスペリエンスを提供することです。 DocumentDB に保存されたユーザーは次のようになります。

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

さらに、投稿は次のようになります。

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

チャンクのいずれかの属性が影響を受ける編集が発生しても、インデックス付き属性を参照するクエリ (SELECT * FROM posts p WHERE p.createdBy.id == “edited_user_id”) を使用し、チャンクを更新することで、影響を受けたドキュメントを簡単に見つけることができます。

## <a name="the-search-box"></a>検索ボックス
ユーザーは、幸いにも大量のコンテンツを生成します。 コンテンツのストリームに直接存在しない可能性のあるコンテンツを検索して見つける機能を提供できる必要があります。作成者をフォローするのではなく、6 か月前に行われた古い投稿を見つけようとしているだけだからです。

Azure DocumentDB を使用しているので、 [Azure Search](https://azure.microsoft.com/services/search/) を使用して、(検索プロセスと UI 以外の) コードを 1 行も入力せずに検索エンジンを数分で簡単に実装できます。

これが非常に簡単なのはなぜでしょうか。

Azure Search は、いわゆる[インデクサー](https://msdn.microsoft.com/library/azure/dn946891.aspx)を実装しています。インデクサーは、データ リポジトリにフックを設定し、インデックス内のオブジェクトを自動的に追加、更新、または削除するバックグラウンド プロセスです。 [Azure SQL Database インデクサー](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/)、[Azure BLOB インデクサー](../search/search-howto-indexing-azure-blob-storage.md)、[Azure DocumentDB インデクサー](../search/search-howto-index-documentdb.md)がサポートされています。 DocumentDB と Azure Search は、どちらも JSON 形式で情報を保存するので、DocumentDB から Azure Search への情報の移行は簡単です。[インデックスを作成](../search/search-create-index-portal.md)し、ドキュメントからインデックス付けする属性をマップするだけで済むため、わずか数分で終わります (データのサイズによって異なります)。クラウド インフラストラクチャの優れたサービスとしての検索ソリューションにより、すべてのコンテンツを検索対象にすることができます。 

Azure Search の詳細については、「 [A Hitchhikers Guide to Search (検索のためのヒッチハイカー ガイド)](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)」をご覧ください。

## <a name="the-underlying-knowledge"></a>基礎となる知識
日々増加し続けるすべてのコンテンツを保存した後に、"ユーザーからのこのすべての情報ストリームを使って何ができるのか" を考えていることに気付くかもしれません。

答えは簡単です。情報ストリームを活用し、情報ストリームから学ぶのです。

しかし、何を学ぶことができるのでしょうか。 簡単な例としては、 [センチメント分析](https://en.wikipedia.org/wiki/Sentiment_analysis)、ユーザーの好みに基づくコンテンツの推奨、ソーシャル ネットワークで公開されるすべてのコンテンツが家族向けの安全なものであることを保証する自動コンテンツ モデレーターなどがあります。

人を夢中にさせるような話なので、シンプルなデータベースやファイルからこれらのパターンや情報を抽出するには、数理科学の博士号が必要と思っておられるでしょう。しかし、それは違います。

[Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx) に含まれる [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) は、完全に管理されたクラウド サービスです。このサービスを利用することで、シンプルなドラッグ アンド ドロップ インターフェイスでのアルゴリズムを使用したワークフローの作成や、[R](https://en.wikipedia.org/wiki/R_\(programming_language\)) での独自のアルゴリズムのコーディング、構築済みのすぐに使える API ([Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2)、[Content Moderator](https://www.microsoft.com/moderator)、[Recommendations](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2) など) の使用が可能になります。

これらの Machine Learning シナリオは、さまざまなソースからの情報を取り込むために [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) を使用し、その情報の処理と Azure Machine Learning で処理できる出力の生成に [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) を使用することで実現できます。

利用できる別のオプションとして、[Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) を使用したユーザーのコンテンツの分析があります。ユーザーが何を書いているかを [Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api) で分析してコンテンツを深く理解できるだけではなく、望ましくないコンテンツや成人向けのコンテンツを [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) で検出することもできます。 Cognitive Services には、Machine Learning の知識を必要とせずに使用できる独創的なソリューションがたくさん含まれています。

## <a name="a-planet-scale-social-experience"></a>世界規模のソーシャル エクスペリエンス
最後に触れなければならない重要なトピックは**スケーラビリティ**です。 アーキテクチャを設計するときは、データの処理量の増加や地理的範囲の拡大に対応しなければならないため、各コンポーネントが自動的に拡張できることが非常に重要です。 DocumentDB を使用すると、こうした複雑な作業を**ターンキー エクスペリエンス**として実現できます。

DocumentDB では、指定された**パーティション キー** (ドキュメントの属性の 1 つとして定義) に基づいてパーティションが自動作成されるため、[動的なパーティション分割](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/)機能をすぐに使用できます。 使用可能な[ベスト プラクティス](documentdb-partition-data.md#designing-for-partitioning)を考慮しながら、適切なパーティション キーをデザイン時に定義する必要があります。ソーシャル エクスペリエンスの場合、パーティション分割戦略は、クエリ実行方法と書き込み方法に合わせる必要があります。クエリ実行については、同じパーティション内で読み取ることが望ましく、書き込みについては、複数のパーティションに書き込みを分散させることで "ホット スポット" を回避します。 たとえば、一時的なキー (日/月/週)、コンテンツのカテゴリ、地理的リージョン、ユーザーに基づいてパーティション分割でき、どの方法でパーティション分割するかは、データに対してどのようにクエリを実行し、そのデータをソーシャル エクスペリエンスでどのように表示するかによって異なります。 

特筆すべきは、DocumentDB では、すべてのパーティションでクエリ ([集計](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)を含む) が透過的に実行される点です。データが拡大しても、ロジックを追加する必要はありません。

トラフィックやリソースの消費量 ([RU](documentdb-request-units.md) (要求ユニット) で測定) は、時間の経過と共に増加します。 ユーザーベースが拡大し、ユーザーによるコンテンツの作成や読み取りが多くなり始めると、ご自身の読み書きの頻度も増えるため、**スループットの拡張**機能が重要になってきます。 RU は非常に簡単に増やすことができます。RU を増やすには、Azure Portal で数回クリックするか [API でコマンドを発行](https://docs.microsoft.com/rest/api/documentdb/replace-an-offer)します。

![スケールアップとパーティション キーの定義](./media/documentdb-social-media-apps/social-media-apps-scaling.png)

状況がこのまま好転し続け、ご自身のプラットフォームが、他のリージョン、国、または大陸のユーザーの目に留まり、使われるようになったらどうでしょう。嬉しい驚きですね。

でも、待ってください... やがては、プラットフォームのユーザー エクスペリエンスが最適ではないことに気が付きます。自分のリージョンからかなり離れた場所にユーザーがいる場合、その待ち時間は相当なものになります。でも、そのユーザーにはプラットフォームの使用をやめてほしくありません。 簡単に**グローバル展開**できる手段さえあれば、と思うでしょう... ところが、手段はあるのです。

DocumentDB を使用すると、数回のクリックで[データをグローバルかつ透過的にレプリケート](documentdb-portal-global-replication.md)し、使用可能なリージョンの中で、[クライアント コード](documentdb-developing-with-multiple-regions.md)からそのリージョンを選択できます。 また、これは[複数のフェールオーバー リージョン](documentdb-regional-failovers.md)を確保できることも意味します。 

データをグローバルにレプリケートするときは、クライアントがそのデータを利用できるかどうかを確認する必要があります。 Web フロントエンドを使用する場合、またはモバイル クライアントから API にアクセスする場合は、[Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) をデプロイし、必要なリージョンすべてに Azure App Service のクローンを作成して、[パフォーマンス構成](../app-service-web/web-sites-traffic-manager.md)を使用しながら、拡張されたグローバル カバレッジをサポートできます。 フロントエンドまたは API にアクセスしたクライアントは、最も近い App Service にルーティングされ、その APP Service はローカルの DocumentDB レプリカに接続されます。

![ソーシャル プラットフォームへのグローバル カバレッジの追加](./media/documentdb-social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>まとめ
この記事では、低コストのサービスを使用して Azure で完全なソーシャル ネットワークを構築し、多層ストレージ ソリューションと "ラダー" と呼ばれるデータ分散の使用を促進することによって優れた結果をもたらす代替手段を明らかにすることを試みています。

![ソーシャル ネットワーキングでの Azure サービス間の対話を示すダイアグラム](./media/documentdb-social-media-apps/social-media-apps-azure-solution.png)

実際には、この種のシナリオに対応する特効薬はありません。優れたソーシャル アプリケーションを提供する Azure DocumentDB の速度と自由度、Azure Search のような最高クラスの検索ソリューションの背後にあるインテリジェンス、言語に依存しないアプリケーションではなく、強力なバックグラウンド プロセスをホストする Azure App Services の柔軟性、大量のデータを保存する拡張可能な Azure Storage と Azure SQL Database、プロセスにフィードバックを提供することができ、適切なコンテンツを適切なユーザーに提供するうえで役立つ知識とインテリジェンスを生み出す Azure Machine Learning の分析力など、優れたサービスの組み合わせによって生まれる相乗効果により、優れた体験を構築することが可能になります。

## <a name="next-steps"></a>次のステップ
データのモデル化の詳細については、「 [DocumentDB のデータのモデル化](documentdb-modeling-data.md) 」をご覧ください。 DocumentDB の他のユース ケースに関心がある場合は、「 [DocumentDB の一般的なユース ケース](documentdb-use-cases.md)」をご覧ください。

または、 [DocumentDB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)に従って、DocumentDB の詳細を確認してください。


