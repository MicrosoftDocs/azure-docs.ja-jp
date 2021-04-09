---
title: SQL Server FCI および DNN との機能の相互運用性
description: '特定の SQL Server 機能および分散ネットワーク名 (DNN) リソースと、Azure VM 上の SQL Server のフェールオーバー クラスター インスタンスを一緒に操作する場合の追加の考慮事項について説明します。 '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358216"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>SQL Server FCI および DNN との機能の相互運用性
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

特定の SQL Server 機能では、ハードコーディングされた仮想ネットワーク名 (VNN) が使用されます。 そのため、分散ネットワーク名 (DNN) リソースを Azure VM 上のフェールオーバー クラスター インスタンスおよび SQL Server と一緒に使用する場合には、いくつかの追加の考慮事項があります。 

この記事では、DNN リソースを使用する際にネットワークの別名を構成する方法のほか、追加の考慮が必要な SQL Server 機能について説明します。

## <a name="create-network-alias-fci"></a>ネットワークの別名 (FCI) の作成

一部のサーバー側コンポーネントでは、ハードコーディングされた VNN 値を使用していて、正常に機能するために、VNN を DNN DNS 名にマップするネットワークの別名が必要です。 [サーバーの別名の作成](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client)に関するページの手順に従って、VNN を DNN DNS 名にマップする別名を作成してください。 

既定のインスタンスの場合は、VNN = DNN DNS 名のように、VNN を DNN DNS 名に直接マップできます。
たとえば、VNN 名が `FCI1`、インスタンス名が `MSSQLSERVER`、DNN が `FCI1DNN` である (クライアントの前の接続先が `FCI` で、今度の接続先が `FCI1DNN` である) 場合、VNN `FCI1` を DNN `FCI1DNN` にマップします。 

名前付きインスタンスの場合、ネットワークの別名のマッピングは `VNN\Instance` = `DNN\Instance` のように完全なインスタンスで行われる必要があります。 たとえば、VNN 名が `FCI1`、インスタンス名が `instA`、DNN が `FCI1DNN` である (クライアントの前の接続先が `FCI1\instA` で、今度の接続先が `FCI1DNN\instaA` である) 場合、VNN `FCI1\instaA` を DNN `FCI1DNN\instaA` にマップします。 



## <a name="client-drivers"></a>クライアント ドライバー

ODBC、OLEDB、ADO.NET、JDBC、PHP、Node.js のドライバーについては、ユーザーが接続文字列で DNN DNS 名をサーバー名として明示的に指定する必要があります。 フェールオーバー後の速やかな接続を確保するために、`MultiSubnetFailover=True` を接続文字列に追加します (SQL クライアントでサポートされている場合)。 

## <a name="tools"></a>ツール

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、[sqlcmd](/sql/tools/sqlcmd-utility)、[Azure Data Studio](/sql/azure-data-studio/what-is)、[SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) のユーザーは、接続文字列で DNN DNS 名をサーバー名として明示的に指定する必要があります。 

## <a name="availability-groups-and-fci"></a>可用性グループと FCI

フェールオーバー クラスター インスタンスをレプリカの 1 つとして使用することで、AlwaysOn 可用性グループを構成できます。 この構成では、FCI レプリカのミラーリング エンドポイント URL で FCI DNN を使用する必要があります。 同様に、FCI が読み取り専用レプリカとして使用される場合は、FCI レプリカへの読み取り専用ルーティングで FCI DNN を使用する必要があります。 

ミラーリング エンドポイントの形式は `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` です。 

たとえば、DNN DNS 名が `dnnlsnr` で、FCI のミラーリング エンドポイントのポートが `5022` の場合、エンドポイント URL を作成するための Transact-SQL (T-SQL) コード スニペットは次のようになります。 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同様に、読み取り専用ルーティング URL の形式は `TCP://<DNN DNS name>:<SQL Server instance port>` です。 

たとえば、DNN DNS 名が `dnnlsnr`、ターゲットの読み取り専用 SQL Server FCI によって使用されるポートが `1444` の場合、読み取り専用ルーティング URL を作成するための T-SQL コード スニペットは次のようになります。 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

ポートが既定の 1433 の場合、URL でそのポートを省略できます。 名前付きインスタンスの場合は、名前付きインスタンスの静的ポートを構成し、読み取り専用ルーティング URL でそれを指定します。  

## <a name="replication"></a>レプリケーション

レプリケーションは 3 つのコンポーネントで構成されます。パブリッシャー、ディストリビューター、サブスクライバー。 これらのコンポーネントはいずれも、フェールオーバー クラスター インスタンスにすることができます。 レプリケーション構成では (明示的にも暗黙的にも) FCI VNN が頻繁に使用されるため、VNN を DNN にマップするネットワークの別名は、レプリケーションが機能するために必要な場合があります。 

レプリケーション内では VNN 名を FCI 名として使用し続けますが、"*レプリケーションを構成する前に*"、次のリモート状況でネットワークの別名を作成します。

