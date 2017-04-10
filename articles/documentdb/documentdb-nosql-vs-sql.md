---
title: "NoSQL を使用する場合と SQL を使用する場合 | Microsoft Docs"
description: "NoSQL の非リレーショナル ソリューションを使用した場合と SQL ソリューションを使用した場合の利点を比較します。 Microsoft Azure NoSQL サービスのいずれか、または SQL Server のいずれかがシナリオに適合するかどうかを説明します。"
keywords: "NoSQL 対 SQL、NoSQL を使用する場合、SQL 対 NoSQL"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 71ef1798-d709-4ccb-9f5c-57948fb96229
ms.service: documentdb
ms.custom: overview
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 84b0b4121e8fc10bdfd3b5daf7fff280dc301d28
ms.lasthandoff: 11/17/2016


---
# <a name="nosql-vs-sql"></a>NoSQL 対 SQL
SQL Server とリレーショナル データベース (RDBMS) は、20 年以上にわたって活躍してきたデータベースです。 ただし、より大きな容量の急速に増える多様なデータを高速で処理する必要性の高まりにより、アプリケーション開発者が求めるデータ ストレージの種類には変化が生じました。 このシナリオを実現するために、大規模な非構造化データと異種データを格納する NoSQL データベースが普及しました。 ほとんどの開発者にとって、リレーショナル データベースはテーブル構造がわかりやすく、使い慣れているため、既定のまたは信頼できるオプションです。しかし、リレーショナル データベース以外も調べてみるべきである多くの理由があります。

NoSQL は、SQL データベースとは明らかに異なるデータベースのカテゴリに属します。 NoSQL は、"Not SQL" に相当するデータ管理システム、または "Not only SQL" などのデータ管理のアプローチを示すためにしばしば使用されます。 NoSQL カテゴリには多くのテクノロジがあります。たとえば、ドキュメント データベース、キー値ストア、列ファミリ ストア、グラフ データベースなどが挙げられ、これらはゲーム アプリ、ソーシャル アプリ、IoT アプリでよく使用されています。

