---
title: SQL Database のディザスター リカバリー | Microsoft Docs
description: Azure SQL Database のアクティブ geo レプリケーションと geo リストア機能を使用して、地域のデータ センターの停止や障害からデータベースを復旧する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/21/2019
ms.openlocfilehash: 00fa1128df03befda8b15be2d7f2c527f65f9973
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341072"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Azure SQL Database を復元する、またはセカンダリにフェールオーバーする

Azure SQL Database は、障害から回復するために次の機能を備えています。

- [アクティブ geo レプリケーション](sql-database-active-geo-replication.md)
- [自動フェールオーバー グループ](sql-database-auto-failover-group.md)
- [geo リストア](sql-database-recovery-using-backups.md#point-in-time-restore)
- [ゾーン冗長データベース](sql-database-high-availability.md)

ビジネス継続性のシナリオと、こうしたシナリオをサポートする機能の詳細については、 [ビジネス継続性](sql-database-business-continuity.md)に関するページをご覧ください。

> [!NOTE]
> ゾーン冗長の Premium または Business Critical データベースやプールを使用している場合は、回復プロセスが自動化され、このガイドの残りの部分は適用されません。

> [!NOTE]
> プライマリとセカンダリ、両方のデータベースが同じサービス階層を持つ必要があります。 また、セカンダリ データベースは、プライマリと同じコンピューティング サイズ (DTU または仮想コア) で作成することを強くお勧めします。 詳細については、「[プライマリ データベースのアップグレードまたはダウングレード](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database)」を参照してください。

> [!NOTE]
> 1 つまたは複数のフェールオーバー グループを使用して、複数のデータベースのフェールオーバーを管理します。
> 既にある geo レプリケーションのリレーションシップをこのフェールオーバー グループに追加する場合は、その geo セカンダリが、プライマリと同じサービス レベルおよびコンピューティング サイズで構成されていることを確認してください。 詳細については、「[自動フェールオーバー グループを使用して、複数のデータベースの透過的な調整されたフェールオーバーを有効にする](sql-database-auto-failover-group.md)」を参照してください。

## <a name="prepare-for-the-event-of-an-outage"></a>障害に備える

フェールオーバー グループまたは geo 冗長バックアップのいずれかを使用して、他のデータ領域に適切に復旧するには、必要な場合に備えて、サーバーを、他のデータ センターが停止したときに新しいプライマリ サーバーとして使用できるように準備する必要があります。また、スムーズに復旧できるように、明確に定義された手順を文書化およびテストする必要もあります。 この準備手順を次に示します。

- 他のリージョンで、新しいプライマリ サーバーとして使用する SQL Database サーバーを特定します。 geo リストアの場合は、通常、データベースが配置されているリージョンの[ペア リージョン](../best-practices-availability-paired-regions.md)にあるサーバーです。 これにより、geo 復元操作中の余分なトラフィック コストを排除できます。
- ユーザーが新しいプライマリ データベースにアクセスするのに必要なサーバー レベルの IP ファイアウォール規則を特定し、必要に応じて定義します。
- 新しいプライマリ サーバーにユーザーをリダイレクトする方法を決めます。たとえば、接続文字列を変更したり、DNS エントリを変更したりすることでリダイレクトできます。
- 新しいプライマリ サーバーのマスター データベースに必要なログインを特定し、必要に応じて作成します。また、マスター データベースにあるこれらのログインに、適切なアクセス許可が付与されていることを確認します (ある場合)。 詳細については、[障害復旧後の SQL Database のセキュリティ](sql-database-geo-replication-security-config.md)に関するページをご覧ください。
- 更新して新しいプライマリ データベースにマップする必要があるアラート ルールを特定します。
- 現在のプライマリ データベースで監査の構成を文書化します
- [ディザスター リカバリーの訓練](sql-database-disaster-recovery-drills.md)を実行します。 geo リストアの障害をシミュレートするには、ソース データベースを削除するか、名前を変更します。これにより、アプリケーションの接続エラーが発生します。 フェールオーバー グループを使用して障害をシミュレートするには、データベースに接続されている Web アプリケーションまたは仮想マシンを無効にするか、データベースをフェールオーバーして、アプリケーションの接続エラーを発生させます。

## <a name="when-to-initiate-recovery"></a>復旧を開始するタイミング

復旧操作はアプリケーションに影響します。 SQL 接続文字列の変更または DNS によるリダイレクトが必要で、永続的にデータが失われる可能性があります。 そのため、アプリケーションの目標復旧時間が経過しても、障害が解決されない可能性が高い場合にのみ行ってください。 アプリケーションが実稼働環境にデプロイされている場合は、アプリケーションの健全性の定期的な監視を実行し、次のデータ ポイントを使用して、復旧が保証されていることをアサートします。

1. 接続の永続的な障害は、アプリケーション層からデータベースに渡ります。
2. Azure ポータルには、影響が広範囲に及ぶリージョンのインシデントに関するアラートが示されます。

> [!NOTE]
> フェールオーバー グループを使用しており、自動フェールオーバーを選択した場合は、回復プロセスは自動化され、アプリケーションに対して透過的になります。

ダウンタイムに対するアプリケーションの許容度およびビジネス責任に応じて、次の回復オプションを検討することができます。

[Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) を使用して、geo レプリケートされた最新の復元ポイントを取得します。

## <a name="wait-for-service-recovery"></a>サービスの回復を待機する

Azure チームはできるだけ早くサービスが利用できるようになるように作業しますが、根本原因によっては数時間から数日かかることがあります。  アプリケーションが長いダウンタイムを許容できる場合は、回復の完了を待つだけで済みます。 この場合、ユーザーによる操作は必要ありません。 現在のサービスの状態は、 [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。 リージョンの回復後に、アプリケーションの可用性が復元されます。

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>フェールオーバー グループの geo レプリケートされているセカンダリ サーバーへのフェールオーバー

アプリケーションのダウンタイムによってビジネス上の賠償責任が発生する場合は、フェールオーバー グループを使用する必要があります。 そうすれば、障害が発生した場合でも異なるリージョンでアプリケーションの可用性を迅速に復元できます。 チュートリアルについては、「[geo 分散型データベースを実装する](sql-database-implement-geo-distributed-database.md)」をご覧ください。

データベースの可用性を復元するには、サポートされているいずれかの方法を使用して、セカンダリ サーバーへのフェールオーバーを開始する必要があります。

geo レプリケートされたセカンダリ データベースへのフェールオーバーについては、次のいずれかを参照してください。

- [Azure ポータルを使用して、geo レプリケートされたセカンダリ サーバーにフフェールオーバーする](sql-database-geo-replication-portal.md)
- [PowerShell を使用したセカンダリ サーバーへのフェールオーバー](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Transact-SQL (T-SQL) を使用したセカンダリ サーバーへのフェールオーバー](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>geo リストアを使用した復旧

アプリケーションのダウンタイムがビジネス責任にならない場合は、アプリケーション データベースを回復する方法として [geo リストア](sql-database-recovery-using-backups.md)を使用できます。 geo リストアは、最新の geo 冗長バックアップからデータベースのコピーを作成します。

## <a name="configure-your-database-after-recovery"></a>復旧後のデータベースの構成

geo リストアを使用して障害から復旧する場合は、通常のアプリケーション機能を再開できるように新しいデータベースへの接続が正しく構成されていることを確認する必要があります。 復旧後のデータベースをすぐ運用できるようにするためのタスクのチェックリストを次に示します。

### <a name="update-connection-strings"></a>接続文字列を更新する

復旧後のデータベースは別のサーバーに存在するため、そのサーバーを示すようにアプリケーションの接続文字列を更新する必要があります。

接続文字列の変更の詳細については、 [接続ライブラリ](sql-database-libraries.md)の適切な開発言語を参照してください。

### <a name="configure-firewall-rules"></a>ファイアウォール規則を構成する

サーバーおよびデータベースで構成されているファイアウォール規則が、プライマリ サーバーとプライマリ データベースで構成されている規則と一致することを確認する必要があります。 詳しくは、「[ファイアウォール設定の構成方法 (Azure SQL Database)](sql-database-configure-firewall-settings.md) に関する記事をご覧ください。

### <a name="configure-logins-and-database-users"></a>ログインとデータベース ユーザーを構成する

アプリケーションで使用するすべてのログインが、復旧されたデータベースをホストしているサーバー上に存在することを確認する必要があります。 詳細については、[geo レプリケーションのセキュリティ構成](sql-database-geo-replication-security-config.md)に関するページをご覧ください。

> [!NOTE]
> ディザスター リカバリーの訓練中に、サーバーのファイアウォール規則とログイン (およびそのアクセス許可) を構成してテストする必要があります。 障害の間は、これらのサーバー レベル オブジェクトとその構成を使用できない場合があります。

### <a name="setup-telemetry-alerts"></a>テレメトリ アラートを設定する

既存のアラート ルールの設定を更新し、復旧されたデータベースおよび異なるサーバーにマップされるようにする必要があります。

データベースのアラート ルールの詳細については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」および「[サービス正常性を追跡する](../monitoring-and-diagnostics/insights-service-health.md)」を参照してください。

### <a name="enable-auditing"></a>監査を有効にする

データベースにアクセスするために監査が必要な場合は、データベースの復旧後に監査を有効にする必要があります。 詳しくは、「[SQL Database の監査](sql-database-auditing.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md) 」を参照してください。
- ビジネス継続性の設計および復旧シナリオについては、 [継続性のシナリオ](sql-database-business-continuity.md)
- 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)を参照してください。
