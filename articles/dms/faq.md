---
title: Azure Database Migration Service の使用に関する FAQ | Microsoft Docs
description: Azure Database Migration Service を使用してデータベースを移行する作業に関してよく寄せられる質問に答えます。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 3da3a8dbe337f649fe7d0516b49c03a2f01d7eea
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180587"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Azure Database Migration Service の使用に関する FAQ
この記事では、Azure Database Migration Service の使用に関してよく寄せられる質問とその回答をまとめてあります。

### <a name="q-what-is-azure-database-migration-service"></a>Q. Azure Database Migration Service とは何ですか。
Azure Database Migration Service は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。 このサービスは、現在一般公開されていますが、以下に重点を置いた開発が進行中です。
- 信頼性とパフォーマンス。
- ソースとターゲットのペアの反復的追加。
- スムーズな移行を実現するための継続的な投資。

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>Q. Azure Database Migration Service では、現在、どのソースとターゲットのペアがサポートされていますか。
このサービスは現在、さまざまな移行シナリオをサポートしています。 利用可能な移行シナリオごとの状態の完全な一覧については、記事「[Azure Database Migration Service によってサポートされる移行シナリオの状態](https://docs.microsoft.com/azure/dms/resource-scenario-status)」をご覧ください。 その他の移行シナリオは限定プレビューにあり、DMS プレビュー サイトを通じて申請を送信する必要があります。 限定プレビューにあるシナリオの完全な一覧と、これらのオファリングのいずれかに参加するためのサインアップ方法については、[DMS プレビュー サイト](https://aka.ms/dms-preview/)をご覧ください。

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>Q. Azure Database Migration Service は、他の Microsoft データベース移行ツール (Database Migration Assistant (DMA)、SQL Server Migration Assistant (SSMA) など) とどのような点が違いますか。
Azure Database Migration Service は、Microsoft Azure への大規模なデータベース移行に適しています。 Azure Database Migration Service と他の Microsoft データベース移行ツールとの比較や、さまざまなシナリオでサービスを使用する際の推奨事項については、ブログ投稿の「[Differentiating Microsoft’s Database Migration Tools and Services (Microsoft の各データベース移行ツールおよびサービスの差異)](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)」を参照してください。

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>Q. Azure Database Migration Service と Azure Migrate サービスはどのような点が違いますか。
Azure Migrate サービスは、オンプレミスの仮想マシンから Azure IaaS への移行を支援します。 このサービスは、移行の適合性を評価し、パフォーマンスに基づくサイズを評価して、オンプレミスの仮想マシンを Azure で実行するためのコストを見積もることができます。 Azure Migrate は、オンプレミスの VM ベースのワークロードを Azure IaaS VM にリフトアンドシフト移行する場合に便利です。 ただし、Azure Database Migration Service とは異なり、Azure Migrate は、Azure PaaS リレーショナル データベース プラットフォーム (Azure SQL Database、SQL Azure、Azure SQL Database Managed Instance など) 用に特化したデータベース移行サービスではありません。

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>Q. Azure Database Migration Service でソースとしてサポートされるのは、どのバージョンの SQL Server ですか。
SQL Server から移行する場合、Azure Database Migration Service では SQL Server 2005 から SQL Server 2017 までがサポートされます。

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>Q. Azure Database Migration Service を使用してデータベースを移行するために必要な手順の概要は、どのようなものですか。
一般的な単純なデータベース移行では、以下の作業を行います。
1.  ターゲット データベースを作成します。
2.  [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595) を使用して、データベース スキーマを移行します。
3.  Azure Database Migration Service のインスタンスを作成する。
4.  ソース データベース、ターゲット データベース、および移行するテーブルを指定する移行プロジェクトを作成します。
5.  完全読み込みを開始します。
6.  後続の検証を選択します。
7.  運用環境を新しいクラウドベースのデータベースに手動で切り替えます。 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>Q. Azure Database Migration Service を使用するための前提条件はどのようなものですか。
データベースを移行するときに Azure Database Migration Service を円滑に動作させるには、いくつかの前提条件があります。 いくつかの前提条件は、サービスでサポートされているすべてのシナリオ (ソースとターゲットのペア) に適用されますが、その他の前提条件は特定のシナリオに固有のものです。
サポートされているすべての移行シナリオで共通の、Azure Database Migration Service の前提条件は、次のとおりです。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の VNET を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。
- Azure Virtual Network (VNET) のネットワーク セキュリティ グループの規則によって、通信ポート 443、53、9354、445、12000 がブロックされていないことを確認します。 Azure VNET NSG トラフィックのフィルター処理の詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)」を参照してください。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
 