| **レプリケーション コンポーネント (FCI と DNN)** | **リモート コンポーネント** | **ネットワークの別名のマップ** | **ネットワーク マップがあるサーバー**| 
|---------|---------|---------|-------- | 
|Publisher | ディストリビューター | パブリッシャー VNN からパブリッシャー DNN| ディストリビューター| 
|ディストリビューター|サブスクライバー (Subscriber) |ディストリビューター VNN からディストリビューター DNN| サブスクライバー (Subscriber) | 
|ディストリビューター|Publisher | ディストリビューター VNN からディストリビューター DNN | Publisher| 
|サブスクライバー (Subscriber)| ディストリビューター| サブスクライバー VNN からサブスクライバー DNN | ディストリビューター| 

たとえば、レプリケーション トポロジで DNN を使用する FCI として構成されたパブリッシャーがあり、ディストリビューターはリモートであるとします。 この場合、ディストリビューター サーバー上にネットワークの別名を作成して、パブリッシャー VNN をパブリッシャー DNN にマップします。 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="SQL Server 構成マネージャーを使用して、DNN DNS 名をネットワークの別名として構成する。" :::

次の画像の例のように、名前付きインスタンスの完全なインスタンス名を使用します。 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="名前付きインスタンスのネットワークの別名を構成する際に、完全なインスタンス名を使用する。" :::

## <a name="database-mirroring"></a>データベース ミラーリング

FCI を一方のデータベース ミラーリング パートナーとしてデータベース ミラーリングを構成できます。 SQL Server Management Studio GUI ではなく、[Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) を使用して構成します。 T-SQL を使用すると、VNN ではなく DNN を使用してデータベース ミラーリング エンドポイントが作成されます。 

たとえば、DNN DNS 名が `dnnlsnr` で、データベース ミラーリング エンドポイントが 7022 の場合、次の T-SQL コード スニペットでデータベース ミラーリング パートナーを構成します。 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

クライアント アクセスについては、**Failover Partner** プロパティでデータベース ミラーリング フェールオーバーを処理できます (ただし、FCI フェールオーバーは不可)。 

## <a name="msdtc"></a>MSDTC

FCI は、Microsoft 分散トランザクション コーディネーター (MSDTC) によってコーディネートされた分散トランザクションに参加できます。 クラスター化された MSDTC とローカルの MSDTC の両方が FCI DNN によってサポートされていますが、Azure では、クラスター化された MSDTC にまだロード バランサーが必要です。 FCI で定義された DNN は、Azure のクラスター化された MSDTC の Azure Load Balancer 要件に代わるものではありません。 

## <a name="filestream"></a>FileStream

FCI のデータベースでは FileStream がサポートされていますが、ファイル システム API と DNN を使用した FileStream または FileTable へのアクセスはサポートされていません。 

## <a name="linked-servers"></a>リンク サーバー

FCI DNN でのリンク サーバーの使用がサポートされています。 DNN を使用してリンク サーバーを直接構成するか、VNN を DNN にマップするネットワークの別名を使用します。 


たとえば、名前付きインスタンス `dnnlsnr` に DNN DNS 名 `insta1` を使用してリンク サーバーを作成するには、次の Transact-SQL (T-SQL) コマンドを使用します。

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

または、代わりに仮想ネットワーク名 (VNN) を使用してリンク サーバーを作成することもできますが、VNN を DNN にマップするネットワークの別名を定義することが必要になります。 

たとえば、インスタンス名が `insta1`、VNN 名が `vnnname`、DNN 名が `dnnlsnr` の場合、VNN を使用してリンク サーバーを作成するには、次の Transact-SQL (T-SQL) コマンドを使用します。

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

次に、`vnnname\insta1` を `dnnlsnr\insta1` にマップするネットワークの別名を作成します。 



## <a name="frequently-asked-questions"></a>よく寄せられる質問


- DNN はどの SQL Server バージョンでサポートされますか? 

   SQL Server 2019 CU2 以降です。

- DNN を使用した場合には、どのくらいのフェールオーバー時間が予想されますか?

   DNN の場合、フェールオーバーの時間は FCI のフェールオーバー時間とちょうど同じになり、時間が余計にかかることはありません (Azure Load Balancer を使用している場合のプローブの時間と同様です)。

- SQL クライアントで OLEDB および ODBC と共に DNN を使用するには、バージョンの要件がありますか?

   DNN には `MultiSubnetFailover=True` 接続文字列を使用することをお勧めします。 これは SQL Server 2012 (11.x) 以降で使用できます。

- DNN を使用するために必要な SQL Server 構成の変更はありますか? 

   DNN を使用するために SQL Server で構成を変更する必要はないものの、一部の SQL Server 機能については、考慮が必要な点が多くなる可能性があります。 

- DNN では、複数のサブネット クラスターがサポートされますか?

   はい。 DNS の DNN は、サブネットに関係なく、クラスターによってクラスター内のすべてのノードの物理 IP アドレスにバインドされます。 SQL クライアントは、サブネットに関係なく、DNS 名の IP アドレスをすべて試行します。 



## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。 

- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

