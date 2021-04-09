---
title: 現実の例を使用して Azure Cosmos DB のデータをモデル化およびパーティション分割する
description: Azure Cosmos DB Core API を使用して現実の例をモデル化およびパーティション分割する方法について説明します
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: d2f35ae7a6110acb2ca89bdaeb487eddabf84923
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185820"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>現実の例を使用して Azure Cosmos DB のデータをモデル化およびパーティション分割する方法
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、[データ モデリング](modeling-data.md)、[パーティション分割](partitioning-overview.md)、[プロビジョニング済みスループット](request-units.md)などの Azure Cosmos DB のいくつかの概念を基にして、現実世界のデータ設計に取り組む方法を示します。

通常、リレーショナル データベースで作業している場合は、データ モデルの設計方法についての習慣や直感が築かれているはずです。 Azure Cosmos DB に固有の制約のため (それは Azure Cosmos DB だけが持つ長所でもありますが)、それらのベスト プラクティスのほとんどはうまく流用できず、次善のソリューションになってしまうことがあります。 この記事の目的は、項目のモデリングからエンティティのコロケーションやコンテナーのパーティション分割まで、Azure Cosmos DB で現実のユース ケースをモデル化する完全なプロセスの手順を示すことです。

この記事の概念を示すコミュニティで作成されたソース コードは、[こちら](https://github.com/jwidmer/AzureCosmosDbBlogExample)でダウンロードまたは表示できます。 このコード サンプルは、コミュニティの共同作成者によって提供されたものであり、Azure Cosmos DB チームはそのメンテナンスをサポートしていません。

## <a name="the-scenario"></a>シナリオ

この演習では、"*ユーザー*" が "*投稿*" を作成できるブログ作成プラットフォームのドメインについて考えます。 また、ユーザーは "*いいね!* " を付けたり、投稿に "*コメント*" を追加したりすることもできます。

> [!TIP]
> "*斜体*" で強調されているいくつかの単語は、モデルで操作する必要がある "モノ" の種類を示します。

仕様への要件の追加:

- フロント ページには、最近作成された投稿のフィードが表示されます。
- 1 人のユーザーのすべての投稿、1 つの投稿に対するすべてのコメント、1 つの投稿に対するすべてのいいね! をフェッチできます。
- 投稿と共に、作成者のユーザー名および投稿に付いているコメントといいね! の数が返されます。
- コメントといいね! については、それを作成したユーザーのユーザー名も返されます。
- 一覧表示するときは、投稿の内容の切り詰められた概要だけを表示する必要があります。

## <a name="identify-the-main-access-patterns"></a>主要なアクセス パターンを明らかにする

最初に、ソリューションのアクセス パターンを明らかにすることにより、初期の仕様に構造を提供します。 Azure Cosmos DB のデータ モデルを設計するときは、モデルで対応する必要がある要求を理解し、モデルがそれらの要求に効率的に対応できることを確認することが重要です。

全体的なプロセスをわかりやすくするため、[CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) のボキャブラリを借りて、さまざまな要求をコマンドまたはクエリに分類します。コマンドは書き込み要求 (つまり、システムを更新する意図) であり、クエリは読み取り専用の要求です。

このプラットフォームで公開する必要がある要求の一覧を次に示します。

- **[C1]** ユーザーを作成/編集する
- **[Q1]** ユーザーを取得する
- **[C2]** 投稿を作成/編集する
- **[Q2]** 投稿を取得する
- **[Q3]** ユーザーの投稿を短い形式で一覧表示する
- **[C3]** コメントを作成する
- **[Q4]** 投稿のコメントを一覧表示する
- **[C4]** 投稿に "いいね!" を付ける
- **[Q5]** 投稿のいいね! を一覧表示する
- **[Q6]** 最近作成された *x* 件の投稿を短い形式で一覧表示する (フィード)

この段階では、各エンティティ (ユーザー、投稿など) の内容の詳細については考えていません。 これらのエンティティをテーブル、列、外部キーなどの観点から変換する方法を把握する必要があるので、このステップは、通常、リレーショナル ストアについて設計するときに最初に取り組むことの 1 つです。書き込み時にスキーマが適用されないドキュメント データベースでは、これはそれほど重要なことではありません。

最初からアクセス パターンを明らかにすることが重要である主な理由は、この要求の一覧がテスト スイートになるためです。 データ モデルの作業を繰り返すたびに、各要求を検討してパフォーマンスとスケーラビリティを確認します。 各モデルで使用される要求ユニットを計算して最適化します。 これらのすべてのモデルでは、既定のインデックス作成ポリシーが使用されますが、これは特定のプロパティのインデックスを作成することでオーバーライドすることができ、それによって RU の使用量と待機時間をさらに向上させることができます。

## <a name="v1-a-first-version"></a>V1: 最初のバージョン

2 つのコンテナー `users` と `posts` から始めます。

### <a name="users-container"></a>users コンテナー

このコンテナーにはユーザー項目のみが格納されます。

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

このコンテナーは `id` でパーティション分割します。つまり、そのコンテナー内の各論理パーティションには、1 つの項目だけが格納されます。

### <a name="posts-container"></a>posts コンテナー

このコンテナーでは、投稿、コメント、いいね! をホストします。

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

このコンテナーは `postId` でパーティション分割します。つまり、そのコンテナー内の各論理パーティションには、1 つの投稿、その投稿に対するすべてのコメント、およびその投稿に対するすべてのいいね! が格納されます。

このコンテナーでホストされるエンティティの 3 つの種類を区別するため、このコンテナーに格納される項目に `type` プロパティが導入されていることに注意してください。

また、関連データについては埋め込みではなく参照を選択してあります (これらの概念について詳しくは、[こちらのセクション](modeling-data.md)を確認してください)。これは次のような理由によるものです。

- ユーザーが作成できる投稿の数には上限がない。
- 投稿の長さは任意である。
- 投稿が保持できるコメントといいね! の数に上限はない。
- 投稿自体を更新することなく、投稿にコメントまたはいいね! を追加できるようにしたい。

## <a name="how-well-does-our-model-perform"></a>モデルのパフォーマンスの確認

ここでは、最初のバージョンのパフォーマンスとスケーラビリティを評価します。 前に識別した要求ごとに、その待機時間と、消費される要求ユニットの数を測定します。 この測定に使用するダミー データ セットには、100,000 人のユーザーが含まれ、1 ユーザーあたり 5 ～ 50 件の投稿、1 投稿あたり最大 25 個のコメントと 100 個のいいね! が含まれます。

### <a name="c1-createedit-a-user"></a>[C1] ユーザーを作成/編集する

この要求は、`users` コンテナーの項目を作成または更新するだけなので、簡単に実装できます。 `id` パーティション キーのおかげで、要求はすべてのパーティションにうまく分散します。

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="users コンテナーへの 1 つの項目の書き込み" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 7 ミリ秒 | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] ユーザーを取得する

