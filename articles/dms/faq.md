---
title: Azure Database Migration Service の使用に関する FAQ | Microsoft Docs
description: データベース移行を実行するための Azure Database Migration Service の使用に関するよく寄せられる質問に答えます。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/10/2019
ms.openlocfilehash: 5077539f6f80784f865bd4c1b52e3b4c147107ed
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718003"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Azure Database Migration Service の使用に関する FAQ。

この記事では、Azure Database Migration Service の使用に関するよく寄せられる質問とそれに関連する回答を示します。

## <a name="overview"></a>概要

**Q.Azure Database Migration Service とは何ですか。**
Azure Database Migration Service は、複数のデータベース ソースから Azure Data プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。 このサービスは、現在一般公開されていますが、以下に重点を置いた開発が進行中です。

* 信頼性とパフォーマンス。
* ソースとターゲットのペアの反復的追加。
* スムーズな移行を実現するための継続的な投資。

**Q.Azure Database Migration Service では、現在、どのソースとターゲットのペアがサポートされていますか。**
このサービスは現在、さまざまなソースとターゲットのペアや移行シナリオをサポートしています。 利用可能な移行シナリオごとの状態の完全な一覧については、記事「[Azure Database Migration Service によってサポートされる移行シナリオの状態](https://docs.microsoft.com/azure/dms/resource-scenario-status)」をご覧ください。

その他の移行シナリオはプレビュー段階にあり、入手するには DMS プレビュー サイト経由で申請を送信する必要があります。 プレビュー段階にあるシナリオの完全な一覧、およびこれらのオファリングのいずれかに参加するためのサインアップについては、[DMS プレビュー サイト](https://aka.ms/dms-preview/)を参照してください。

**Q.Azure Database Migration Service でソースとしてサポートされるのは、どのバージョンの SQL Server ですか。**
SQL Server から移行する場合、Azure Database Migration Service でサポートされているソースは SQL Server 2005 から SQL Server 2017 までです。

**Q:Azure Database Migration Service を使用する場合、オフライン移行とオンライン移行の違いは何ですか。**
Azure Database Migration Service は、オフライン移行およびオンライン移行を行うことができます。 *オフライン*移行では、アプリケーションのダウンタイムが、移行の開始時に開始されます。 *オンライン*移行では、ダウンタイムが移行の最後の切り替え時だけに限定されます。 オフライン移行をテストして、ダウンタイムが許容可能かどうかを判断することをお勧めします。許容できない場合は、オンライン移行を行います。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 詳しくは、Azure Database Migration Service の[価格](https://azure.microsoft.com/pricing/details/database-migration/)に関するページをご覧ください。

**Q.Azure Database Migration Service は、他の Microsoft データベース移行ツール (Database Migration Assistant (DMA)、SQL Server Migration Assistant (SSMA) など) とどのような点が違いますか。**
Azure Database Migration Service は、Microsoft Azure への大規模なデータベース移行に適しています。 Azure Database Migration Service と他の Microsoft データベース移行ツールとの比較や、さまざまなシナリオでサービスを使用する際の推奨事項については、ブログ投稿の「[Differentiating Microsoft’s Database Migration Tools and Services (Microsoft の各データベース移行ツールおよびサービスの差異)](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)」を参照してください。

**Q.Azure Database Migration Service と Azure Migrate サービスはどのような点が違いますか。**
Azure Migrate は、オンプレミスの仮想マシンから Azure IaaS への移行を支援します。 このサービスは、移行の適合性を評価し、パフォーマンスに基づくサイズを評価して、オンプレミスの仮想マシンを Azure で実行するためのコストを見積もることができます。 Azure Migrate は、オンプレミスの VM ベースのワークロードを Azure IaaS VM にリフトアンドシフト移行する場合に便利です。 ただし、Azure Database Migration Service とは異なり、Azure Migrate は、Azure PaaS リレーショナル データベース プラットフォーム (Azure SQL Database、Azure SQL Database Managed Instance など) 用に特化したデータベース移行サービスではありません。

## <a name="setup"></a>セットアップ

**Q.Azure Database Migration Service を使用するための前提条件はどのようなものですか。**
データベースを移行するときに Azure Database Migration Service を円滑に動作させるには、いくつかの前提条件があります。 いくつかの前提条件は、サービスでサポートされているすべてのシナリオ (ソースとターゲットのペア) に適用されますが、その他の前提条件は特定のシナリオに固有のものです。

サポートされているすべての移行シナリオで共通の、Azure Database Migration Service の前提条件は、次のとおりです。

* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の VNet を作成します。これにより、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) のどちらかを使用したオンプレミスのソース サーバーへのサイト間接続が提供されます。
* Azure Virtual Network (VNet) のネットワーク セキュリティ グループの規則によって 443、53、9354、445、12000 の通信ポートがブロックされていないことを確認します。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事を参照してください。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。

Azure Database Migration Service を使用して特定の移行シナリオを試すために必要なすべての前提条件の一覧については、docs.microsoft.com にある Azure Database Migration Service [ドキュメント](https://docs.microsoft.com/azure/dms/dms-overview)の関連チュートリアルを参照してください。

**Q.移行のソース データベースへのアクセスに使用されるファイアウォール規則の許可リストを作成するために、Azure Database Migration Service の IP アドレスを調べるには、どうすればよいですか。**
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

**Q.Azure Virtual Network をセットアップするにはどうすればよいですか。**
Azure VNET のセットアップ手順を説明する複数の Microsoft チュートリアルがありますが、公式ドキュメントは「[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)」という記事に掲載されています。

## <a name="usage"></a>使用法

**Q.Azure Database Migration Service を使用してデータベースを移行するために必要な手順の概要は、どのようなものですか。**
一般的な単純なデータベース移行では、以下の作業を行います。

1. ターゲット データベースを作成します。
2. ソース データベースを評価します。
    * 同種移行を行う場合は、[DMA](https://www.microsoft.com/download/details.aspx?id=53595) を使用して既存のデータベースを評価します。
    * 異種移行 (競合ソースから) を行う場合は、[SSMA](https://aka.ms/get-ssma) を使用して既存のデータベースを評価します。 SSMA は、データベース オブジェクトを変換してスキーマをターゲット プラットフォームに移行する場合にも使用します。
3. Azure Database Migration Service のインスタンスを作成する。
4. ソース データベース、ターゲット データベース、および移行するテーブルを指定する移行プロジェクトを作成します。
5. 完全読み込みを開始します。
6. 後続の検証を選択します。
7. 運用環境を新しいクラウドベースのデータベースに手動で切り替えます。

## <a name="troubleshooting-and-optimization"></a>トラブルシューティングと最適化

**Q.DMS で移行プロジェクトをセットアップしていますが、ソース データベースへの接続で問題が発生しています。どうすればよいですか。**
移行作業中にソース データベース システムへの接続に問題が発生した場合、DMS インスタンスを設定する VNet に仮想マシンを作成します。 その仮想マシンでは、UDL ファイルを使用して SQL Server への接続をテストする、または Robo 3T をダウンロードして MongoDB の接続をテストするなど、接続テストを実行できるはずです。 接続テストに成功した場合、ソース データベースへの接続に問題はありません。 接続テストが成功しなかった場合は、ネットワーク管理者に問い合わせてください。

**Q.Azure Database Migration Service が利用できないか停止しています。なぜですか。**
ユーザーが明示的に Azure Database Migration Service (DMS) を停止した場合、またはサービスが 24 時間非アクティブ状態になった場合、サービスは停止状態または自動一時停止状態になります。 いずれの場合も、サービスは利用できなくなり、停止状態になります。  アクティブな移行を再開するには、サービスを再起動します。

**Q.Azure Database Migration Service のパフォーマンスを最適化するための推奨事項はありますか。**
このサービスによるデータベースの移行を高速化するために、次のようなことができます。

* サービス インスタンスを作成するときにマルチ CPU の汎用価格レベルを使用して、サービスが並列化とデータ転送の高速化のために複数の vCPU を活用できるようにします。
* データ移行操作中に一時的に Azure SQL Database のターゲット インスタンスを Premium レベル SKU にスケールアップし、より低いレベルの SKU を使用した場合にデータ転送アクティビティに影響する Azure SQL Database の調整を最小限に抑えます。

## <a name="next-steps"></a>次の手順

Azure Database Migration Service の概要と、リージョンごとの利用可能性については、「[Azure Database Migration Service とは](dms-overview.md)」という記事をご覧ください。
