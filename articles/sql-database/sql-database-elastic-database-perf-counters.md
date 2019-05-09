---
title: パフォーマンス カウンターを作成してシャード マップ マネージャーのパフォーマンスを追跡する
description: ShardMapManager クラスとデータ依存ルーティング パフォーマンス カウンター
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 5c6c923c86ea0c5968079188c87ec3988ec30142
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494920"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>パフォーマンス カウンターを作成してシャード マップ マネージャーのパフォーマンスを追跡する

パフォーマンス カウンターを使用して、[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)操作のパフォーマンスを追跡します。 これらのカウンターは、パフォーマンス モニターの [エラスティック データベース: シャード管理] カテゴリでアクセスできます。

特に[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)を使用するときに、[シャード マップ マネージャー](sql-database-elastic-scale-shard-map-management.md)のパフォーマンスをキャプチャできます。 カウンターは、Microsoft.Azure.SqlDatabase.ElasticScale.Client クラスのメソッドを使用して作成されます。  


**最新バージョン:**[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) に移動してください。 「 [最新のエラスティック データベース クライアント ライブラリを使用するためのアプリのアップグレード](sql-database-elastic-scale-upgrade-client-library.md)」も参照してください。

## <a name="prerequisites"></a>前提条件

* パフォーマンス カテゴリとカウンターを作成するには、アプリケーションをホストするコンピューター上でユーザーがローカルな **Administrators** グループの一員になっている必要があります。  
* パフォーマンス カウンター インスタンスを作成し、カウンターを更新するには、ユーザーが **Administrators** グループまたは **Performance Monitor Users** グループの一員になっている必要があります。

## <a name="create-performance-category-and-counters"></a>パフォーマンス カテゴリとカウンターを作成する

カウンターを作成するには、[ShardMapManagementFactory クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)の CreatePerformanceCategoryAndCounters メソッドを呼び出します。 このメソッドは、管理者だけが実行できます。

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

[この](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell スクリプトを使用してメソッドを実行することもできます。
このメソッドは、次のパフォーマンス カウンターを作成します。  

* **キャッシュされたマッピング**: シャード マップ用にキャッシュされたマッピングの数です。
* **DDR 操作数/秒**: シャード マップ用のデータ依存ルーティング操作の割合です。 このカウンターは、[OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) に対する呼び出しで宛先のシャードへの接続が成功したときに更新されます。
* **マッピングのキャッシュ検索ヒット数/秒**: シャード マップでのマッピングで成功したキャッシュ検索操作の割合です。
* **マッピングのキャッシュ検索ミス数/秒**: シャード マップでのマッピングで失敗したキャッシュ検索操作の割合です。
* **キャッシュに追加または更新されたマッピング数/秒**: シャード マップ用のキャッシュに追加または更新されたマッピングの割合です。
* **キャッシュから削除されたマッピング数/秒**: シャード マップ用のキャッシュから削除されたマッピングの割合です。

パフォーマンス カウンターは、それぞれのキャッシュされたシャード マップで、プロセスごとに作成されます。  

## <a name="notes"></a>メモ

次のイベントは、パフォーマンス カウンターの作成をトリガーします。  

* [一括読み込み](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)による [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy) の初期化 (ShardMapManager にシャード マップが含まれている場合)。 [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) メソッドと [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) メソッドを含みます。
* シャード マップの検索の成功 ([GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx)、[GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx)、または [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx) を使用)。
* CreateShardMap() を使用したシャード マップの作成の成功。

パフォーマンス カウンターは、シャード マップとマッピングに対して実行されるすべてのキャッシュ操作によって更新されます。 DeleteShardMap () を使用してシャード マップが正常に削除されると、パフォーマンス カウンター インスタンスも削除されます。  

## <a name="best-practices"></a>ベスト プラクティス

* パフォーマンス カテゴリとカウンターの作成は、ShardMapManager オブジェクトの作成前に 1 回だけ実行する必要があります。 CreatePerformanceCategoryAndCounters() コマンドを実行するたびに前のカウンターがクリアされ (すべてのインスタンスによって報告されたデータが失われ)、新しいカウンターが作成されます。  
* パフォーマンス カウンター インスタンスは、プロセスごとに作成されます。 パフォーマンス カウンター インスタンスは、アプリケーションがクラッシュするか、シャード マップがキャッシュから削除されると削除されます。  

### <a name="see-also"></a>関連項目

[Elastic Database 機能の概要](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