ユーザーの取得は、`users` コンテナーから対応する項目を読み取ることによって行われます。

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="users コンテナーからの 1 つの項目の取得" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 2 ミリ秒 | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] 投稿を作成/編集する

**[C1]** と同様に、`posts` コンテナーに書き込むことだけが必要です。

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="posts コンテナーへの 1 つの項目の書き込み" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 9 ミリ秒 | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] 投稿を取得する

最初に、`posts` コンテナーから対応するドキュメントを取得します。 ただし、それだけでは不十分です。仕様では、投稿の作成者のユーザー名と、投稿に付いているコメントといいね! の数を集計する必要があります。これには、さらに 3 つの SQL クエリを発行する必要があります。

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="投稿の取得と追加データの集計" border="false":::

各追加クエリでは、それぞれのコンテナーのパーティション キーでフィルター処理を行います。これこそが、パフォーマンスとスケーラビリティを最大にする必要がある部分です。 ただし、最終的には 1 つの投稿を返すために 4 つの操作を実行する必要があるので、その改善は次の繰り返しで行います。

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 9 ミリ秒 | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] ユーザーの投稿を短い形式で一覧表示する

最初に、その特定のユーザーに対応する投稿をフェッチする SQL クエリを使用して、目的の投稿を取得する必要があります。 ただし、作成者のユーザー名およびコメントといいね! の数を集計するため、追加のクエリを発行する必要があります。

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="1 人のユーザーのすべての投稿を取得し、追加データを集計する" border="false":::

