---
title: 可用性グループおよび DNN リスナーとの機能の相互運用性
description: '特定の SQL Server 機能および分散ネットワーク名 (DNN) リスナーと、Azure VM 上の SQL Server の Always On 可用性グループを一緒に操作する場合の追加の考慮事項について説明します。 '
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
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359406"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>AG と DNN リスナーとの機能の相互運用性 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

特定の SQL Server 機能では、ハードコーディングされた仮想ネットワーク名 (VNN) が使用されます。 そのため、分散ネットワーク名 (DNN) リスナーを Azure VM 上の Always On 可用性グループおよび SQL Server と一緒に使用する場合には、いくつかの追加の考慮事項がある場合があります。 

この記事では、SQL Server 機能と可用性グループ DNN リスナーとの相互運用性について詳しく説明します。 


## <a name="client-drivers"></a>クライアント ドライバー

ODBC、OLEDB、ADO.NET、JDBC、PHP、Node.js のドライバーについては、ユーザーが接続文字列で DNN リスナー名とポートをサーバー名として明示的に指定する必要があります。 フェールオーバー後の速やかな接続を確保するために、`MultiSubnetFailover=True` を接続文字列に追加します (SQL クライアントでサポートされている場合)。 

## <a name="tools"></a>ツール

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、[sqlcmd](/sql/tools/sqlcmd-utility)、[Azure Data Studio](/sql/azure-data-studio/what-is)、[SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) のユーザーは、リスナーに接続するため、接続文字列で DNN リスナー名とポートをサーバー名として明示的に指定する必要があります。 

SQL Server Management Studio (SSMS) GUI を使用して DNN リスナーを作成することは現在サポートされていません。 


## <a name="availability-groups-and-fci"></a>可用性グループと FCI

フェールオーバー クラスター インスタンス (FCI) をレプリカの 1 つとして使用することで、Always On 可用性グループを構成できます。 この構成が DNN リスナーで動作するためには、[フェールオーバー クラスター インスタンスでも DNN を使用する必要があります](failover-cluster-instance-distributed-network-name-dnn-configure.md)。これは、FCI 仮想 IP アドレスを AG DNN IP リストに入れる方法がないためです。 

この構成では、FCI レプリカのミラーリング エンドポイント URL で FCI DNN を使用する必要があります。 同様に、FCI が読み取り専用レプリカとして使用される場合は、FCI レプリカへの読み取り専用ルーティングで FCI DNN を使用する必要があります。 

ミラーリング エンドポイントの形式は `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` です。 

たとえば、FCI DNN DNS 名が `dnnlsnr` で、FCI のミラーリング エンドポイントのポートが `5022` の場合、エンドポイント URL を作成するための Transact-SQL (T-SQL) コード スニペットは次のようになります。 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同様に、読み取り専用ルーティング URL の形式は `TCP://<FCI DNN DNS name>:<SQL Server instance port>` です。 

たとえば、DNN DNS 名が `dnnlsnr`、ターゲットの読み取り専用 SQL Server FCI によって使用されるポートが `1444` の場合、読み取り専用ルーティング URL を作成するための T-SQL コード スニペットは次のようになります。 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

ポートが既定の 1433 の場合、URL でそのポートを省略できます。 名前付きインスタンスの場合は、名前付きインスタンスの静的ポートを構成し、読み取り専用ルーティング URL でそれを指定します。  

## <a name="distributed-availability-group"></a>分散型可用性グループ

現在、分散型可用性グループは DNN リスナーではサポートされていません。 

## <a name="replication"></a>レプリケーション

トランザクション、マージ、およびスナップショット レプリケーションのすべてにおいて、VNN リスナーをリスナーに接続するレプリケーション オブジェクトの DNN リスナーとポートで置き換えることがサポートされています。 

可用性グループでレプリケーションを使用する方法の詳細については、[パブリッシャーと AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)、[サブスクライバーと AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)、および[ディストリビューターと AG](/sql/relational-databases/replication/configure-distribution-availability-group) に関するページを参照してください。

## <a name="msdtc"></a>MSDTC

ローカルおよびクラスター化された MSDTC の両方がサポートされますが、MSDTC では、HA ポートを構成するために標準の Azure Load Balancer が必要になる動的ポートが使用されます。 そのため、VM では標準の IP 予約を使用しないと、インターネットに公開されません。 

2 つのルールを定義します。1 つは RPC エンドポイント マッパー ポート 135 用で、もう 1 つは実際の MSDTC ポート用です。 フェールオーバー後、新しいノードで MSDTC ポートが変更されたら、LB ルールを新しい MSDTC ポート宛に変更します。 

MSDTC がローカルの場合は、必ず送信通信を許可してください。 

## <a name="distributed-query"></a>分散クエリ 

分散クエリは、AG DNN リスナーとポートを使用して構成できるリンク サーバーに依存します。 ポートが 1433 でない場合は、リンク サーバーを構成するときに SQL Server Management Studio (SSMS) の **[Use other data source]\(その他のデータ ソースを使用\)** オプションを選択します。 

## <a name="filestream"></a>FileStream

Filestream はサポートされていますが、ユーザーが Windows File API を使用してスコープのファイル共有にアクセスする場合はサポートされません。 

## <a name="filetable"></a>Filetable

Filetable はサポートされていますが、ユーザーが Windows File API を使用してスコープのファイル共有にアクセスする場合はサポートされません。 

## <a name="linked-servers"></a>リンク サーバー

AG DNN リスナーの名前とポートを使用して、リンク サーバーを構成します。 ポートが 1433 でない場合は、リンク サーバーを構成するときに SQL Server Management Studio (SSMS) の **[Use other data source]\(その他のデータ ソースを使用\)** オプションを選択します。 


## <a name="frequently-asked-questions"></a>よく寄せられる質問


- AG DNN リスナーはどの SQL Server バージョンでサポートされますか? 

   SQL Server 2019 CU8 以降です。

- DNN リスナーを使用した場合には、どのくらいのフェールオーバー時間が予想されますか?

   DNN リスナーの場合、フェールオーバーの時間は AG のフェールオーバー時間とちょうど同じになり、時間が余計にかかることはありません (Azure Load Balancer を使用している場合のプローブの時間と同様です)。

- SQL クライアントで OLEDB および ODBC と共に DNN を使用するには、バージョンの要件がありますか?

   DNN リスナーには `MultiSubnetFailover=True` 接続文字列を使用することをお勧めします。 これは SQL Server 2012 (11.x) 以降で使用できます。

- DNN リスナーを使用するために変更する必要がある SQL Server の構成はありますか? 

   DNN を使用するために SQL Server で構成を変更する必要はないものの、一部の SQL Server 機能については、考慮が必要な点が多くなる可能性があります。 

- DNN では、複数のサブネット クラスターがサポートされますか?

   はい。 DNS の DNN は、サブネットに関係なく、クラスターによって可用性内のすべてのレプリカの物理 IP アドレスにバインドされます。 SQL クライアントは、サブネットに関係なく、DNS 名の IP アドレスをすべて試行します。 



## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。 

- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)   
- [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

