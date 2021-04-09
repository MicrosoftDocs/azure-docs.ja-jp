---
title: シャード マップ マネージャーを追跡するパフォーマンス カウンター
description: ShardMapManager クラスとデータ依存ルーティング パフォーマンス カウンター
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: 3bfbf56b6e5f2be33b407945490531e6e2e8ac47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92781262"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>パフォーマンス カウンターを作成してシャード マップ マネージャーのパフォーマンスを追跡する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

パフォーマンス カウンターを使用して、[データ依存ルーティング](elastic-scale-data-dependent-routing.md)操作のパフォーマンスを追跡します。 これらのカウンターは、パフォーマンス モニターの [エラスティック データベース: シャード管理] カテゴリでアクセスできます。

特に[データ依存ルーティング](elastic-scale-data-dependent-routing.md)を使用するときに、[シャード マップ マネージャー](elastic-scale-shard-map-management.md)のパフォーマンスをキャプチャできます。 カウンターは、Microsoft.Azure.SqlDatabase.ElasticScale.Client クラスのメソッドを使用して作成されます。  


**最新バージョン:** [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) に移動してください。 「 [最新のエラスティック データベース クライアント ライブラリを使用するためのアプリのアップグレード](elastic-scale-upgrade-client-library.md)」も参照してください。

## <a name="prerequisites"></a>前提条件

* パフォーマンス カテゴリとカウンターを作成するには、アプリケーションをホストするコンピューター上でユーザーがローカルな **Administrators** グループの一員になっている必要があります。  
* パフォーマンス カウンター インスタンスを作成し、カウンターを更新するには、ユーザーが **Administrators** グループまたは **Performance Monitor Users** グループの一員になっている必要があります。

## <a name="create-performance-category-and-counters"></a>パフォーマンス カテゴリとカウンターを作成する

カウンターを作成するには、[ShardMapManagementFactory クラス](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)の CreatePerformanceCategoryAndCounters メソッドを呼び出します。 このメソッドは、管理者だけが実行できます。

`ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()`

[この](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell スクリプトを使用してメソッドを実行することもできます。
このメソッドは、次のパフォーマンス カウンターを作成します。  

* **キャッシュされたマッピング**: シャード マップ用にキャッシュされたマッピングの数です。
* **DDR 操作数/秒**: シャード マップ用のデータ依存ルーティング操作の割合です。 このカウンターは、[OpenConnectionForKey()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) に対する呼び出しで宛先のシャードへの接続が成功したときに更新されます。
* **マッピングのキャッシュ検索ヒット数/秒**: シャード マップでのマッピングで成功したキャッシュ検索操作の割合です。
* **マッピングのキャッシュ検索ミス数/秒**: シャード マップでのマッピングで失敗したキャッシュ検索操作の割合です。
* **キャッシュに追加または更新されたマッピング数/秒**: シャード マップ用のキャッシュに追加または更新されたマッピングの割合です。
* **キャッシュから削除されたマッピング数/秒**: シャード マップ用のキャッシュから削除されたマッピングの割合です。

パフォーマンス カウンターは、それぞれのキャッシュされたシャード マップで、プロセスごとに作成されます。  

## <a name="notes"></a>Notes

次のイベントは、パフォーマンス カウンターの作成をトリガーします。  

* [一括読み込み](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)による [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy) の初期化 (ShardMapManager にシャード マップが含まれている場合)。 [GetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) メソッドと [TryGetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) メソッドを含みます。
* シャード マップの検索の成功 ([GetShardMap()](/previous-versions/azure/dn824215(v=azure.100))、[GetListShardMap()](/previous-versions/azure/dn824212(v=azure.100))、または [GetRangeShardMap()](/previous-versions/azure/dn824173(v=azure.100)) を使用)。
* CreateShardMap() を使用したシャード マップの作成の成功。

パフォーマンス カウンターは、シャード マップとマッピングに対して実行されるすべてのキャッシュ操作によって更新されます。 DeleteShardMap () を使用してシャード マップが正常に削除されると、パフォーマンス カウンター インスタンスも削除されます。  

## <a name="best-practices"></a>ベスト プラクティス

* パフォーマンス カテゴリとカウンターの作成は、ShardMapManager オブジェクトの作成前に 1 回だけ実行する必要があります。 CreatePerformanceCategoryAndCounters() コマンドを実行するたびに前のカウンターがクリアされ (すべてのインスタンスによって報告されたデータが失われ)、新しいカウンターが作成されます。  
* パフォーマンス カウンター インスタンスは、プロセスごとに作成されます。 パフォーマンス カウンター インスタンスは、アプリケーションがクラッシュするか、シャード マップがキャッシュから削除されると削除されます。  

### <a name="see-also"></a>関連項目

[Elastic Database 機能の概要](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->