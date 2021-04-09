---
title: SQL Managed Instance でのフェールオーバーを手動で開始する
description: Azure SQL Managed Instance でプライマリ レプリカとセカンダリ レプリカを手動でフェールオーバーする方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 02/27/2021
ms.openlocfilehash: 3c969c1898e67361e37a825d7976b1c52d08dd24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101691146"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>SQL Managed Instance でユーザーによって開始される手動フェールオーバー
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、SQL Managed Instance の General Purpose (GP) および Business Critical (BC) サービス レベルでプライマリ ノードを手動でフェールオーバーする方法と、BC サービス レベルのみでセカンダリ読み取り専用レプリカ ノードを手動でフェールオーバーする方法について説明します。

## <a name="when-to-use-manual-failover"></a>どのようなときに手動フェールオーバーを使用するか

[高可用性](../database/high-availability-sla.md)は、データベース アプリケーションに対して透過的に機能する SQL Managed Instance プラットフォームの基礎となる部分です。 ノードで機能低下や障害検出が発生した場合、または定期的な毎月のソフトウェア更新の間に、Azure で SQL Managed Instance を使用しているすべてのアプリケーションで、プライマリ ノードからセカンダリ ノードへのフェールオーバーが発生することが予想されます。

次のような理由により、SQL Managed Instance での[手動フェールオーバー](../database/high-availability-sla.md#testing-application-fault-resiliency)の実行を検討することがあります。
- 運用環境にデプロイする前に、フェールオーバーの回復性をテストする
- 自動フェールオーバーでの障害回復性について、エンド ツー エンドのシステムをテストする
- 既存のデータベース セッションに対するフェールオーバーの影響をテストする
- ネットワーク待機時間の変化によりフェールオーバーのエンド ツー エンドのパフォーマンスが変化するかどうかを確認する
- クエリのパフォーマンスが低下した場合、手動フェールオーバーがパフォーマンスの問題の軽減に役立つ場合があります。

> [!NOTE]
> 運用環境にデプロイする前にアプリケーションがフェールオーバー回復性を備えていることを確認するのは、運用環境でのアプリケーションの障害のリスクを軽減するのに役立ち、顧客に対するアプリケーションの可用性に寄与します。 アプリケーションのクラウド対応状況をテストする方法については、「[SQL Managed Instance によるフェールオーバー回復性のためのアプリ クラウドの準備のテスト](https://youtu.be/FACWYLgYDL8)」という動画をご覧ください。

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>SQL Managed Instance で手動フェールオーバーを開始する

### <a name="azure-rbac-permissions-required"></a>必要な Azure RBAC アクセス許可

フェールオーバーを開始するユーザーには、次のいずれかの Azure ロールが必要です。

- サブスクリプションの所有者ロール、または
- [Managed Instance 共同作成者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロール、または
- 次のアクセス許可を持つカスタム ロール:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>PowerShell の使用

Az.Sql [v2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 以降のバージョンが必要です。 常に最新バージョンの PowerShell を使用できる Azure portal の [Azure Cloud Shell](../../cloud-shell/overview.md) を使用することを検討します。 

前提条件として、次の PowerShell スクリプトを使用して必要な Azure モジュールをインストールします。 さらに、フェールオーバーする Managed Instance が含まれるサブスクリプションを選択します。

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

(BC と GP 両方のサービス レベルに適用) プライマリ ノードのフェールオーバーを開始するには、次の例のように PowerShell コマンド [Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) を使用します。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

(BC サービス レベルのみに適用) 読み取りセカンダリ ノードをフェールオーバーするには、次の PS コマンドを使用します。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>CLI の使用

最新の CLI スクリプトがインストールされていることを確認します。

(BC と GP 両方のサービス レベルに適用) プライマリ ノードのフェールオーバーを開始するには、次の例のように az sql mi failover CLI コマンドを使用します。

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

(BC サービス レベルのみに適用) 読み取りセカンダリ ノードをフェールオーバーするには、次の CLI コマンドを使用します。

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>REST API の使用

継続的テスト パイプラインや自動パフォーマンス問題軽減機能を実装する目的で、SQL Managed Instance のフェールオーバーを自動化する必要がある上級ユーザーの場合、API 呼び出しによってフェールオーバーを開始することで、この機能を実現できます。 詳細については、[Managed Instance のフェールオーバー REST API](/rest/api/sql/managed%20instances%20-%20failover/failover)に関する記事を参照してください。

REST API の呼び出しを使用してフェールオーバーを開始するには、まず、任意の API クライアントを使用して認証トークンを生成します。 生成された認証トークンは、API 要求のヘッダーで Authorization プロパティとして使用されます。これは必須です。

次のコードは、呼び出す API URI の例です。

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

API 呼び出しでは、次のプロパティを渡す必要があります。

| **API のプロパティ** | **パラメーター** |
| --- | --- |
| subscriptionId | マネージド インスタンスがデプロイされているサブスクリプション ID |
| resourceGroupName | マネージド インスタンスが含まれているリソース グループ |
| managedInstanceName | マネージド インスタンスの名前 |
| replicaType | (省略可能) (Primary または ReadableSecondary)。 これらのパラメーターは、フェールオーバーするレプリカの種類 (プライマリまたは読み取り可能セカンダリ) を表します。 指定しない場合、既定でフェールオーバーはプライマリ レプリカで開始されます。 |
| api-version | 静的な値。現在は "2019-06-01-preview" にする必要があります |

API 応答は、次の 2 つのいずれかになります。

- 202 Accepted
- 400 要求エラーのいずれか。

操作の状態は、応答ヘッダーの API 応答を確認することで追跡できます。 詳細については、[非同期 Azure 操作の状態](../../azure-resource-manager/management/async-operations.md)に関する記事を参照してください。

## <a name="monitor-the-failover"></a>フェールオーバーを監視する

BC インスタンスに関するユーザーが開始したフェールオーバーの進行状況を監視するには、任意のクライアント (SSMS など) で SQL Managed Instance に対して次の T-SQL クエリを実行します。 システム ビュー dm_hadr_fabric_replica_states が読み取られて、インスタンスで使用可能なレプリカがレポートされます。 手動フェールオーバーを開始した後、同じクエリを更新します。

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

フェールオーバーを開始する前の出力では、AlwaysOn 可用性グループ内の 1 つのプライマリと 3 つのセカンダリを含む BC サービス レベルの現在のプライマリ レプリカが示されています。 フェールオーバーを実行して、このクエリをもう一度実行すると、プライマリ ノードの変更が示される必要があります。

GP サービス レベルでは、上記の BC で示されているものと同じ出力を表示することはできません。 これは、GP サービス レベルは 1 つのノードのみに基づいているためです。 GP サービス レベル インスタンスのノードで SQL プロセスが開始された時刻を示す、次の代わりの T-SQL クエリを使用できます。

```T-SQL
SELECT sqlserver_start_time, sqlserver_start_time_ms_ticks FROM sys.dm_os_sys_info
```

フェールオーバー中にクライアントからの接続が短時間 (通常は 1 分未満) 失われる場合は、サービス レベルに関係なくフェールオーバーが実行されていることを示します。

> [!NOTE]
> (実際の短い使用不可ではなく) フェールオーバー プロセスが完了するには、**高負荷** のワークロードで一度に数分かかることがあります。 これは、フェールオーバーが行われる前に、インスタンス エンジンによって、プライマリ上のすべての現在のトランザクションが処理され、セカンダリ ノードでキャッチアップされるためです。

> [!IMPORTANT]
> ユーザーが開始した手動フェールオーバーの機能上の制限は次のとおりです。
> - 同じ Managed Instance 上では、**15 分** ごとに 1 つのフェールオーバーを開始できます。
> - BC インスタンスの場合、フェールオーバー要求が受け入れられるには、レプリカのクォーラムが存在している必要があります。
> - BC インスタンスの場合、フェールオーバーを開始する読み取り可能セカンダリ レプリカを指定することはできません。
> - フェールオーバーは、新しいデータベースの最初の完全バックアップが自動バックアップ システムによって完了されるまで許可されません。
> - データベースの復元が進行中のとき、フェールオーバーは許可されません。

## <a name="next-steps"></a>次のステップ
- アプリケーションのクラウド対応状況をテストする方法については、「[SQL Managed Instance によるフェールオーバー回復性のためのアプリ クラウドの準備のテスト](https://youtu.be/FACWYLgYDL8)」という動画をご覧ください。
- マネージド インスタンスの高可用性の詳細については、[Azure SQL Managed Instance での高可用性](../database/high-availability-sla.md)に関する記事を参照してください。
- 概要については、「[Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」を参照してください。
