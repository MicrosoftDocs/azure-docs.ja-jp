---
title: Azure Arc 対応 Managed Instance の高可用性
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: 高可用性を備えた Azure Arc 対応 Managed Instance をデプロイする方法について説明します。
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 92f5c900238fc5d40e22870e2f00f8adeb5d335f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032196"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Azure Arc 対応 Managed Instance の高可用性

Azure Arc 対応 Managed Instance は、コンテナー化されたアプリケーションとして Kubernetes にデプロイされ、ステートフル セットや永続ストレージなどの Kubernetes コンストラクトを使用して、サービスの正常性を維持するための組み込みの稼働状況の監視、障害検出、フェールオーバー メカニズムが提供されます。 信頼性を向上させるために、高可用性構成で追加のレプリカと共にデプロイするように Azure Arc 対応 Managed Instance を構成することもできます。 監視、障害検出、自動フェールオーバーは、Arc データ サービスのデータ コントローラーによって管理されます。 このサービスでは、可用性グループのセットアップ、データベース ミラーリング エンドポイントの構成から、可用性グループへのデータベースの追加またはフェールオーバーとアップグレードの調整まですべて、ユーザーの介入なしで提供されます。 このドキュメントでは、両方の種類の高可用性について説明します。

## <a name="built-in-high-availability"></a>組み込みの高可用性 

リモートの永続ストレージが構成され、Arc データ サービスのデプロイで使用されるノードと共有される場合は、Kubernetes によって組み込みの高可用性が提供されます。 この構成では、Kubernetes はクラスター オーケストレーターの役割を果たします。 コンテナー内のマネージド インスタンスまたは基になるノードで障害が発生すると、オーケストレーターにより、そのコンテナーの別のインスタンスがブートストラップされて同じ永続ストレージに接続されます。 Azure Arc 対応 Managed Instance をデプロイすると、このタイプが既定で有効になります。

### <a name="verify-built-in-high-availability"></a>組み込みの高可用性を検証する

このセクションでは、Kubernetes によって提供される組み込みの高可用性を検証します。 この機能をテストする手順に従う場合は、既存のマネージド インスタンスのポッドを削除し、Kubernetes によってこのアクションから回復されることを確認します。 

### <a name="prerequisites"></a>前提条件

- Kubernetes クラスターには、[共有されたリモート記憶域](storage-configuration.md#factors-to-consider-when-choosing-your-storage-configuration)がある必要があります 
- 1 つのレプリカ (既定) を使用してデプロイされた Azure Arc 対応 Managed Instance

1. ポッドを表示します。 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. マネージド インスタンスのポッドを削除します。

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   次に例を示します。

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. ポッドを表示して、マネージド インスタンスが回復していることを確認します。

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   次に例を示します。

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

ポッド内のすべてのコンテナーが回復したら、そのマネージド インスタンスに接続できます。

## <a name="deploy-with-always-on-availability-groups"></a>Always On 可用性グループを使用してデプロイする

信頼性を向上させるために、高可用性構成で追加のレプリカと共にデプロイするように Azure Arc 対応 Managed Instance を構成できます。 

可用性グループによって有効になる機能は次のとおりです。

- 複数のレプリカと共にデプロイすると、`containedag` という名前の単一の可用性グループが作成されます。 既定では、`containedag` に、プライマリを含む、3 つのレプリカがあります。 可用性グループの CRUD 操作はすべて内部的に管理されます。これには、可用性グループの作成や作成された可用性グループへのレプリカの参加が含まれます。 Azure Arc 対応 Managed Instance に追加の可用性グループを作成することはできません。

- データベースはすべて可用性グループに自動的に追加されます。これには、すべてのユーザーおよびシステム データベース (`master` や `msdb` など) が含まれます。 この機能により、可用性グループ レプリカ全体で単一システム ビューが提供されます。 インスタンスに直接接続する場合は、`containedag_master` データベースと `containedag_msdb` データベースの両方に注意してください。 `containedag_*` データベースは、可用性グループ内の `master` と `msdb` を表します。

- 外部エンドポイントは、可用性グループ内のデータベースへの接続用に自動的にプロビジョニングされます。 このエンドポイント `<managed_instance_name>-svc-external` は、可用性グループ リスナーの役割を果たします。

### <a name="deploy"></a>配置

可用性グループを使用してマネージド インスタンスをデプロイするには、次のコマンドを実行します。

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>状態の確認
インスタンスがデプロイされたら、次のコマンドを実行してインスタンスの状態を確認します。

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

出力例:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

追加された `Replicas` の数と、可用性グループの正常性を示す `AGstatus` フィールドに注目してください。 すべてのレプリカが稼働していて同期されている場合、この値は `healthy` になります。 

### <a name="restore-a-database"></a>データベースを復元する 
可用性グループにデータベースを復元するには、追加の手順が必要となります。 次の手順では、データベースをマネージド インスタンスに復元し、可用性グループに追加する方法について説明します。 

1. 新しい Kubernetes サービスを作成して、プライマリ インスタンスの外部エンドポイントを公開します。

    マネージド インスタンスに接続して次のコマンドを実行し、プライマリ レプリカをホストしているポッドを特定します。

    ```sql
    SELECT @@SERVERNAME
    ```
    Kubernetes クラスターで nodePort サービスを使用する場合は、次のコマンドを実行して、プライマリ インスタンスに kubernetes サービスを作成します。 `podName` を前の手順で返されたサーバーの名前に、`serviceName` を作成された Kubernetes サービスに使用する名前に置き換えます。

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    LoadBalancer サービスについても、作成されるサービスの種類が `LoadBalancer` であることを除き、同じコマンドを実行します。 次に例を示します。 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Azure Kubernetes Service に対してこのコマンドを実行する例を以下に示します。この場合、プライマリをホストしているポッドは `sql2-0` です。

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    作成された Kubernetes サービスの IP を取得します。

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. プライマリ インスタンスのエンドポイントにデータベースを復元します。

    データベースのバックアップ ファイルをプライマリ インスタンスのコンテナーに追加します。

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    例

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    次のコマンドを実行して、データベースのバックアップ ファイルを復元します。

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    例

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. 可用性グループにデータベースを追加します。

    データベースを AG に追加するには、完全復旧モードで実行する必要があり、ログ バックアップを作成する必要があります。 次の TSQL ステートメントを実行して、復元されたデータベースを可用性グループに追加します。

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    以下の例では、インスタンスで復元された `WideWorldImporters` という名前のデータベースを追加します。

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> ベスト プラクティスとして、このコマンドを実行し、上記で作成された Kubernetes サービスを削除してクリーンアップする必要があります。
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>制限事項

Azure Arc 対応 Managed Instance の可用性グループには、[ビッグ データ クラスターの可用性グループと同じ制限があります。詳細については、ここをクリックしてください。](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 SQL Managed Instance の機能](managed-instance-features.md)の詳細を確認する
