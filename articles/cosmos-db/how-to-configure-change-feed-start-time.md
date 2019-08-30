---
title: 変更フィード プロセッサの開始時刻の構成方法 - Azure Cosmos DB
description: 特定の日時から読み取りを開始するよう変更フィード プロセッサを構成する方法を説明します
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093645"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>変更フィード プロセッサの開始時刻の構成方法

この記事では、特定の日時から読み取りを開始するよう[変更フィード プロセッサ](./change-feed-processor.md)を構成する方法を説明します。

## <a name="default-behavior"></a>既定の動作

変更フィード プロセッサは、最初に開始したときに、リース コンテナーを初期化し、その[処理のライフ サイクル](./change-feed-processor.md#processing-life-cycle)を開始します。 変更フィード プロセッサが初めて初期化される前にコンテナー内で発生した変更が検出されることはありません。

## <a name="reading-from-a-previous-date-and-time"></a>以前の日時からの読み取り

`DateTime` のインスタンスを `WithStartTime` ビルダー拡張機能に渡すことで、**特定の日時**以降の変更を読み取るよう変更フィード プロセッサを初期化することができます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

変更フィード プロセッサは、その特定の日時に対して初期化され、それ以降に発生した変更の読み取りを開始します。

## <a name="reading-from-the-beginning"></a>最初からの読み取り

データの移行やコンテナーの履歴全体の分析など、他のシナリオでは、**そのコンテナーの有効期間の最初**から変更フィードを読み取る必要があります。 これを行うために、ビルダー拡張機能で `WithStartTime` を使用できますが、`DateTime.MinValue.ToUniversalTime()` を渡すと、次のように `DateTime` の最小値の UTC 表現が生成されます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

この変更フィード プロセッサは初期化され、コンテナーの有効期間の最初から変更の読み取りを開始します。

> [!NOTE]
> これらのカスタマイズ オプションは、変更フィード プロセッサの開始時点を設定するためだけに機能します。 リース コンテナーが初めて初期化された後、それらを変更しても影響はありません。

> [!NOTE]
> 特定の時点を指定すると、コンテナー内に現在存在する項目に対する変更のみが読み取られます。 項目が削除された場合は、変更フィード上のその履歴も削除されます。

## <a name="additional-resources"></a>その他のリソース

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub の使用例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub のその他のサンプル](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>次の手順

以下の記事で、変更フィード プロセッサに関してさらに詳しく知ることができます。

* [変更フィード プロセッサの概要](change-feed-processor.md)
* [変更フィード推定機能の使用](how-to-use-change-feed-estimator.md)
