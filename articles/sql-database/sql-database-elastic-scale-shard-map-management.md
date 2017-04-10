---
title: "Azure SQL Database をスケールアウトする | Microsoft Docs"
description: "Elastic Database クライアント ライブラリの ShardMapManager の使用方法"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 0e9d647a-9ba9-4875-aa22-662d01283439
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: eb5483e497ef1c1a239f207a034eb8c67f485a39
ms.openlocfilehash: c7a46ebf0df6db92d2e66c7523e00c0a574ebf56


---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>シャード マップ マネージャーでデータベースをスケールアウトする
SQL Azure でデータベースを簡単にスケールアウトするには、シャード マップ マネージャーを使用します。 シャード マップ マネージャーは、シャード セット内のすべてのシャード (データベース) についてのグローバル マッピング情報を保持する特殊なデータベースです。 メタデータにより、アプリケーションは **シャーディング キー**の値に基づいて適切なデータベースに接続できます。 さらに、セット内のすべてのシャードには、ローカル シャード データを追跡するマップが含まれます ( **シャードレット**と呼ばれます)。 

![シャード マップの管理](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

これらのマップの構造を理解することは、シャード マップ管理に不可欠です。 そのためには、シャード マップを管理するための [Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)に含まれる [ShardMapManager クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)を使用します。  

## <a name="shard-maps-and-shard-mappings"></a>シャード マップとシャードのマッピング
シャードごとに、作成するシャード マップの種類を選択する必要があります。 何を選択するかはデータベースのアーキテクチャによって異なります。 

1. データベースごとに&1; つのテナント  
2. データベースごとに複数のテナント (2 種類):
   1. リスト マッピング
   2. 範囲マッピング

シングルテナント モデルの場合は、 **リスト マッピング** シャード マップを作成します。 シングルテナント モデルでは、テナントごとに&1; つのデータベースが割り当てられます。 これは、管理が簡単なので、SaaS 開発者に有効なモデルです。

![リスト マッピング][1]

マルチテナント モデルでは、1 つのデータベースに複数のテナントが割り当てられます (そして、テナントのグループを複数のデータベースに分散させることができます)。 各テナントで必要なデータが少ない場合は、このモデルを使用します。 このモデルでは、 **範囲マッピング**を使用してデータベースにテナントの範囲を割り当てます。 

![範囲マッピング][2]

または、 *リスト マッピング* を使用して複数のテナントを&1; つのデータベースに割り当てることにより、マルチテナント データベース モデルを実装できます。 たとえば、ID が 1 と 5 のテナントに関する情報を DB1 に格納し、DB2 にテナント 7 と 10 のデータを格納する、といったことができます。 

![単一 DB 上の複数のテナント][3] 

### <a name="supported-net-types-for-sharding-keys"></a>シャーディング キーでサポートされる .Net 型
Elastic Scale では、シャーディング キーとして次の .NET Framework 型がサポートされます。

* integer
* long
* guid
* byte[]  
* datetime
* timespan
* datetimeoffset

### <a name="list-and-range-shard-maps"></a>リスト シャード マップと範囲シャード マップ
シャード マップは、**個々のシャーディング キー値のリスト**または**シャーディング キー値の範囲**を使用して作成できます。 

### <a name="list-shard-maps"></a>リスト シャード マップ
**シャード**には、**シャードレット**が含まれます。シャードレットとシャードの間のマッピングは、シャード マップによって管理されます。 **リスト シャード マップ**は、シャードレットを識別する個々のキー値と、シャードとして動作するデータベースとの間の関連付けのことです。  **リスト マッピング**は明示的であり、異なるキー値を同じデータベースにマップすることができます。 たとえば、キー 1 がデータベース A にマップし、キー値 3 と 6 の両方がデータベース B を参照します。

| キー | シャードの場所 |
| --- | --- |
| 1 |データベース A |
| 3 |データベース B |
| 4 |データベース C |
| 6 |データベース B |
| ... |... |

### <a name="range-shard-maps"></a>範囲シャード マップ
**範囲シャード マップ**では、キーの範囲がペア **[Low Value, High Value)** によって記述されます。*Low Value* は範囲内の最小キー、*High Value* は範囲を超える最初の値です。 

たとえば、 **[0, 100)** には、0 以上 100 未満のすべての整数が含まれます。 複数の範囲が同じデータベースをポイントでき、隣接していない範囲もサポートされます (たとえば、次の例では、[100,200) と [400,600) は、両方ともデータベース C をポイントしています)。

| キー | シャードの場所 |
| --- | --- |
| [1,50) |データベース A |
| [50,100) |データベース B |
| [100,200) |データベース C |
| [400,600) |データベース C |
| ... |... |

上に示す各テーブルは、 **ShardMap** オブジェクトの概念上の例です。 各行は、個々の **PointMapping** (リスト シャード マップ) オブジェクトまたは **RangeMapping** (範囲シャード マップ) オブジェクトの簡単な例です。

## <a name="shard-map-manager"></a>シャード マップ マネージャー
クライアント ライブラリでは、シャード マップ マネージャーはシャード マップのコレクションです。 **ShardMapManager** インスタンスによって管理されるデータは、次の&3; つの場所に保持されます。 

1. **グローバル シャード マップ (GSM)**: すべてのシャード マップとマッピングのリポジトリとして機能するデータベースを指定します。 この情報を管理するために、特殊なテーブルとストアド プロシージャが自動的に作成されます。 通常、これは小さなデータベースで簡単にアクセスでき、アプリケーションの他の目的には使用されません。 このテーブルは、**__ShardManagement** という名前の特殊なスキーマに含まれます。 
2. **ローカル シャード マップ (LSM)**: シャードとして指定されたすべてのデータベースは、シャードに固有のシャード マップ情報を格納と管理するためのいくつかの小さなテーブルと特殊なストアド プロシージャを含むように変更されます。 この情報は GSM 内の情報を冗長化したもので、これにより、アプリケーションは、GSM に負荷をかけることなくキャッシュされたシャード マップ情報を検証できます。アプリケーションは、LSM を使用して、キャッシュされたマッピングがまだ有効であるかどうかを判定できます。 各シャードの LSM に対応するテーブルも、スキーマ **__ShardManagement** に含まれます。
3. **アプリケーション キャッシュ**: **ShardMapManager** オブジェクトにアクセスする各アプリケーション インスタンスは、そのマッピングのローカル メモリ内キャッシュを保持します。 ここには、最近取得されたルーティング情報が格納されます。 

## <a name="constructing-a-shardmapmanager"></a>ShardMapManager の作成
**ShardMapManager** オブジェクトは、 [ファクトリ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx) パターンを使用して作成されます。 **[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** メソッドは、(サーバー名と、GSM を保持しているデータベースの名前を含む) **ConnectionString** 形式の資格情報を受け取り、**ShardMapManager** のインスタンスを返します。  

**注意**: **ShardMapManager** は、アプリケーションの初期化コード内でアプリケーション ドメインごとに&1; 回だけインスタンス化する必要があります。 同じアプリケーション ドメインで ShardMapManager の追加のインスタンスを作成すると、アプリケーションのメモリ使用率と CPU 使用率が増加します。 **ShardMapManager** には、任意の数のシャード マップを含めることができます。 多くのアプリケーションでは、シャード マップは&1; つあれば十分です。ただし、異なるスキーマ用や一意性を確保する目的のためにデータベースの異なるセットを使用する場合は、複数のシャード マップを使用することをお勧めします。 

次のコードでは、アプリケーションは、 **TryGetSqlShardMapManager メソッド** を使用して既存の [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)を開こうとします。  グローバル **ShardMapManager** (GSM) を表すオブジェクトがデータベース内に存在しない場合、クライアント ライブラリは [CreateSqlShardMapManager メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)を使用してこれを作成します。

    // Try to get a reference to the Shard Map Manager 
     // via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 

別の方法として、Powershell を使用して新しいシャード マップ マネージャーを作成できます。 [こちら](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)の例を利用できます。

## <a name="get-a-rangeshardmap-or-listshardmap"></a>RangeShardMap または ListShardMap の取得
シャード マップ マネージャーを作成した後に、[TryGetRangeShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)、[TryGetListShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)、または [GetShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) メソッドを使用して [RangeShardMap](https://msdn.microsoft.com/library/azure/dn807318.aspx) または [ListShardMap](https://msdn.microsoft.com/library/azure/dn807370.aspx) を取得できます。

    /// <summary>
    /// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
    /// </summary>
    public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
    {
        // Try to get a reference to the Shard Map.
        RangeShardMap<T> shardMap;
        bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

        if (shardMapExists)
        {
            ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
        }
        else
        {
            // The Shard Map does not exist, so create it
            shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
            ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
        }

        return shardMap;
    } 

### <a name="shard-map-administration-credentials"></a>シャード マップ管理資格情報
シャード マップを管理、操作するアプリケーションは、シャード マップを使用して接続をルーティングするアプリケーションとは異なります。 

シャード マップの管理 (シャード、シャード マップ、シャード マッピングの追加または変更など) を実行するには、**ShardMapManager** をインスタンス化する必要があります。そのためには、**GSM データベースとシャードとして機能する各データベースに対する読み取り/書き込み特権**を持つ資格情報を使用します。 この資格情報により、新しいシャードに LSM テーブルが作成されるときだけでなく、シャード マップ情報が入力または変更されたときに GSM と LSM の両方のテーブルに対する書き込みが許可される必要があります。  

「 [Elastic Database クライアント ライブラリへのアクセスに使用する資格情報](sql-database-elastic-scale-manage-credentials.md)」を参照してください。

### <a name="only-metadata-affected"></a>影響を受けるのはメタデータのみ
**ShardMapManager** データを設定または変更するために使用されるメソッドは、シャード自体に格納されているユーザー データを変更しません。 たとえば、**CreateShard**、**DeleteShard**、**UpdateMapping** などのメソッドは、シャード マップ メタデータのみに作用します。 シャードに含まれるユーザー データを削除、追加、変更することはありません。 代わりに、これらのメソッドは、実際のデータベースを作成または削除するために実行される別の操作や、シャード化環境のバランスを再調整するためにシャードで行を移動する操作と組み合わせて使用するように設計されています  (Elastic Database ツールに含まれる**分割/マージ**ツールでは、シャード間の実際のデータ移動のオーケストレーションと共にこれらの API を利用しています)。「[Elastic Database 分割/マージ ツールを使用したスケーリング](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください。

## <a name="populating-a-shard-map-example"></a>シャード マップの設定の例
特定のシャード マップを設定する場合の処理手順を次に示します。 このコードは、次の手順を実行します。 

1. シャード マップ マネージャー内に新しいシャード マップが作成されます。 
2. 2 つの異なるシャードのメタデータがシャード マップに追加されます。 
3. さまざまなキー範囲マッピングが追加され、シャード マップ全体の内容が表示されます。 

このコードは、エラーが発生した場合にメソッドを再実行できるように記述されています。 各要求は、シャードまたはマッピングを作成しようとする前に、それらが既に存在するかどうかをテストします。 次のコードでは、**sample_shard_0**、**sample_shard_1**、**sample_shard_2** という名前のデータベースが、文字列 **shardServer** によって参照されるサーバーに既に作成されていることを想定しています。 

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
                                     shardServer, 
                                     "sample_shard_0"), 
                                     out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
                                            shardServer, 
                                            "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
                                    shardServer, 
                                    "sample_shard_1"), 
                                    out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
                                             shardServer, 
                                            "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                          new RangeMappingCreationInfo<long>
                          (new Range<long>(0, 50), 
                          shard0, 
                          MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 

別の方法として、PowerShell スクリプトを使用して同じ結果を得ることができます。 [こちら](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)で、いくつかのPowerShell のサンプルを入手できます。     

シャード マップを設定した後は、データ アクセス アプリケーションを作成するか、またはマップに適合させることができます。 **マップのレイアウト** を変更する必要があるまで、マップの設定または操作を再度行う必要はありません。  

## <a name="data-dependent-routing"></a>データ依存ルーティング
シャード マップ マネージャーは、ほとんどの場合、アプリに固有のデータ操作を実行するためのデータベース接続を必要とするアプリケーションで使用されます。 これらの接続は、正しいデータベースに関連付けられている必要があります。 これを **データ依存ルーティング**といいます。 このようなアプリケーションの場合は、GSM データベースに対する読み取り専用アクセス権限を持つ資格情報を使用してファクトリからシャード マップ マネージャー オブジェクトをインスタンス化します。 後で個々の接続要求により、適切なシャード データベースに接続するために必要な資格情報が提供されます。

(読み取り専用の資格情報を使用して開かれた **ShardMapManager** を使用する) これらのアプリケーションはマップまたはマッピングを変更できないことに注意してください。 このような操作を行うには、既に説明したように、高度な特権の資格情報を提供する管理操作専用のアプリケーションまたは PowerShell スクリプトを作成します。 「 [Elastic Database クライアント ライブラリへのアクセスに使用する資格情報](sql-database-elastic-scale-manage-credentials.md)」を参照してください。

詳細については、「 [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)」をご覧ください。 

## <a name="modifying-a-shard-map"></a>シャード マップの変更
シャード マップはさまざまな方法で変更できます。 次に示すどのメソッドを使用しでもシャードとそのマッピングを変更できますが、これらによってシャード内のメタデータが物理的に変更されたり、実際のデータベースが作成または削除されたりすることはありません。  次に示すシャード マップの管理操作の中には、物理的にデータを移動したり、シャードとして機能するデータベースを追加または削除する管理操作と組み合わせて使用したりすることが必要になる操作もあります。

これらのメソッドは、シャード化データベース環境内のデータの全体的な分散状況を変更するために使用できるビルド ブロックとして連携します。  

* シャードを追加または削除するには: [Shardmap クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)の **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)** と **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)** を使用します。 
  
    これらの操作を実行するには、サーバーとターゲット シャードを表すデータベースが既に存在している必要があります。 これらのメソッドは、データベース自体には作用せず、シャード マップ内のメタデータのみに作用します。
* シャードにマップされるポイントまたは範囲を作成または削除するには: **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**、[RangeShardMapping](https://msdn.microsoft.com/library/azure/dn807318.aspx) クラスの **[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)**、[ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx) の **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)** を使用します。
  
    多くの異なるポイントまたは範囲を同じシャードにマップできます。 これらのメソッドは、メタデータにのみ作用し、シャードに既に存在するデータには作用しません。 **DeleteMapping** 操作に合わせてデータをデータベースから削除する必要がある場合は、これらのメソッドを組み合わせてこれらの操作を別個に実行する必要があります。  
* 既存の範囲を&2; つに分割する、または隣接する範囲を&1; つにマージするには: **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)** と **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)** を使用します。  
  
    分割操作とマージ操作を行っても、**キー値がマップされているシャードは変更されない**ことに注意してください。 分割操作を実行すると、既存の範囲が&2; つの部分に分割されます。このとき、どちらの部分も同じシャードにマップされたままになります。 マージ操作を実行すると、同じシャードに既にマップされている&2; つの隣接する範囲が&1; つの範囲に結合されます。  シャード間でのポイントまたは範囲自体の移動は、実際のデータの移動と組み合わせて **UpdateMapping** を使用して調整する必要があります。  Elastic Database ツールの一部である **Split/Merge** サービスを使用すると、データの移動が必要な場合にシャード マップの変更をデータの移動と連携させることができます。 