この実装では、多くの欠点が示されています。

- 最初のクエリによって返される各投稿に対し、コメントといいね! の数を集計するクエリを発行する必要があります。
- メインのクエリでは `posts` コンテナーのパーティション キーによるフィルター処理が行われないので、ファンアウトとパーティション スキャンがコンテナー全体に対して行われます。

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 130 ミリ秒 | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] コメントを作成する

コメントは、対応する項目を `posts` コンテナーに書き込むことによって作成されます。

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="posts コンテナーへの 1 つの項目の書き込み" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 7 ミリ秒 | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] 投稿のコメントを一覧表示する

最初に、やはりその投稿のすべてのコメントをフェッチするクエリを実行し、コメントごとに個別にユーザー名を集計する必要もあります。

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="1 つの投稿に対するすべてのコメントを取得し、追加データを集計する" border="false":::

メイン クエリはコンテナーのパーティション キーでフィルター処理されますが、ユーザー名の個別集計は全体的なパフォーマンスに悪影響を与えます。 それについては後で改善します。

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 23 ミリ秒 | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] 投稿に "いいね!" を付ける

**[C3]** と同じように、`posts` コンテナーに対応する項目を作成します。

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="posts コンテナーへの 1 つの項目の書き込み" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 6 ミリ秒 | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] 投稿のいいね! を一覧表示する

**[Q4]** と同じように、その投稿に対するいいね! のクエリを実行した後、それらのユーザー名を集計します。

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="1 つの投稿に対するすべてのいいね! を取得し、追加データを集計する" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 59 ミリ秒 | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] 最近作成された x 件の投稿を短い形式で一覧表示する (フィード)

作成日の降順に並べ替えられた `posts` コンテナーのクエリを実行することによって最新の投稿をフェッチした後、各投稿のユーザー名およびコメントといいね! の数を集計します。

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="最新の投稿を取得し、追加データを集計する" border="false":::

やはり、最初のクエリでは `posts` コンテナーのパーティション キーによるフィルター処理は行われないので、コストのかかるファンアウトが発生します。この場合は、はるかに大きい結果セットを対象とし、結果を `ORDER BY` 句で並べ替えるためさらに悪くなり、要求ユニットに関していっそう高価になります。

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 306 ミリ秒 | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>V1 のパフォーマンスの検討

前のセクションで直面したパフォーマンスの問題を調べると、問題には主に 2 つのクラスがあることがわかります。

- 一部の要求では、必要なすべてのデータを収集するため、複数のクエリを発行する必要があります。
- 一部のクエリでは、対象のコンテナーのパーティション キーでフィルター処理されていないため、スケーラビリティを損なうファンアウトが発生します。

これらの問題を解決しましょう。まずは 1 つ目です。

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: 非正規化を導入して読み取りクエリを最適化する

一部のケースで追加の要求を発行する必要があるのは、最初の要求の結果に取得する必要があるすべてのデータが含まれていないためです。 Azure Cosmos DB のような非リレーショナル データ ストアを使用する場合、この種の問題は、一般に、データ セットでデータを非正規化することによって解決します。

この例では、投稿項目を変更し、投稿の作成者のユーザー名、コメントの数、いいね! の数を追加します。

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

また、コメント項目といいね! 項目も変更し、それらを作成したユーザーのユーザー名を追加します。

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>コメントといいね! の数の非正規化

