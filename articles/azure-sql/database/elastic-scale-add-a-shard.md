---
title: エラスティック データベース ツールを使用してシャードを追加する
description: Elastic Scale API を使用して新しいシャードをシャード セットに追加する方法。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 62f7f93b4baeeb4132e867a90e4f911187967f42
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84034753"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Elastic Database ツールを使用してシャードを追加する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>新しい範囲またはキー用のシャードを追加するには

多くの場合、アプリケーションは、既に存在しているシャード マップに対し、新しいキーまたはキー範囲から予想されるデータを処理するために新しいシャードを追加する必要があります。 たとえば、テナント ID によってシャード化されるアプリケーションの場合、新しいテナントに対して新しいシャードをプロビジョニングすることが必要になる場合があります。また、毎月シャード化されるデータの場合、新しい月が始まる前に新しいシャードをプロビジョニングすることが必要になる場合があります。

キー値の新しい範囲が既存のマッピングにまだ含まれていない場合、新しいシャードを追加し、そのシャードに新しいキーまたは範囲を関連付けるのは簡単です。

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>例: シャードとその範囲を既存のシャード マップに追加する

このサンプルでは、TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard)、[.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100)))、CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard))、CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) の各メソッドを使用し、ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) クラスのインスタンスを作成します。 次の例では、範囲 [300, 400) を保持する、**sample_shard_2** という名前のデータベースと、その中の必要なすべてのスキーマ オブジェクトが作成されています。  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

.NET バージョンの場合、代わりに PowerShell を使用して新しいシャード マップ マネージャーを作成することもできます。 [こちら](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)の例を利用できます。

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>既存の範囲の空き部分用のシャードを追加するには

状況によっては、シャードに対する範囲のマッピングを既に実行し、部分的にデータが入力されている場合がありますが、以降のデータを別のシャードに宛てることができます。 たとえば、日単位で範囲のシャーディングを実行し、既に 50 日がシャードに割り当てられているが、24 日目に今後のデータを別のシャードに保存できます。 Elastic Database の[分割/マージ ツール](elastic-scale-overview-split-and-merge.md)でこの操作を実行できますが、データ移動が不要な場合 (たとえば [25、50) 日の範囲のデータはまだ存在していません。これは 25 日の分を含み 50 日の分を除くデータです) は、シャード マップ管理 API を直接使用することで操作全体を実行できます。

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>例: 範囲の分割と新しく追加したシャードへの空き部分の割り当て

"sample_shard_2" という名前のデータベースと、その中のすべての必要なスキーマ オブジェクトが作成されています。  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**重要**:この手法は、更新されるマッピング用の範囲が空であることが確実である場合のみ使用します。  上記の方法では、移動される範囲のデータはチェックされないので、コードにチェックを含めることをお勧めします。  移動される範囲内に行が存在する場合、実際のデータ分布は更新されたシャード マップと一致しなくなります。 このような場合は、 [分割/マージ ツール](elastic-scale-overview-split-and-merge.md) を代わりに使用して操作を行ってください。  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]