![一般的なシナリオとデータ モデルを示す NoSQL 対 SQL の概要図](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

この記事の目的は、NoSQL と SQL の違いをわかりやすく説明し、Microsoft から提供している NoSQL および SQL サービスの概要を紹介することです。  

## <a name="when-to-use-nosql"></a>NoSQL を使用する場合
新しいソーシャル ネットワーク サイトを構築中であるとします。 ユーザーは投稿を作成し、それに画像、ビデオ、音楽を追加することができます。 他のユーザーは、投稿にコメントし、投稿を評価するポイント ("いいね") を与えることができます。 ランディング ページには、ユーザーが共有し操作できる投稿のフィードが含められます。 

このデータはどのように格納しますか? SQL に精通している場合は、次のような処理方法の開発を始めるでしょう。

![ソーシャル ネットワーク サイトのリレーショナル データ モデルを示す NoSQL 対 SQL の図](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

これまでは順調です。次に、単一の投稿の構造と、それを表示する方法について考えてみます。 投稿と、それに関連付けられた画像、オーディオ、ビデオ、コメント、ポイント、およびユーザー情報を Web サイトまたはアプリケーションに表示する場合は、8 つのテーブル結合でクエリを実行してコンテンツを取得する必要があります。 次に、動的に読み込まれ画面に表示される投稿のストリームを考えてみましょう。タスクを完了するには、数千ものクエリと多くの結合が必要となることが容易に予測できます。

ここで、データを格納し、結合を使用してクエリを行うために、SQL Server のようなリレーショナル ソリューションを使用することができます。SQL では、[JSON 形式の](https://msdn.microsoft.com/library/dn921897.aspx)動的データがサポートされているためです。しかし、この特定のシナリオに対するアプローチを単純化する、NoSQL という別のオプションもあります。 次のような 1 つのドキュメントを使用し、それを DocumentDB (Azure NoSQL ドキュメント データベース サービス) に格納することにより、パフォーマンスを高め、結合なしの 1 つのクエリで投稿全体を取得することができます。 これは、より簡単でよりわかりやすく、パフォーマンスに優れた結果です。

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

さらに、このデータは投稿 ID によってパーティション分割することができます。これにより、データは自然にスケールアウトされ、NoSQL スケール特性を利用できるようになります。 また、NoSQL システムを使用すると、開発者は一貫性を緩和し、可用性の高いアプリを低待機時間で提供することもできます。  最後に、このソリューションの場合、開発者はデータ層でスキーマを定義、管理、および維持しなくても迅速な反復処理を実現できます。

このソリューションは、他の Azure サービスを使用して構築できます。

* [Azure Search](https://azure.microsoft.com/services/search/) を Web アプリ経由で使用すると、ユーザーが投稿を検索できるようにすることができます。
* [Azure App Services](https://azure.microsoft.com/services/app-service/) を使用すると、アプリケーションとバック グラウンド プロセスをホストすることができます。
* [Azure Blob Storage](https://azure.microsoft.com/services/storage/) を使用すると、画像を含む完全なユーザー プロファイルを格納することができます。
* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) を使用すると、ログイン情報などの大量のデータや、利用状況分析用のデータを格納することができます。
* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) を使用すると、プロセスに対するフィードバックを提供したり、適切なユーザーへの適切なコンテンツの配信を支援するナレッジやインテリジェンスを構築したりすることができます。

このソーシャル ネットワーク サイトは、NoSQL データベースがジョブの適切なデータ モデルとなるシナリオの 1 つにすぎません。 このシナリオの詳細、およびソーシャル メディア アプリケーションで DocumentDB のデータをモデル化する方法については、「 [DocumentDB によるソーシャル化](documentdb-social-media-apps.md)」を参照してください。 

## <a name="nosql-vs-sql-comparison"></a>NoSQL と SQL の比較
次の表では、NoSQL と SQL の主な違いを比較します。 

![NoSQL を使用する場合と SQL を使用する場合を示す NoSQL 対 SQL の図。 SQL と NoSQL の比較](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

NoSQL データベースが要件を最も満たしている場合は、Azure で利用できる NoSQL サービスの詳細について説明する次のセクションに進んでください。 あるいは、SQL データベースが要件を最も満たしている合は、「 [Microsoft SQL 製品とは?](#what-are-the-microsoft-sql-offerings)

## <a name="what-are-the-microsoft-azure-nosql-offerings"></a>Microsoft Azure NoSQL 製品とは?
Azure には次の 4 つの完全に管理された NoSQL サービスがあります。 

* [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
* [Azure Table Storage](https://azure.microsoft.com/services/storage/)
* [HDInsight の一部として提供される Azure HBase](https://azure.microsoft.com/services/hdinsight/)
* [Azure Redis Cache](https://azure.microsoft.com/services/cache/)

次の比較表では、各サービスの主な違いをまとめたものを示します。 ご使用のアプリケーションのニーズを最も正確に表しているのはどれですか? 

![Microsoft Azure の NoSQL 製品 (DocumentDB、Table Storage、HDInsight の一部として提供される HBase、Redis Cache) を使用する場合を示す NoSQL 対 SQL の図。](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

これらのサービスの 1 つ以上がアプリケーションのニーズを満たしている場合は、次のリソースを参照してください。 

* [DocumentDB ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)と [DocumentDB のユース ケース](documentdb-use-cases.md)
* [Azure Table Storage を使用する](../storage/storage-dotnet-how-to-use-tables.md)
* [HDInsight の HBase とは](../hdinsight/hdinsight-hbase-overview.md)
* [Redis Cache のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

無料試用版については、「 [次のステップ](#next-steps) 」で示します。

## <a name="what-are-the-microsoft-sql-offerings"></a>Microsoft SQL 製品とは?
Microsoft では次の 5 つの SQL 製品を提供しています。 

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
* [Azure Virtual Machines における SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)
* [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
* [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)
* [Analytics Platform System (オンプレミスのアプライアンス)](https://www.microsoft.com/en-us/server-cloud/products/analytics-platform-system/)

仮想マシン上の SQL Server または SQL Database に関心がある場合は、「[クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](../sql-database/sql-database-paas-vs-sql-server-iaas.md)」を参照してください。2 つの違いが詳しく説明されています。

SQL が最善のオプションである場合は、[SQL Server](https://www.microsoft.com/server-cloud/products/) に関するページを参照してください。Microsoft SQL 製品およびサービスで提供する必要がある内容について詳しく説明されています。

無料試用版リンクと評価リンクについては、「 [次のステップ](#next-steps) 」を参照してください。

## <a name="next-steps"></a>次のステップ
無料試用版を使用して、SQL 製品と NoSQL 製品について学習することをお勧めします。 

* Azure サービスすべてについて、 [free one-month trial (1 か月の無料評価版)](https://azure.microsoft.com/pricing/free-trial/) にサインアップすれば、どの Azure サービスにも利用できる 200 ドルを進呈します。
  
  * [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
  * [HDInsight の一部として提供される Azure HBase](https://azure.microsoft.com/services/hdinsight/)
  * [Azure Redis Cache](https://azure.microsoft.com/services/cache/)
  * [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)
  * [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
  * [Azure Table Storage](https://azure.microsoft.com/services/storage/)
* [仮想マシンでの SQL Server 2016 の評価バージョン](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/)にサインアップすることも、[SQL Server 評価版](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016)をダウンロードすることもできます。
  
  * [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
  * [Azure Virtual Machines における SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)