* 個々のポイントまたは範囲を別のシャードに再マップ (移動) するには: **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**の値に基づいて適切なデータベースに接続できます。  
  
    データは **UpdateMapping** 操作に合わせてシャード間で移動することが必要になる場合があるため、これらのメソッドを使いながら移動操作を別個に実行する必要があります。
* マッピングをオンラインまたはオフラインにするには: **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)** と **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)** を使用して、マッピングのオンライン状態を制御します。 
  
    マッピングが "オフライン" 状態のときは、シャード マッピングに対して特定の操作のみを実行できます (**UpdateMapping**、**DeleteMapping** など)。 マッピングがオフラインのとき、そのマッピングに含まれるキーに基づくデータに依存する要求はエラーを返します。 さらに、範囲が初めてオフラインになったときは、変更が加えられている範囲に対するクエリによって一貫性のない結果または不完全な結果が生成されるのを防ぐために、影響を受けるシャードへのすべての接続が自動的に強制終了されます。 

マッピングは、.Net では不変オブジェクトです。  マッピングを変更する上記のすべてのメソッドは、コード内のこれらへの参照もすべて無効にします。 マッピングの状態を変更する一連の操作を実行しやすくするため、マッピングを変更するすべてのメソッドは新しいマッピングの参照を返します。これにより、操作を連結できます。 たとえば、キー 25 を含むシャード マップ sm で既存のマッピングを削除するには、次のように実行できます。 

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## <a name="adding-a-shard"></a>シャードの追加
多くの場合、アプリケーションは、既に存在しているシャード マップに対し、新しいキーまたはキー範囲から期待されるデータを処理するために新しいシャードを単に追加する必要があります。 たとえば、テナント ID によってシャード化されるアプリケーションの場合、新しいテナントに対して新しいシャードをプロビジョニングすることが必要になる場合があります。また、毎月シャード化されるデータの場合、新しい月が始まる前に新しいシャードをプロビジョニングすることが必要になる場合があります。 

キー値の新しい範囲が既に既存のマッピングの一部ではなく、データの移動が不要な場合は、新しいシャードを追加して、新しいキーまたは範囲をそのシャードに関連付けるようにすると簡単に済みます。 新しいシャードを追加する方法の詳細については、「 [Adding a new Shard (新しいシャードの追加)](sql-database-elastic-scale-add-a-shard.md)」をご覧ください。

ただし、データの移動を必要とするシナリオでは、必要なシャード マップの更新と共にシャード間でのデータの移動を調整するために分割/マージ ツールが必要になります。 分割/マージ ツールの使用法の詳細については、「 [Overview of Split-Merge (Split-Merge の概要)](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png



<!--HONumber=Jan17_HO4-->