実現したいのは、コメントまたはいいね! を追加するたびに、対応する投稿の `commentCount` または `likeCount` も増分することです。 `posts` コンテナーは `postId` によってパーティション分割されているので、新しい項目 (コメントまたはいいね!) とその対応する投稿は、同じ論理パーティション内に存在します。 その結果、[ストアド プロシージャ](stored-procedures-triggers-udfs.md)を使用して、その操作を実行できます。

そこで、コメントを作成するとき ( **[C3]** )、`posts` コンテナーに新しい項目を追加するだけでなく、そのコンテナーに対して次のストアド プロシージャを呼び出します。

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

このストアド プロシージャは、投稿の ID と新しいコメントの本文をパラメーターとして受け取り、次のことを行います。

- 投稿を取得します
- `commentCount` を増分します
- 投稿を置き換えます
- 新しいコメントを追加します

ストアド プロシージャはアトミック トランザクションとして実行されるので、`commentCount` の値とコメントの実際の数が常に同期します。

新しいいいね! を追加するときも似たストアド プロシージャを呼び出して、`likeCount` を増分します。

### <a name="denormalizing-usernames"></a>ユーザー名の非正規化

ユーザー名の場合は、ユーザーが異なるパーティションに存在するだけでなく、異なるコンテナーに存在しているので、別のアプローチが必要になります。 パーティションとコンテナー間でデータを非正規化する場合は、ソース コンテナーの[変更フィード](change-feed.md)を使用できます。

この例では、`users` コンテナーの変更フィードを使用して、ユーザーが自分のユーザー名を更新するたびに対応します。 それが発生したら、`posts` コンテナーで別のストアド プロシージャを呼び出すことによって、変更を反映します。

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="posts コンテナーへのユーザー名の非正規化" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

このストアド プロシージャは、ユーザーの ID と新しいユーザー名をパラメーターとして受け取り、次のことを行います。

- `userId` と一致するすべての項目をフェッチします (投稿、コメント、いいね!)
- これらの項目ごとに次のことを行います
  - `userUsername` を置き換えます
  - 項目を置き換えます

> [!IMPORTANT]
> このストアド プロシージャを `posts` コンテナーのすべてのパーティションで実行する必要があるため、この操作にはコストがかかります。 ほとんどのユーザーはサインアップ時に適切なユーザー名を選択し、変更することはないものと想定されるので、この更新が実行されることはほとんどありません。

## <a name="what-are-the-performance-gains-of-v2"></a>V2 でのパフォーマンス向上の内容

### <a name="q2-retrieve-a-post"></a>[Q2] 投稿を取得する

これで、非正規化が行われたので、その要求を処理するために必要なのは 1 つの項目をフェッチすることだけです。

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="posts コンテナーからの 1 つの項目の取得" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 2 ミリ秒 | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] 投稿のコメントを一覧表示する

ここでも、ユーザー名をフェッチしていた余分な要求を行わなくてよくなり、パーティション キーでフィルター処理を行う 1 つのクエリだけになります。

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="投稿のすべてのコメントの取得" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 4 ミリ秒 | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] 投稿のいいね! を一覧表示する

いいね! を一覧表示するときもまったく同じ状況です。

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="投稿のすべてのいいね! の取得" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 4 ミリ秒 | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: すべての要求がスケーラブルであることを確認する

全体的なパフォーマンスの向上を調べると、完全に最適化されていない要求がまだ 2 つあります。 **[Q3]** と **[Q6]** です。 これらは、対象コンテナーのパーティション キーでフィルター処理を行っていないクエリに関連する要求です。

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] ユーザーの投稿を短い形式で一覧表示する

この要求は既に、追加のクエリを不要にする V2 で導入された改善によるメリットがあります。

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="ユーザーの投稿を短い形式で一覧表示するクエリを示す図。" border="false":::

しかし、残りのクエリではまだ、`posts` コンテナーのパーティション キーによるフィルター処理が行われていません。

このような状況について考慮する方法は、実際には単純です。

