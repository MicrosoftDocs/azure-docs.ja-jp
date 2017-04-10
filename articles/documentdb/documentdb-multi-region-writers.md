---
title: "Azure DocumentDB を使用したマルチマスター データベース アーキテクチャ | Microsoft Docs"
description: "Azure DocumentDB を使用して複数の地理的リージョン間でローカルの読み取り/書き込みを実行するアプリケーション アーキテクチャを設計する方法について説明します。"
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d6292567bbf7afd71b21be3b236537c609c63644
ms.lasthandoff: 03/07/2017


---
# <a name="multi-master-globally-replicated-database-architectures-with-documentdb"></a>DocumentDB を使用したグローバルにレプリケートされたマルチマスター データベース アーキテクチャ
DocumentDB では、ワークロードであらゆる場所での待機時間の短いアクセスによって複数のリージョンにデータを配布できる、ターンキー [グローバル レプリケーション](documentdb-distribute-data-globally.md)をサポートしています。 このモデルは、1 つの地理的リージョンにライターが存在し、他の複数の (読み取り) リージョンにグローバルに分散したリーダーが存在する発行者/コンシューマー ワークロードに一般に使用されます。 

DocumentDB のグローバル レプリケーションのサポートを使用して、ライターとリーダーがグローバルに分散するアプリケーションを構築することもできます。 このドキュメントでは、Azure DocumentDB を使用して、分散したライターのローカル書き込みアクセスとローカル読み取りアクセスを実現するパターンの概要を説明します。

## <a id="ExampleScenario"></a>コンテンツ発行 - シナリオ例
DocumentDB でグローバルに分散したマルチリージョン/マルチマスターの読み取り/書き込みパターンを使用する方法を説明するために、実際のシナリオを見てみましょう。 DocumentDB 上に構築されたコンテンツ発行プラットフォームがあるとします。 発行者とコンシューマーの両方の優れたユーザー エクスペリエンスを実現するために、このプラットフォームが満たす必要のある要件がいくつかあります。

* 作成者と購読者はどちらも世界中に分散しています。 
* 作成者はローカル (最寄りの) リージョンに記事を発行する (書き込む) 必要があります。
* 作成者には、世界中に分散した記事の閲覧者/購読者がいます。 
* 新しい記事が発行されたときに、購読者に通知する必要があります。
* 購読者はローカル リージョンから記事を読み取ることができる必要があります。 また、これらの記事にレビューを追加することもできる必要があります。 
* 記事の作成者を含めたすべてのユーザーが、記事に添付されたすべてのレビューをローカル リージョンから表示できる必要があります。 

数百万人のコンシューマーと発行者が存在し、数十億件の記事があると想定すると、アクセスのローカリティを保証すると共に、スケールの問題に速やかに対処する必要があります。 ほとんどのスケーラビリティの問題と同様に、ソリューションは適切なパーティション分割戦略にあります。 次に、記事、レビュー、通知をドキュメントとしてモデル化し、DocumentDB アカウントを構成して、データ アクセス層を実装する方法を見てみましょう。 

パーティション分割とパーティション キーの詳細については、「[Azure DocumentDB でのパーティション分割とスケーリング](documentdb-partition-data.md)」をご覧ください。

