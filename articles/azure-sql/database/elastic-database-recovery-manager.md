---
title: Recovery Manager でシャード マップに関する問題を解決する
description: RecoveryManager クラスを使用したシャード マップに関する問題の解決
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91bcd998849c619a328a198c97bb8c977b9d8232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792227"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>RecoveryManager クラスを使用したシャード マップに関する問題の解決
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[RecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) クラスを使用すると、ADO.NET アプリケーションで、シャード化されたデータベース環境におけるグローバル シャード マップ (GSM) とローカル シャード マップ (LSM) 間の不整合を簡単に検出して修正できます。

GSM と LSM はシャード化環境内の各データベースのマッピングを追跡します。 場合によっては、GSM と LSM の間で断絶が発生します。 その場合は、RecoveryManager クラスを使用して断絶を検出し、修復します。

RecoveryManager クラスは、 [Elastic Database クライアント ライブラリ](elastic-database-client-library.md)に含まれています。

![シャード マップ][1]

用語の定義については、「 [Elastic Database ツールの用語集](elastic-scale-glossary.md)」を参照してください。 **ShardMapManager** を使用してシャーディング ソリューション内のデータを管理する方法については、「 [シャード マップの管理](elastic-scale-shard-map-management.md)」を参照してください。

## <a name="why-use-the-recovery-manager"></a>Recovery Manager を使用する理由

シャード化データベース環境では、データベースごとに 1 つのテナントがあり、サーバーごとに多くのデータベースがあります。 また、環境に多くのサーバーが存在する場合もあります。 各データベースはシャード マップでマッピングされるため、呼び出しを適切なサーバーとデータベースにルーティングできます。 データベースは、**シャーディング キー** に従って追跡されます。各シャードには **キー値の範囲** が割り当てられます。 たとえば、あるシャーディング キーは、"D" ～ "F" の顧客名を表します。 すべてのシャード (データベースとも呼ばれます) のマッピングとそのマッピング範囲は、**グローバル シャード マップ (GSM)** に保持されます。 各データベースには、シャードに属している範囲のマップも保持されます。これを **ローカル シャード マップ (LSM)** と呼びます。 アプリがシャードに接続すると、すばやく取得できるようにマッピングがアプリにキャッシュされます。 LSM は、キャッシュされたデータの検証に使用されます

GSM と LSM は、次の理由により、同期されなくなる可能性があります。