1. この要求では、特定のユーザーに対するすべての投稿をフェッチしたいので、`userId` でフィルター処理を行う "*必要があります*"。
1. それは、`userId` でパーティション分割されていない `posts` コンテナーに対して実行されるため、パフォーマンスがよくありません。
1. 当然、このパフォーマンスの問題を解決するには、`userId` によってパーティション分割されて "*いる*" コンテナーに対して要求を実行します
1. そのようなコンテナーは既にあります。`users` コンテナーです。

そこで、`users` コンテナーに投稿全体を複製することにより、2 番目のレベルの非正規化を導入します。 それを行うことで、パーティション分割されているディメンションが異なるだけの、投稿のコピーを実質的に取得し、その `userId` によっていっそう効率的に取得できるようになります。

`users` コンテナーには 2 種類の項目が含まれるようになっています。

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

以下の点に注意してください。

- ユーザーと投稿を区別するため、ユーザー項目に `type` フィールドが導入されています。
- また、ユーザー項目に `userId` フィールドが追加されています。これは `id` フィールドと重複しますが、`users` コンテナーが (以前のような `id` ではなく) `userId` によってパーティション分割されるようになったため必要です。

その非正規化を実現するには、再び変更フィードを使用します。 今回は、`posts` コンテナーの変更フィードに対応して、新しい投稿または更新された投稿を `users` コンテナーにディスパッチします。 また、投稿の一覧表示では完全なコンテンツを取得する必要はないので、処理で切り詰めることができます。

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="users コンテナーへの投稿の非正規化" border="false":::

これで、クエリを `users` コンテナーにルーティングし、コンテナーのパーティション キーでフィルター処理できるようになりました。

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="ユーザーのすべての投稿の取得" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 4 ミリ秒 | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] 最近作成された x 件の投稿を短い形式で一覧表示する (フィード)

ここでも似た状況に対応する必要があります。V2 で導入された非正規化により、残っていた不要な追加クエリが除去された後も、残りのクエリではコンテナーのパーティション キーによるフィルター処理が行われていません。

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="最近作成された x 件の投稿を短い形式で一覧表示するクエリを示す図。" border="false":::

同じ方法で、この要求のパフォーマンスとスケーラビリティを最大限にするには、1 つのパーティションのみにヒットすることが必要です。 これは、限られた数の項目だけを取得する必要があるので、考えられることです。このブログ作成プラットフォームのホーム ページを設定するには、最新の 100 件の投稿を取得するだけでよく、データ セット全体をページ分割する必要はありません。

そのため、この最後の要求を最適化するには、この要求を処理するためだけに、3 番目のコンテナーを設計に導入します。 その新しい `feed` コンテナーに対して投稿を非正規化します。

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

このコンテナーは `type` によってパーティション分割され、これは常に項目の `post` になります。 そうすることで、このコンテナー内のすべての項目は、必ず同じパーティション内に配置されます。

