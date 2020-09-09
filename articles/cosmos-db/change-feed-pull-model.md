---
title: 変更フィードのプル モデル
description: Azure Cosmos DB の変更フィード プル モデルを使用して、変更フィード、およびプル モデルと変更フィード プロセッサの違いを読み取る方法について説明します。
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/19/2020
ms.reviewer: sngun
ms.openlocfilehash: 8916f4b9824f88361fdeb9d866f84adb71e8138e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563794"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB の変更フィード プル モデル

変更フィード プル モデルを使用すると、Azure Cosmos DB の変更フィードを自分のペースで使用できます。 [変更フィード プロセッサ](change-feed-processor.md)で既に行えているのと同様に、変更フィード プル モデルを使用して、複数の変更フィード コンシューマーの変更処理を並列化することができます。

> [!NOTE]
> 変更フィード プル モデルは、現時点では、[Azure Cosmos DB .NET SDK でのみプレビュー](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)段階です。 このプレビューは、他の SDK バージョンではまだ使用できません。

## <a name="consuming-an-entire-containers-changes"></a>コンテナーの変更全体を使用する

プル モデルを使用して変更フィードを処理するために、`FeedIterator` を作成できます。 最初に `FeedIterator` を作成するときに、オプションの `StartTime` を `ChangeFeedRequestOptions` 内で指定できます。 指定しない場合、`StartTime` は現在の時刻になります。

`FeedIterator` には 2 つの種類があります。 以下の例ではエンティティ オブジェクトを返しますが、それに加えて、`Stream` のサポートを使用して応答を取得することもできます。 ストリームを使用すると、最初にデータを逆シリアル化してクライアント リソースに保存しなくても、データを読み取ることができます。

次に、エンティティ オブジェクト (この例では `User` オブジェクト) を返す `FeedIterator` を取得する例を示します。

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

次に、`Stream` を返す `FeedIterator` を取得する例を示します。

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

`FeedIterator` を使用すると、コンテナーの変更フィード全体を自分のペースで簡単に処理できます。 次に例を示します。

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator<User>();

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>パーティション キーの変更を使用する

特定のパーティション キーの変更のみを処理することが必要になる場合があります。 特定のパーティション キーの `FeedIterator` を取得し、その変更をコンテナー全体の場合と同じ方法で処理することができます。

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator<User>(new PartitionKey("myPartitionKeyValueToRead"));

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>並列処理のために FeedRange を使用する

[変更フィード プロセッサ](change-feed-processor.md)では、作業は複数のコンシューマーに自動的に分散されます。 変更フィード プル モデルでは、`FeedRange` を使用して、変更フィードの処理を並列化できます。 `FeedRange` は、パーティション キー値の範囲を表します。

次に、コンテナーの範囲の一覧を取得する方法の例を示します。

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

コンテナーの FeedRange の一覧を取得すると、[物理パーティション](partition-data.md#physical-partitions)ごとに 1 つの `FeedRange` が得られます。

次に、`FeedRange` を使用して `FeedIterator` を作成することにより、複数のマシンやスレッドの変更フィードの処理を並列化できます。 コンテナー全体のために 1 つの `FeedIterator` を取得する方法を示した前の例とは異なり、`FeedRange` を使用すると、変更フィードを並列処理できる複数の FeedIterator を取得できます。

FeedRange を使用する場合は、FeedRange を取得して対象のマシンに配布するオーケストレーター プロセスが必要になります。 この配布は次のように行います。

* `FeedRange.ToJsonString` を使用して、この文字列値を配布する。 コンシューマーは、この値を `FeedRange.FromJsonString` で使用できます。
* 配布がインプロセスの場合は、`FeedRange` オブジェクト参照を渡す。

並列で読み取りを行う 2 台の別個の仮定のマシンを使用して、コンテナーの変更フィードを最初から読み取る方法を示すサンプルを次に示します。

マシン 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

マシン 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>継続トークンを保存する

継続トークンを作成すれば、`FeedIterator` の位置を保存できます。 継続トークンとは、FeedIterator が最後に処理した変更を追跡し続ける文字列値のことです。 これにより、後で `FeedIterator` による処理をこの位置から再開できるようになります。 次のコードでは、コンテナーの作成以降の変更フィードを読み取ります。 それ以上読み取るべき変更がなくなると、変更フィードの使用を後で再開できるように継続トークンが保持されます。

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

Cosmos コンテナーが存在する限り、FeedIterator の継続トークンが期限切れになることはありません。

## <a name="comparing-with-change-feed-processor"></a>変更フィード プロセッサとの比較

[変更フィード プロセッサ](change-feed-processor.md)とプル モデルのどちらかを使用することにより、多くのシナリオで変更フィードを処理できます。 プル モデルの継続トークンと変更フィード プロセッサのリース コンテナーは、どちらも変更フィードで最後に処理された項目 (または項目のバッチ) の "ブックマーク" になります。
しかし、継続トークンをリース コンテナー (またはその逆) に変換することはできません。

次のシナリオでは、プル モデルの使用を検討することをお勧めします。

- 特定のパーティション キーから変更を読み取る
- クライアントが処理のために変更を受け取るペースを制御する
- 変更フィードの既存のデータを 1 回だけ読み取る (データ移行を行う場合など)

変更フィード プロセッサとプル モデルの主な違いを次に示します。

|特徴量  | 変更フィード プロセッサ| プル モデル |
| --- | --- | --- |
| 変更フィード処理の現在位置の追跡 | リース (Azure Cosmos DB コンテナーに保存) | 継続トークン (メモリに保存または手動で保持) |
| 過去の変更の再生機能 | あり。プッシュ モデルを使用 | あり。プル モデルを使用|
| 将来の変更のポーリング | ユーザー指定の `WithPollInterval` に基づき変更を自動チェック | マニュアル |
| コンテナー全体からの変更処理 | 可能。同一のコンテナーから使用している複数のスレッドまたはマシンの処理を自動的に並列化| 可能。FeedTokens を使用して手動で並列化 |
| 単一のパーティション キーのみからの変更処理 | サポートされていません | はい|
| サポート レベル | 一般公開 | プレビュー |

## <a name="next-steps"></a>次のステップ

* [変更フィードの概要](change-feed.md)
* [変更フィード プロセッサの使用](change-feed-processor.md)
* [Azure Functions をトリガーする](change-feed-functions.md)