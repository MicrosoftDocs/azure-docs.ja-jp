---
title: Azure SQL Database - レプリカでの読み取りクエリ | Microsoft Docs
description: Azure SQL Database は、読み取りスケールアウトと呼ばれる読み取り専用レプリカの処理能力を使用して、読み取り専用ワークロードを負荷分散する機能を提供します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619893"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>読み取り専用レプリカを使用して読み取り専用クエリ ワークロードを負荷分散する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

**読み取りスケールアウト**では、1 つの読み取り専用レプリカの処理能力を使用して、Azure SQL Database の読み取り専用ワークロードを負荷分散できます。

Premium 階層 ([DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)) または Business Critical 階層 ([仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)) の各データベースは、可用性 SLA をサポートするために、複数の Always ON レプリカによって自動的にプロビジョニングされます。 これを示したのが次の図です。

![読み取り専用レプリカ](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

セカンダリ レプリカは、プライマリ レプリカと同じコンピューティング サイズでプロビジョニングされます。 **読み取りスケールアウト**機能では、読み取り/書き込みレプリカを共有する代わりに、読み取り専用レプリカのいずれか 1 つの処理能力を使用して SQL Database の読み取り専用ワークロードを負荷分散できます。 これにより、読み取り専用のワークロードは、メインの読み取り/書き込みワークロードから分離され、パフォーマンスに影響を及ぼすことはありません。 この機能は、分析などの論理的に分離された読み取り専用ワークロードを含むアプリケーションを対象としています。そのため、余分なコストをかけることなく、この追加の処理能力を使用してパフォーマンス上の利点を得ることが可能です。

特定のデータベースで読み取りスケールアウト機能を使用するには、データベースを作成するときに明示的に有効にするか、PowerShell を使用して [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) または [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) コマンドレットを呼び出すか、Azure Resource Manager REST API から [データベース - 作成または更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) メソッドを使用して構成を変更し、後から明示的に有効にする必要があります。

データベースの読み取りスケールアウトが有効になると、そのデータベースに接続するアプリケーションが、アプリケーションの接続文字列で構成されている `ApplicationIntent` プロパティに応じて、ゲートウェイによってデータベースの読み取り/書き込みレプリカまたは読み取り専用レプリカにリダイレクトされます。 `ApplicationIntent` プロパティの詳細については、「[アプリケーションの目的を指定する](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)」を参照してください。

読み取りスケールアウトが無効の場合またはサポートされていないサービス レベルで ReadScale プロパティを設定した場合、`ApplicationIntent` プロパティとは関係なく、すべての接続が読み取り/書き込みレプリカにリダイレクトされます。

> [!NOTE]
> 読み取り専用レプリカでは、クエリ データ ストア、拡張イベント、SQL Profiler、および監査の各機能はサポートされていません。 
## <a name="data-consistency"></a>データの一貫性

レプリカのメリットの 1 つは、レプリカが常にトランザクション上一貫性が保たれた状態となることですが、さまざまな時点で異なるレプリカ間に短い待機時間が発生する可能性があります。 読み取りスケールアウトは、セッション レベルの一貫性をサポートしています。 そのため、レプリカが使用できないことが原因で接続エラーが発生した後に読み取り専用セッションが再接続された場合、読み取り/書き込みレプリカを使用して 100% 最新ではないレプリカにリダイレクトされる可能性があります。 同様に、アプリケーションが読み取り/書き込みセッションを使用してデータを書き込み、読み取り専用セッションを使用してすぐに読み取る場合、レプリカに最新の更新がすぐに表示されない可能性があります。 待機時間は、非同期のトランザクション ログのやり直し操作が原因で生じます。

> [!NOTE]
> 領域内のレプリケーションの待機時間は短く、このような状況はまれです。

## <a name="connect-to-a-read-only-replica"></a>読み取り専用レプリカに接続する

データベースの読み取りスケールアウトを有効にすると、クライアントによって提供された接続文字列の `ApplicationIntent` オプションにより、接続が書き込みレプリカと読み取り専用レプリカのどちらにルーティングされるかが指定されます。 具体的には、`ApplicationIntent` 値が `ReadWrite` (既定値) の場合、接続はデータベースの読み取り/書き込みレプリカにリダイレクトされます。 これは、既存の動作と同じです。 `ApplicationIntent` 値が `ReadOnly` の場合、接続は読み取り専用レプリカにルーティングされます。

たとえば、次の接続文字列はクライアントを読み取り専用レプリカに接続します (山かっこ内の項目は環境内の適切な値に置き換え、山かっこは削除します)。

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

次の接続文字列はどちらもクライアントを読み取り/書き込みレプリカに接続します (山かっこ内の項目は環境内の適切な値に置き換え、山かっこは削除します)。

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>接続先が読み取り専用レプリカであることを確認する

次のクエリを実行することにより、読み取り専用レプリカに接続しているかどうかを確認することができます。 読み取り専用レプリカに接続している場合は、READ_ONLY が返されます。

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> ReadOnly セッションでアクセスできる AlwaysON レプリカは、どの時点においても 1 つのみです。

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>読み取り専用レプリカの監視とトラブルシューティング

読み取り専用レプリカに接続すると、`sys.dm_db_resource_stats` DMV を使用してパフォーマンス メトリックにアクセスできます。 クエリ プランの統計情報にアクセスするには、`sys.dm_exec_query_stats`、`sys.dm_exec_query_plan`、および `sys.dm_exec_sql_text` DMV を使用します。

> [!NOTE]
> 論理マスター データベース内の DMV `sys.resource_stats` は、プライマリ レプリカの CPU 使用率とストレージ データを返します。


## <a name="enable-and-disable-read-scale-out"></a>読み取りスケールアウトの有効化と無効化

読み取りスケールアウトは、既定では[マネージド インスタンス](sql-database-managed-instance.md)の Business Critical レベルで有効になります。 [SQL Database サーバーに配置されたデータベース](sql-database-servers.md)の Premium および Business Critical 階層で明示的に有効にする必要があります。 ここでは、読み取りスケールアウトを有効または無効にする方法について説明します。

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell:読み取りスケールアウトの有効化と無効化

Azure PowerShell で読み取りスケールアウトを管理するには、2016 年 12 月以降のリリースの Azure PowerShell が必要です。 最新の PowerShell リリースについては、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) に関するページを参照してください。