非正規化を実現するには、その新しいコンテナーに投稿をディスパッチするために前に導入した変更フィード パイプラインでフックすることだけが必要です。 考慮する必要がある 1 つの重要な点は、最新の 100 件の投稿のみが格納されることを確認する必要があることです。そうでない場合は、コンテナーのコンテンツがパーティションの最大サイズを超えて拡大する可能性があります。 これを行うには、ドキュメントがコンテナーに追加されるたびに[事後トリガー](stored-procedures-triggers-udfs.md#triggers)を呼び出します。

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="feed コンテナーへの投稿の非正規化" border="false":::

コレクションを切り詰める事後トリガーの本体を次に示します。

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

最後のステップは、新しい `feed` コンテナーにクエリを再ルーティングすることです。

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="最新の投稿の取得" border="false":::

| **待機時間** | **RU 料金** | **パフォーマンス** |
| --- | --- | --- |
| 9 ミリ秒 | 16.97 RU | ✅ |

## <a name="conclusion"></a>まとめ

設計のさまざまなバージョンに導入したパフォーマンスとスケーラビリティの全体的な向上を調べてみましょう。

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ミリ秒 / 5.71 RU | 7 ミリ秒 / 5.71 RU | 7 ミリ秒 / 5.71 RU |
| **[Q1]** | 2 ミリ秒 / 1 RU | 2 ミリ秒 / 1 RU | 2 ミリ秒 / 1 RU |
| **[C2]** | 9 ミリ秒 / 8.76 RU | 9 ミリ秒 / 8.76 RU | 9 ミリ秒 / 8.76 RU |
| **[Q2]** | 9 ミリ秒 / 19.54 RU | 2 ミリ秒 / 1 RU | 2 ミリ秒 / 1 RU |
| **[Q3]** | 130 ミリ秒 / 619.41 RU | 28 ミリ秒 / 201.54 RU | 4 ミリ秒 / 6.46 RU |
| **[C3]** | 7 ミリ秒 / 8.57 RU | 7 ミリ秒 / 15.27 RU | 7 ミリ秒 / 15.27 RU |
| **[Q4]** | 23 ミリ秒 / 27.72 RU | 4 ミリ秒 / 7.72 RU | 4 ミリ秒 / 7.72 RU |
| **[C4]** | 6 ミリ秒 / 7.05 RU | 7 ミリ秒 / 14.67 RU | 7 ミリ秒 / 14.67 RU |
| **[Q5]** | 59 ミリ秒 / 58.92 RU | 4 ミリ秒 / 8.92 RU | 4 ミリ秒 / 8.92 RU |
| **[Q6]** | 306 ミリ秒 / 2063.54 RU | 83 ミリ秒 / 532.33 RU | 9 ミリ秒 / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>読み取り負荷の高いシナリオの最適化

お気付きかもしれませんが、ここでの作業では、書き込み要求 (コマンド) を犠牲にして、読み取り要求 (クエリ) のパフォーマンスを向上させることに集中しています。 多くの場合、書き込み操作では、変更フィードによって後続の非正規化がトリガーされるようになったため、計算の負荷は大きくなり、実現には時間がかかります。

これは、(多くのソーシャル アプリと同様) ブログ作成プラットフォームでは読み取り負荷が高いという事実によって正当化されます。つまり、対応する必要がある読み取り要求の量は、書き込み要求の量より桁違いに多いのが普通です。 そのため、読み取り要求を低コストで高パフォーマンスにするため、書き込み要求の実行コストを高くすることには意味があります。

ここで行ったものの中で最も極端な最適化を調べた場合、 **[Q6]** は 2000 以上の RU がわずか 17 RU になりました。項目あたり約 10 RU のコストで投稿を非正規化することによって、それを実現しました。 投稿の作成または更新より多くのフィード要求に対応するので、全体的なコスト削減を考えると、この非正規化のコストはごくわずかです。

### <a name="denormalization-can-be-applied-incrementally"></a>非正規化は段階的に適用できる

この記事で調べたスケーラビリティの向上には、データ セット全体のデータの非正規化と複製が含まれます。 これらの最適化を一度に行う必要はないことに注意する必要があります。 パーティション キーでフィルター処理を行うクエリは大きな規模でパフォーマンスが向上しますが、あまり呼び出されない場合、または限られたデータ セットに対して呼び出される場合であれば、クロスパーティション クエリでもまったく問題ありません。 プロトタイプを構築するだけの場合、または小規模の制御されたユーザー ベースで製品を使い始める場合は、おそらく、これらの改善を後回しにすることができます。その場合に重要なことは、モデルのパフォーマンスを[監視](use-metrics.md)して、いつ着手すべきかを判断できるようにすることです。

更新を他のコンテナーに配布するために使用した変更フィードでは、すべての更新が永続的に格納されます。 これにより、コンテナーの作成時からのすべての更新を要求することができ、システムに既に大量のデータが存在する場合であっても、1 回限りのキャッチアップ操作として非正規化されたビューを開始できます。

## <a name="next-steps"></a>次のステップ

実用的なデータ モデリングとパーティション分割に関するこの概要の後は、ここで説明した概念を次の記事で確認できます。

- [データベース、コンテナー、およびアイテムの操作](account-databases-containers-items.md)
- [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
- [Azure Cosmos DB の変更フィード](change-feed.md)