1. 使用されていないと思われる範囲のシャードの削除またはシャードの名前変更 シャードを削除すると、**孤立したシャード マッピング** が発生します。 同様に、データベースの名前を変更した場合も、孤立したシャード マッピングが発生します。 変更の目的に応じて、シャードの削除が必要になることもあれば、シャードの場所を更新するだけで済むこともあります。 削除されたデータベースを回復するには、[削除されたデータベースの復元](recovery-using-backups.md)に関する記事を参照してください。
2. geo フェールオーバー イベントの発生。 続行するには、アプリケーションのシャード マップ マネージャーのサーバー名とデータベース名を更新し、シャード マップ内のすべてのシャードのシャード マッピングの詳細を更新する必要があります。 geo フェールオーバーが発生する場合は、フェールオーバー ワークフロー内でこのような復旧ロジックを自動化しておく必要があります。 復旧操作を自動化すると、geo 対応データベースを円滑に管理でき、ユーザーによる手動操作もなくすことができます。 データ センターの停止が発生した場合にデータベースを復元するオプションの詳細については、[ビジネス継続性](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関する記事および[ディザスター リカバリー](disaster-recovery-guidance.md)に関する記事をご覧ください。
3. シャード化データベースまたは ShardMapManager データベースが以前の時点に復元された。 バックアップを使った特定の時点への復旧については、[バックアップを使用した復旧](recovery-using-backups.md)に関する記事をご覧ください。

Azure SQL Database の Elastic Database ツール、geo レプリケーション、および復元の詳細については、以下をご覧ください。

* [概要:SQL Database を使用したクラウド ビジネス継続性とデータベース ディザスター リカバリー](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [エラスティック データベース ツールの概要](elastic-scale-get-started.md)  
* [ShardMap 管理](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>RecoveryManager からの ShardMapManager の取得

最初の手順は、RecoveryManager インスタンスの作成です。 [GetRecoveryManager メソッド](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager)を実行すると、現在の [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) インスタンスのリカバリ マネージャーが返されます。 シャード マップ内の不整合を解決するには、最初に、特定のシャード マップの RecoveryManager を取得する必要があります。

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

この例では、RecoveryManager が ShardMapManager から初期化されています。 ShardMap が含まれた ShardMapManager も既に初期化されています。

このアプリケーション コードではシャード マップを直接操作しないため、ファクトリ メソッドで使われる資格情報 (前の例では smmConnectionString) は、接続文字列が参照する GSM データベースに対する読み取り/書き込みアクセス許可を持つ資格情報である必要があります。 これらの資格情報は、通常、データ依存ルーティングの接続で使用される資格情報とは異なります。 詳細については、 [エラスティック データベース クライアントにおける資格情報の使用](elastic-scale-manage-credentials.md)に関するページを参照してください。

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>シャードを削除した後の ShardMap からのシャードの削除

[DetachShard メソッド](/previous-versions/azure/dn842083(v=azure.100)) を実行すると、シャード マップから特定のシャードがデタッチされ、そのシャードに関連付けられたマッピングが削除されます。  

* Location パラメーターはシャードの場所、具体的には、デタッチするシャードのサーバー名とデータベース名です。
* shardMapName パラメーターは、シャード マップの名前です。 これは、複数のシャード マップが同じシャード マップ マネージャーによって管理されている場合のみ必須です。 省略可能。

> [!IMPORTANT]
> この手法は、更新されるマッピング用の範囲が空であることが確実である場合のみ使用します。 上記の方法では、移動される範囲のデータはチェックされないため、コード内にチェックを含めることが最善です。

次の例では、シャードをシャード マップから削除します。

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

シャード マップには、シャードを削除する前の GSM 内におけるシャードの場所が反映されます。 シャードが削除されたため、これは意図的な操作であり、シャーディング キー範囲が使用されなくなったと見なされます。 そうでない場合は、ポイント イン タイム リストアを実行して 過去の特定の時点からシャードを回復できます。 その場合、以下のセクションで、シャードの不整合の検出について確認してください。回復するには、[Point in time recovery (特定の時点への復旧)](recovery-using-backups.md) に関する記事を参照してください。

データベースの削除は意図的な操作であることを前提とするため、最終的なクリーンアップ管理操作は、シャード マップ マネージャーでシャードのエントリを削除することです。 これによって、アプリケーションが誤って予期しない範囲に情報を書き込むことがなくなります。

## <a name="to-detect-mapping-differences"></a>マッピングの相違点を検出するには

[DetectMappingDifferences メソッド](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) を実行すると、シャード マップ (ローカルとグローバルのいずれか) のうち 1 つが唯一の情報源として選択されて返され、両方のシャード マップ (GSM および LSM) でマッピングが調整されます。

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *location* では、サーバー名とデータベース名を指定します。
* *shardMapName* パラメーターは、シャード マップの名前です。 これは、複数のシャード マップが同じシャード マップ マネージャーによって管理されている場合のみ必須です。 省略可能。

## <a name="to-resolve-mapping-differences"></a>マッピングの相違点を解決するには

[ResolveMappingDifferences メソッド](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) を実行すると、シャード マップ (ローカルとグローバルのいずれか) のうち 1 つが唯一の情報源として選択され、両方のシャード マップ (GSM および LSM) でマッピングが調整されます。

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* パラメーターには、特定のシャードの GSM と LSM のマッピングの相違点を列挙します。
* [MappingDifferenceResolution 列挙体](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) は、シャード マッピングの相違点を解決するためのメソッドを指定するために使用します。
* LSM に正確なマッピングが含まれていて、そのため、シャード内のマッピングを使う必要のあるイベントでは、**MappingDifferenceResolution.KeepShardMapping** を使うことをお勧めします。 これは通常、フェールオーバーが発生した場合に当てはまります。そのような状況では、新しいサーバーにシャードが存在します。 最初に (RecoveryManager.DetachShard メソッドを使用して) GSM からシャードを削除する必要があるため、マッピングは GSM には存在しなくなります。 そのため、LSM を使って、シャード マッピングを再確立する必要があります。

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>シャードを復元した後の ShardMap へのシャードのアタッチ

[AttachShard メソッド](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) を実行すると、特定のシャードがシャード マップにアタッチされます。 その後、シャード マップの不整合が検出され、復元された時点のシャードと一致するようにマッピングが更新されます。 ポイントインタイム リストアでは既定で新しいデータベースにタイムスタンプが付加されるため、(シャードの復元の実行前に) 元のデータベース名を反映するようにデータベース名が変更されていることを前提とします。

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* *location* パラメーターは、アタッチするシャードのサーバー名とデータベース名です。
* *shardMapName* パラメーターは、シャード マップの名前です。 これは、複数のシャード マップが同じシャード マップ マネージャーによって管理されている場合のみ必須です。 省略可能。

この例では、以前の時点から最近復元されたシャード マップにシャードを追加します。 シャード (厳密には LSM 内のシャードのマッピング) が復元されたため、GSM 内のシャード エントリと不整合が生じている可能性があります。 このコード例では、外部でシャードが復元され、データベースの元の名前に変更されています。 LSM 内のマッピングは復元されたため、信頼されたマッピングであると見なします。

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>シャードの geo フェールオーバー (復元) の後のシャードの場所の更新

geo フェールオーバーが発生する場合は、セカンダリ データベースが書き込みアクセス許可を与えられて、新しいプライマリ データベースになります。 サーバーの名前が、場合によっては (構成による) データベースの名前も、元のプライマリと異なっている今年があります。 そのため、GSM と LSM のシャードのマッピング エントリを修正する必要があります。 同様に、データベースを別の名前、別の場所、または以前の時点に復元した場合も、シャード マップで不整合が生じている可能性があります。 適切なデータベースへのオープンな接続の分配は、シャード マップ マネージャーが処理します。 分配は、シャード マップ内のデータと、アプリケーション要求の対象であるシャーディング キーに基づいて行われます。 geo フェールオーバー後は、この情報を、回復されたデータベースの正確なサーバー名、データベース名、およびシャード マッピングを使用して更新する必要があります。

## <a name="best-practices"></a>ベスト プラクティス

geo フェールオーバーと復旧は一般的に、アプリケーションのクラウド管理者が Azure SQL Database のビジネス継続性機能を意図的に使用して管理する操作です。 ビジネス継続性の計画には、ビジネス運営を中断なく確実に続行できるプロセス、手順、手段が必要です。 実行する復旧アクションに基づいて GSM と LSM が最新の状態に維持されるように、RecoveryManager クラスの一部として提供されているメソッドをこのワークフロー内で使用してください。 フェールオーバー後に GSM と LSM に正確な情報が反映されていることを正しく確認するには、次の 5 つの基本的な手順を実行します。 これらの手順を実行するアプリケーション コードを、既存のツールやワークフローに統合できます。

1. Recovery Manager を ShardMapManager から取得します。
2. シャード マップから使用しなくなったシャードをデタッチします。
3. 新しいシャードの場所を含むシャード マップに、新しいシャードをアタッチします。
4. GSM と LSM のマッピングの不整合が検出されます。
5. GSM と LSM の相違点を、LSM を信頼して解決します。

この例では、次の手順を実行します。

1. フェールオーバーの前に、シャードの場所が反映されているシャード マップからシャードを削除します。
2. 新しいシャード場所が ("Configuration.SecondaryServer" パラメーターは、サーバー名は新しく、データベース名は同じです) が反映されているシャード マップにシャードをアタッチします。
3. 各シェードの GSM と LSM のマッピングの相違点を検出して、回復トークンを取得します。
4. 各シャードの LSM のマッピングを信頼して、不整合を解決します。

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png