## <a id="ModelingNotifications"></a>通知のモデル化
通知はユーザーに固有のデータ フィードです。 そのため、通知ドキュメントのアクセス パターンは、常に単一ユーザーのコンテキストに存在します。 たとえば、"ユーザーに通知を送信" したり、"特定のユーザーのすべての通知を取得" したりすると考えられます。 そのため、このタイプのパーティション キーの最適な選択肢は `UserId` になります。

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions">サブスクリプションのモデル化</a>
サブスクリプションは、興味がある記事の特定のカテゴリや特定の発行者などのさまざまな条件に合わせて作成できます。 したがって、`SubscriptionFilter` がパーティション キーに適しています。

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles">記事のモデル化</a>
通知によって記事が特定されたら、通常、以降のクエリは `ArticleId` に基づきます。 `ArticleID` をパーティション キーとして選択すると、DocumentDB コレクション内に記事を格納するための最適な配布が実現されます。 

    class Article 
    { 
        // Unique ID for Article public string Id { get; set; }
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews">レビューのモデル化</a>
記事と同様に、レビューのほとんどの書き込みと読み取りは記事のコンテキストで実行されます。 `ArticleId` をパーティション キーとして選択すると、最適に配布され、記事に関連付けられたレビューに効率的にアクセスできます。 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>データ アクセス層のメソッド
次に、実装する必要がある主なデータ アクセス メソッドを見てみましょう。 `ContentPublishDatabase` に必要なメソッドを次に示します。

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>DocumentDB アカウントの構成
ローカルの読み取りと書き込みを保証するには、パーティション キーだけでなく、リージョンへの地理的なアクセス パターンにも基づいてデータをパーティション分割する必要があります。 このモデルは、リージョンごとに geo レプリケートされた Azure DocumentDB データベース アカウントがあることに依存します。 たとえば、2 つのリージョンでのマルチリージョンの書き込みのセットアップは次のとおりです。

| アカウント名 | 書き込みリージョン | 読み取りリージョン |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

次の図は、このセットアップを使用して一般的なアプリケーションで読み取りと書き込みが実行されるしくみを示しています。

![Azure DocumentDB のマルチマスター アーキテクチャ](./media/documentdb-multi-region-writers/documentdb-multi-master.png)

`West US` リージョンで実行されている DAL でクライアントを初期化する方法を示すコード スニペットを次に示します。
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

前のセットアップでは、データ アクセス層は、デプロイされている場所に基づいてすべての書き込みをローカル アカウントに転送できます。 読み取りは、データのグローバル ビューを得るために両方のアカウントから読み取ることによって実行されます。 この方法は必要な数のリージョンに適用できます。 たとえば、3 つの地理的リージョンでのセットアップを次に示します。

| アカウント名 | 書き込みリージョン | 読み取りリージョン 1 | 読み取りリージョン 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>データ アクセス層の実装
次に、2 つの書き込み可能なリージョンでのアプリケーションのデータ アクセス層 (DAL) の実装を見てみましょう。 DAL では次の手順を実装する必要があります。

* アカウントごとに `DocumentClient` の複数のインスタンスを作成します。 2 つのリージョンで、各 DAL インスタンスは `writeClient` と `readClient` を&1; つずつ持ちます。 
* アプリケーションのデプロイされたリージョンに基づいて、`writeclient` と `readClient` のエンドポイントを構成します。 たとえば、`West US` にデプロイされた DAL では、`contentpubdatabase-usa.documents.azure.com` を使用して書き込みを実行します。 `NorthEurope` にデプロイされた DAL では、書き込みに `contentpubdatabase-europ.documents.azure.com` を使用します。

前のセットアップでは、データ アクセス メソッドを実装できます。 書き込み操作では、書き込みが対応する `writeClient` に転送されます。

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

通知とレビューを読み取るには、次のスニペットに示すように、両方のリージョンから読み取り、結果を結合する必要があります。

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

そのため、適切なパーティション キーと静的アカウントベースのパーティション分割を選択すれば、Azure DocumentDB を使用したマルチリージョンのローカル書き込み/読み取りを実現できます。

## <a id="NextSteps"></a>次のステップ
この記事では、サンプル シナリオとしてコンテンツ発行を使用して、グローバルに分散するマルチリージョンの読み取り/書き込みパターンを DocumentDB で使用する方法について説明しました。

* DocumentDB による[グローバル配布](documentdb-distribute-data-globally.md)のサポートのしくみについて確認する
* [Azure DocumentDB の自動および手動フェールオーバー](documentdb-regional-failovers.md)について確認する
* [DocumentDB でのグローバルな整合性](documentdb-consistency-levels.md)について確認する
* [Azure DocumentDB SDK](documentdb-developing-with-multiple-regions.md) を使用して複数のリージョンで開発する