Azure Database Migration Service を使用して特定の移行シナリオを試すために必要なすべての前提条件の一覧については、docs.microsoft.com にある Azure Database Migration Service [ドキュメント](https://docs.microsoft.com/azure/dms/dms-overview)の関連チュートリアルを参照してください。

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>Q. 移行のソース データベースへのアクセスに使用されるファイアウォール規則の許可リストを作成するために、Azure Database Migration Service の IP アドレスを調べるには、どうすればよいですか。
Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。 サービスの IP アドレスは動的ですが、ExpressRoute を使用している場合、このアドレスは企業ネットワークによってプライベートに割り当てられます。 適切な IP アドレスを特定する最も簡単な方法は、プロビジョニングされた Azure Database Migration Service リソースと同じリソース グループを検索して、関連付けられているネットワーク インターフェイスを見つけることです。 通常、ネットワーク インターフェイス リソースの名前は、NIC 接頭辞で始まり、一意の文字と数字のシーケンスが続きます (NIC-jj6tnztnmarpsskr82rbndyp など)。 このネットワーク インターフェイス リソースを選択すると、Azure Portal のリソースの概要のページに、許可リストに含める必要がある IP アドレスが表示されます。

また、SQL Server がリッスンしているポート ソースも許可リストに含めなければならないことがあります。 既定ではポート 1433 ですが、ソース SQL Server が他のポートもリッスンするように構成されていることがあります。 その場合、それらのポートも許可リストに含める必要があります。 次の動的管理ビュー クエリを使用すると、SQL Server がリッスンしているポートを特定できます。

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
SQL Server エラー ログに対して次のクエリを実行して、SQL Server がリッスンしているポートを特定することもできます。

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>Q. Azure Database Migration Service のパフォーマンスを最適化するための推奨事項はありますか。
このサービスによるデータベースの移行を高速化するために、次のようなことができます。
- サービス インスタンスを作成するときにマルチ CPU の汎用価格レベルを使用して、サービスが並列化とデータ転送の高速化のために複数の vCPU を活用できるようにします。
- データ移行操作中に一時的に Azure SQL Database のターゲット インスタンスを Premium レベル SKU にスケールアップし、より低いレベルの SKU を使用した場合にデータ転送アクティビティに影響する Azure SQL Database の調整を最小限に抑えます。

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>Q. Azure Virtual Network をセットアップするにはどうすればよいですか。
Azure VNET のセットアップ手順を説明する複数の Microsoft チュートリアルがありますが、公式ドキュメントは「[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)」という記事に掲載されています。

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>Q. Azure Database Migration Service が利用できないか停止しています。なぜですか。
ユーザーが明示的に Azure Database Migration Service (DMS) を停止した場合、またはサービスが 24 時間非アクティブ状態になった場合、サービスは停止状態または自動一時停止状態になります。 いずれの場合も、サービスは利用できなくなり、停止状態になります。  アクティブな移行を再開するには、サービスを再起動します。

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>Q. Azure Database Migration Service についてのフィードバックは、どこで行うことができますか。
ユーザーからのご意見をお待ちしています。 Azure Database Migration Service に関するフィードバックやご要望は、[こちら](https://feedback.azure.com/forums/906100-azure-database-migration-service)から、または [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) でチームまでお送りください。

## <a name="next-steps"></a>次の手順
Azure Database Migration Service の概要と、リージョンごとの利用可能性については、「[Azure Database Migration Service とは](dms-overview.md)」という記事をご覧ください。 