Azure PowerShell で [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) コマンドレットを呼び出し、`-ReadScale` パラメーターに目的の値 (`Enabled` または `Disabled`) を渡して、読み取りスケールアウトを有効または無効にします。 または、[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) コマンドレットを使用して、読み取りスケールアウトが有効になっている新しいデータベースを作成することもできます。

たとえば、既存のデータベースの読み取りスケールアウトを有効にするには、次のようにします (山かっこ内の項目は環境内の適切な値に置き換え、山かっこは削除します)。

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

既存のデータベースの読み取りスケールアウトを無効にするには、次のようにします (山かっこ内の項目は環境内の適切な値に置き換え、山かっこは削除します)。

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

読み取りスケールアウトが有効になっている新しいデータベースを作成するには、次のようにします (山かっこ内の項目は環境内の適切な値に置き換え、山かっこは削除します)。

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST API:読み取りスケールアウトの有効化と無効化

読み取りスケールアウトが有効になっているデータベースを作成したり、既存のデータベースの読み取りスケールアウトを有効または無効にしたりするには、次の要求のサンプルのように `readScale` プロパティを `Enabled` または `Disabled` に設定して、対応するデータベース エンティティを作成または更新します。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

詳細については、「[データベース - 作成または更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)」を参照してください。

## <a name="using-tempdb-on-read-only-replica"></a>読み取り専用レプリカでの TempDB の使用

TempDB データベースは読み取り専用レプリカにはレプリケートされません。 各レプリカには、レプリカの作成時に作成された独自のバージョンの TempDB データベースがあります。 これにより、TempDB が更新可能となり、クエリの実行時に変更できるようになります。 読み取り専用ワークロードが TempDB オブジェクトの使用に依存している場合は、これらのオブジェクトをクエリ スクリプトの一部として作成する必要があります。 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>geo レプリケートされたデータベースで読み取りスケールアウトを使用する

(たとえば、フェールオーバー グループのメンバーとして) geo レプリケートされたデータベース上の読み取り専用ワークロードを、読み取りスケールアウトを使用して負荷分散する場合は、プライマリ データベース上および geo レプリケートされたセカンダリ データベース上の両方で、読み取りスケールアウトが有効になっていることを確認します。 この構成により、フェールオーバー後にアプリケーションが新しいプライマリに接続したときに、同じ負荷分散エクスペリエンスが継続されます。 読み取りスケールが有効な、geo レプリケートされたセカンダリ データベースに接続している場合、`ApplicationIntent=ReadOnly` が設定されたセッションは、プライマリ データベース上で接続をルーティングするのと同じ方法で、レプリカの 1 つにルーティングされます。  `ApplicationIntent=ReadOnly` が設定されていないセッションは、geo レプリケートされたセカンダリのプライマリ レプリカ (これも読み取り専用) にルーティングされます。 geo レプリケートされたセカンダリ データベースのエンドポイントは、プライマリ データベースとは異なります。そのため、これまでは、セカンダリにアクセスするために、`ApplicationIntent=ReadOnly` を設定する必要はありませんでした。 下位互換性を確保するため、`sys.geo_replication_links` DMV には、`secondary_allow_connections=2` (すべてのクライアント接続を許可) が示されています。

> [!NOTE]
> セカンダリ データベースのローカル レプリカ間でラウンド ロビンやその他の負荷分散ルーティングはサポートされていません。

## <a name="next-steps"></a>次の手順

- PowerShell を使用した読み取りスケールアウトの設定の詳細については、「[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)」コマンドレットまたは「[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)」コマンドレットを参照してください。
- REST API を使用した読み取りスケールアウトの設定の詳細については、「[データベース - 作成または更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)」を参照してください。
