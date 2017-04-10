---
title: "T-SQL: Azure SQL Database のエラスティック プールを管理する | Microsoft Docs"
description: "T-SQL を使って Azure SQL Database のエラスティック プールを管理します。"
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: a3acd5f4ec63061254b550737ae9fb4d39b343c6
ms.lasthandoff: 03/10/2017


---
# <a name="monitor-and-manage-an-elastic-pool-with-transact-sql"></a>Transact-SQL を使用したエラスティック プールの監視と管理
このトピックでは、Transact-SQL を使用してスケーラブルな[エラスティック プール](sql-database-elastic-pool.md)を管理する方法について説明します。  Azure エラスティック プールは、[Azure Portal](https://portal.azure.com/)、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、REST API、[C#](sql-database-elastic-pool-manage-csharp.md) を使用して作成および管理することもできます。 また、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md) を使用して、データベースを作成したり、エラスティック プールに出し入れしたりすることもできます。


データベースをエラスティック プールに作成したりエラスティック プールから移動したりするには、[Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) コマンドと [Alter Database (Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) コマンドを使います。 これらのコマンドを使用する前に、エラスティック プールが存在している必要があります。 これらのコマンドは、データベースのみに作用します。 T-SQL コマンドを使用して新しいプールを作成したりプールのプロパティ (最小 eDTU、最大 eDTU など) の設定を変更したりすることはできません。

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>エラスティック プールにプールされるデータベースの作成
CREATE DATABASE コマンドと共に SERVICE_OBJECTIVE オプションを使用します。   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in an elastic named S3M100.

エラスティック プール内のすべてのデータベースが、エラスティック プールのサービス レベル (Basic、Standard、Premium) を継承します。 

## <a name="move-a-database-between-elastic-pools"></a>エラスティック プール間でのデータベースの移動
ALTER DATABASE コマンドと共に MODIFY を使用します。SERVICE\_OBJECTIVE オプションに ELASTIC\_POOL を設定します。 name にターゲット プールの名前を設定します。

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to an elastic named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>エラスティック プールへのデータベースの移動
ALTER DATABASE コマンドと共に MODIFY を使用します。SERVICE\_OBJECTIVE オプションに ELASTIC_POOL を設定します。 name にターゲット プールの名前を設定します。

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to an elastic named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>エラスティック プールからのデータベースの移動
ALTER DATABASE コマンドを使用し、SERVICE_OBJECTIVE をいずれかのパフォーマンス レベル (S0、S1 など) に設定します。

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>エラスティック プール内のデータベースの一覧表示
エラスティック プール内のデータベースを一覧表示するには、[sys.database\_service \_objectives ビュー](https://msdn.microsoft.com/library/mt712619)を使用します。 ビューを照会するためにマスター データベースにログインします。

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>エラスティック プールのリソース使用状況データの取得
論理サーバーのエラスティック プールのリソース使用量の統計を確認するには、[sys.elastic\_pool \_resource \_stats ビュー](https://msdn.microsoft.com/library/mt280062.aspx)を使用します。 ビューを照会するためにマスター データベースにログインします。

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-a-pooled-database"></a>プールされているデータベースのリソース使用状況を取得する
エラスティック プール内のデータベースのリソース使用量の統計を確認するには、[sys.dm\_ db\_ resource\_stats ビュー](https://msdn.microsoft.com/library/dn800981.aspx)または [sys.resource \_stats ビュー](https://msdn.microsoft.com/library/dn269979.aspx)を使用します。 このプロセスは、1 つのデータベースのリソース使用量を照会する操作に似ています。

## <a name="next-steps"></a>次のステップ
エラスティック プールを作成した後、弾力性ジョブを作成してプールの弾力性データベースを管理できます。 エラスティック ジョブはプールのデータベースの任意の数に対して T-SQL スクリプトの実行を容易にします。 詳細については、「 [エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」をご覧ください。 

エラスティック データベース ツールを使用してスケールアウト、データの移動、クエリ、トランザクションの作成を行う方法については、「[Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md)」を参照してください。


