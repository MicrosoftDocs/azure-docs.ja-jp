---
title: "SQL Database の障害復旧 | Microsoft Docs"
description: "Azure SQL Database のアクティブ地理レプリケーションと 地理リストア機能を使用して、地域のデータ センターの停止や障害からデータベースを復旧する方法について説明します。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 4800960e-3f9d-40ce-9e55-fb7f2784c067
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 164f3affdf0622653a0a7dcc2a5e886ab855ced1
ms.lasthandoff: 02/17/2017


---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Azure SQL Database を復元する、またはセカンダリにフェールオーバーする
Azure SQL Database は、障害から回復するために次の機能を備えています。

* [アクティブ地理レプリケーションを選択するとき](sql-database-geo-replication-overview.md)
* [地理リストア](sql-database-recovery-using-backups.md#point-in-time-restore)

ビジネス継続性のシナリオと、こうしたシナリオをサポートする機能の詳細については、 [ビジネス継続性](sql-database-business-continuity.md)に関するページをご覧ください。

### <a name="prepare-for-the-event-of-an-outage"></a>障害に備える
アクティブ地理レプリケーションまたは 地理冗長バックアップのいずれかを使用して、他のデータリージョンに適切に復旧するには、必要な場合に備えて、サーバーを、他のデータ センターが停止したときに新しいプライマリ サーバーとして使用できるように準備する必要があります。また、スムーズに復旧できるように、明確に定義された手順を文書化およびテストする必要もあります。 この準備手順を次に示します。

* 他のリージョンで、新しいプライマリ サーバーとして使用する論理サーバーを特定します。 アクティブ地理レプリケーションを使用する場合、これは少なくとも 1 つで、ほとんどの場合、各セカンダリ サーバーになります。 地理リストアの場合は、通常、データベースが配置されているリージョンの [ペア リージョン](../best-practices-availability-paired-regions.md) にあるサーバーです。
* ユーザーが新しいプライマリ データベースにアクセスするのに必要なサーバー レベルのファイアウォール規則を特定し、必要に応じて定義します。
* 新しいプライマリ サーバーにユーザーをリダイレクトする方法を決めます。たとえば、接続文字列を変更したり、DNS エントリを変更したりすることでリダイレクトできます。
* 新しいプライマリ サーバーのマスター データベースに必要なログインを特定し、必要に応じて作成します。また、マスター データベースにあるこれらのログインに、適切なアクセス許可が付与されていることを確認します (ある場合)。 詳細については、[障害復旧後の SQL Database のセキュリティ](sql-database-geo-replication-security-config.md)に関するページをご覧ください。
* 更新して新しいプライマリ データベースにマップする必要があるアラート ルールを特定します。
* 現在のプライマリ データベースで監査の構成を文書化します
* [障害復旧の訓練](sql-database-disaster-recovery-drills.md)を実行します。 地理リストアの障害をシミュレートするには、ソース データベースを削除するか、名前を変更します。これにより、アプリケーションの接続エラーが発生します。 アクティブ地理レプリケーションの障害をシミュレートするには、データベースに接続されている Web アプリケーションまたは仮想マシンを無効にするか、データベースをフェールオーバーして、アプリケーションの接続エラーを発生させます。

## <a name="when-to-initiate-recovery"></a>復旧を開始するタイミング
復旧操作はアプリケーションに影響します。 SQL 接続文字列の変更または DNS によるリダイレクトが必要で、永続的にデータが失われる可能性があります。 そのため、アプリケーションの目標復旧時間が経過しても、障害が解決されない可能性が高い場合にのみ行ってください。 アプリケーションが実稼働環境にデプロイされている場合は、アプリケーションの健全性の定期的な監視を実行し、次のデータ ポイントを使用して、復旧が保証されていることをアサートします。

1. 接続の永続的な障害は、アプリケーション層からデータベースに渡ります。
2. Azure ポータルには、影響が広範囲に及ぶリージョンのインシデントに関するアラートが示されます。
3. Azure SQL Database サーバーは機能低下としてマークされます。

ダウンタイムに対するアプリケーションの許容度およびビジネス責任に応じて、次の回復オプションを検討することができます。

[Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) を使用して、地理レプリケートされた最新の復元ポイントを取得します。

## <a name="wait-for-service-recovery"></a>サービスの回復を待機する
Azure チームはできるだけ早くサービスが利用できるようになるように作業しますが、根本原因によっては数時間から数日かかることがあります。  アプリケーションが長いダウンタイムを許容できる場合は、回復の完了を待つだけで済みます。 この場合、ユーザーによる操作は必要ありません。 現在のサービスの状態は、 [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。 リージョンの回復後に、アプリケーションの可用性が復元されます。

## <a name="failover-to-geo-replicated-secondary-database"></a>地理レプリケートされたセカンダリ データベースへのフェールオーバー
アプリケーションのダウンタイムによってビジネス責任が発生する場合は、アプリケーションで地理レプリケートされたデータベースを使用する必要があります。 そうすれば、障害が発生した場合でも異なるリージョンでアプリケーションの可用性を迅速に復元できます。 [地理レプリケーションを構成](sql-database-geo-replication-portal.md)する方法をご覧ください。

データベースの可用性を復元するには、サポートされているいずれかの方法を使用して、地理レプリケートされたセカンダリ データベースへのフェールオーバーを開始する必要があります。

地理レプリケートされたセカンダリ データベースへのフェールオーバーについては、次のいずれかを参照してください。

* [Azure ポータルを使用して Azure SQL Database の地理レプリケーションを構成する](sql-database-geo-replication-portal.md)
* [PowerShell を使用して Azure SQL Database の地理レプリケーションを構成する](sql-database-geo-replication-powershell.md)
* [Transact-SQL を使用して Azure SQL Database の地理レプリケーションを構成する](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>地理リストアを使用した復旧
アプリケーションのダウンタイムがビジネス責任にならない場合は、アプリケーション データベースを回復する方法として 地理リストアを使用できます。 地理リストアは、最新の地理冗長バックアップからデータベースのコピーを作成します。

新しいリージョンへのデータベースの地理リストアについては、次のいずれかを参照してください。

* [Geo-Restore a database to a new region using Azure Portal (Azure ポータルを使用して新しいリージョンにデータベースを地理リストアする)](sql-database-geo-restore-portal.md)
* [Geo-Restore a database to a new region using PowerShell (PowerShell を使用して新しいリージョンにデータベースを地理リストアする)](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>復旧後のデータベースの構成
地理レプリケーション フェールオーバーまたは地理リストアを使用して障害から復旧する場合は、通常のアプリケーション機能を再開できるように新しいデータベースへの接続が正しく構成されていることを確認する必要があります。 復旧後のデータベースをすぐ運用できるようにするためのタスクのチェックリストを次に示します。

### <a name="update-connection-strings"></a>接続文字列を更新する
復旧後のデータベースは別のサーバーに存在するため、そのサーバーを示すようにアプリケーションの接続文字列を更新する必要があります。

接続文字列の変更の詳細については、 [接続ライブラリ](sql-database-libraries.md)の適切な開発言語を参照してください。

### <a name="configure-firewall-rules"></a>ファイアウォール規則を構成する
サーバーおよびデータベースで構成されているファイアウォール規則が、プライマリ サーバーとプライマリ データベースで構成されている規則と一致することを確認する必要があります。 詳細については、「 [方法: ファイアウォール設定を構成する (Azure SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

### <a name="configure-logins-and-database-users"></a>ログインとデータベース ユーザーを構成する
アプリケーションで使用するすべてのログインが、復旧されたデータベースをホストしているサーバー上に存在することを確認する必要があります。 詳細については、 [地理レプリケーションのセキュリティ構成](sql-database-geo-replication-security-config.md)に関するページをご覧ください。

> [!NOTE]
> 障害復旧の訓練中に、サーバーのファイアウォール規則とログイン (およびそのアクセス許可) を構成してテストする必要があります。 障害の間は、これらのサーバー レベル オブジェクトとその構成を使用できない場合があります。
> 
> 

### <a name="setup-telemetry-alerts"></a>製品利用統計情報アラートをセットアップする
既存のアラート ルールの設定を更新し、復旧されたデータベースおよび異なるサーバーにマップされるようにする必要があります。

データベースのアラート ルールの詳細については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」および「[サービス正常性を追跡する](../monitoring-and-diagnostics/insights-service-health.md)」を参照してください。

### <a name="enable-auditing"></a>監査を有効にする
データベースにアクセスするために監査が必要な場合は、データベースの復旧後に監査を有効にする必要があります。 クライアント アプリケーションで *.database.secure.windows.net パターンのセキュリティで保護された接続文字列を使用している場合は、監査が必要であることを表しています。 詳しくは、「[SQL Database の監査](sql-database-auditing.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* Azure SQL Database 自動バックアップの詳細については、「 SQL Database 自動バックアップ](sql-database-automated-backups.md)
* ビジネス継続性の設計および復旧シナリオについては、 [継続性のシナリオ](sql-database-business-continuity.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)


