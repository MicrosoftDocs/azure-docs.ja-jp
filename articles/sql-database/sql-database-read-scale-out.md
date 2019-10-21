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
ms.date: 06/03/2019
ms.openlocfilehash: 73c31a60fb14df00f50fefb35ca123298241c61d
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812381"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>読み取り専用レプリカを使用して読み取り専用クエリ ワークロードを負荷分散する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[高可用性アーキテクチャ](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)の一部として、Premium および Business Critical サービス レベルの各データベースは、1 つのプライマリ レプリカと複数のセカンダリ レプリカを使用して自動的にプロビジョニングされます。 セカンダリ レプリカは、プライマリ レプリカと同じコンピューティング サイズでプロビジョニングされます。 **読み取りスケールアウト**機能では、読み取り/書き込みレプリカを共有する代わりに、読み取り専用レプリカのいずれか 1 つの処理能力を使用して SQL Database の読み取り専用ワークロードを負荷分散できます。 これにより、読み取り専用のワークロードは、メインの読み取り/書き込みワークロードから分離され、パフォーマンスに影響を及ぼすことはありません。 この機能は、分析などの論理的に分離された読み取り専用ワークロードを含むアプリケーション向けです。 Premium および Business Critical サービス レベルでは、余分なコストをかけることなく、この追加の処理能力を使用してパフォーマンス上のメリットをアプリケーションで得ることが可能です。

また、少なくとも 1 つのセカンダリ レプリカが作成されている場合は、ハイパースケール サービス レベルで**読み取りスケールアウト**機能を使用することもできます。 読み取り専用ワークロードが 1 つのセカンダリ レプリカで利用可能なリソースよりも多くのリソースを必要とする場合は、複数のセカンダリ レプリカを使用できます。 Basic、Standard、および General Purpose サービス レベルの高可用性アーキテクチャには、レプリカは一切含まれていません。 **読み取りスケールアウト**機能は、これらのサービス レベルでは使用できません。

次の図に、Business Critical データベースの使用例を示します。

![読み取り専用レプリカ](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

新しい Premium、Business Critical、および Hyperscale データベースでは、読み取りスケールアウト機能は既定で有効になっています。 ハイパースケールの場合、既定では、新しいデータベースに対して 1 つのセカンダリ レプリカが作成されます。 お使いの SQL 接続文字列が `ApplicationIntent=ReadOnly` で構成されている場合、アプリケーションは、ゲートウェイによって、データベースの読み取り専用レプリカにリダイレクトされます。 `ApplicationIntent` プロパティの使用方法の詳細については、「[アプリケーションの目的を指定する](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)」をご覧ください。

SQL 接続文字列の `ApplicationIntent` 設定に関係なく、アプリケーションがプライマリ レプリカに確実に接続されるようにする場合は、データベースを作成するとき、またはその構成を変更するときに、読み取りスケールアウトを明示的に無効にする必要があります。 たとえばデータベースを Standard または General Purpose レベルから Premium、Business Critical、または Hyperscale レベルにアップグレードするときに、すべての接続が引き続きプライマリ レプリカに対して行われるようにするには、読み取りスケールアウトを無効にします。無効にする方法の詳細については、「[読み取りスケールアウトの有効化と無効化](#enable-and-disable-read-scale-out)」を参照してください。

> [!NOTE]
> 読み取り専用レプリカでは、クエリ データ ストア、拡張イベント、SQL Profiler、および監査の各機能はサポートされていません。 

## <a name="data-consistency"></a>データの一貫性

レプリカのメリットの 1 つは、レプリカが常にトランザクション上一貫性が保たれた状態となることですが、さまざまな時点で異なるレプリカ間に短い待機時間が発生する可能性があります。 読み取りスケールアウトは、セッション レベルの一貫性をサポートしています。 つまり、レプリカが使用できないことが原因で接続エラーが発生した後に読み取り専用セッションが再接続された場合、読み取り/書き込みレプリカによる更新が 100% ではないレプリカにリダイレクトされる可能性があります。 同様に、アプリケーションが読み取り/書き込みセッションを使用してデータを書き込み、読み取り専用セッションを使用してすぐに読み取る場合、レプリカに最新の更新がすぐに表示されない可能性があります。 待機時間は、非同期のトランザクション ログのやり直し操作が原因で生じます。

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

Premium、Business Critical、および Hyperscale サービス レベルでは、読み取りスケールアウトは既定で有効になっています。 Basic、Standard、または General Purpose サービス レベルで読み取りスケールアウトを有効にすることはできません。 レプリカ数 0 で構成された Hyperscale データベースでは、読み取りスケールアウトは自動的に無効になります。 

Premium または Business Critical サービス レベルの単一データベースとエラスティック プール データベースの読み取りスケールアウトは、次の方法で無効にして再び有効にすることができます。

> [!NOTE]
> 読み取りスケールアウトを無効にする能力は、下位互換性を維持するために提供されています。

### <a name="azure-portal"></a>Azure ポータル

読み取りスケールアウトの設定は、 **[構成]** データベース ブレードで管理できます。 

### <a name="powershell"></a>PowerShell

Azure PowerShell で読み取りスケールアウトを管理するには、2016 年 12 月以降のリリースの Azure PowerShell が必要です。 最新の PowerShell リリースについては、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) に関するページを参照してください。

Azure PowerShell で [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) コマンドレットを呼び出し、`-ReadScale` パラメーターに目的の値 (`Enabled` または `Disabled`) を渡すことで、読み取りスケールアウトを無効にし、再び有効にできます。 

既存のデータベースの読み取りスケールアウトを無効にするには (山かっこ内の項目を自分の環境用の適切な値に置き換え、山かっこを削除してください):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
新しいデータベースの読み取りスケールアウトを無効にするには (山かっこ内の項目を自分の環境用の適切な値に置き換え、山かっこを削除してください):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

既存のデータベースの読み取りスケールアウトを再び有効にするには (山かっこ内の項目を自分の環境用の適切な値に置き換え、山かっこを削除してください):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

読み取りスケールアウトが無効なデータベースを作成する、または既存のデータベースの設定を変更するには、次の方法を使用して、次の要求の例に示すように `readScale` プロパティを `Enabled` または `Disabled` に設定します。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
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

- SQL Database Hyperscale オファリングの情報については、[Hyperscale サービス レベル](./sql-database-service-tier-hyperscale.md)に関する記事を参照してください。
