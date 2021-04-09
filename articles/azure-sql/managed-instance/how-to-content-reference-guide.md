---
title: 構成および管理に関するコンテンツ参照
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance を構成および管理する方法を説明するコンテンツの参照ガイドです。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779766"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL Managed Instance のコンテンツ参照
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事には、Azure SQL Managed Instance を管理および構成する場合に役立つさまざまなガイド、スクリプト、および説明へのコンテンツ参照が含まれています。

## <a name="load-data"></a>データの読み込み

- [Azure SQL Managed Instance への移行](migrate-to-instance-from-sql-server.md): Azure SQL Managed Instance への移行のための推奨される移行プロセスおよびツールについて説明します。
- [Azure SQL Managed Instance への TDE 証明書の移行](tde-certificate-migrate.md): お客様の SQL Server データベースが Transparent Data Encryption (TDE) を使用して保護される場合、お客様が Azure に格納したいバックアップの暗号化を解除するために SQL Managed Instance によって使用される証明書を移行する必要があります。
- [BACPAC からの DB のインポート](../database/database-import.md)
- [BACPAC への DB のエクスポート](../database/database-export.md)
- [BCP を使用したデータの読み込み](../load-from-csv-with-bcp.md)
- [Azure Data Factory を使用してデータを読み込む](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>ネットワーク構成

- [サブネットのサイズを決める](vnet-subnet-determine-size.md): SQL Managed Instance をデプロイした後ではサブネットのサイズを変更することができないため、サブネットにデプロイする予定のマネージド インスタンスの数と種類に対して必要なアドレスの IP 範囲を計算する必要があります。 
- [新しい VNet とサブネットを作成する](virtual-network-subnet-create-arm-template.md): [ネットワーク要件](connectivity-architecture-overview.md#network-requirements)に従って、仮想ネットワークとサブネットを構成します。 
- [既存の VNet とサブネットを構成する](vnet-existing-add-subnet.md): ネットワーク要件を確認し、SQL Managed Instance をデプロイする既存の仮想ネットワークとサブネットを構成します。 
- [カスタム DNS を構成する](custom-dns-configure.md): データベース メール プロファイルのリンク サーバー経由で SQL Managed Instance からカスタム ドメインにアクセスする権限を外部リソースに付与するようにカスタム DNS を構成します。 
- [ネットワーク構成を同期する](azure-app-sync-network-configuration.md): [ご利用のアプリを Azure 仮想ネットワークに統合](../../app-service/web-sites-integrate-with-vnet.md)してから接続を確立できなくなった場合に、ネットワーク構成計画を更新します。
- [管理エンドポイント IP アドレスを検索する](management-endpoint-find-ip-address.md): SQL Managed Instance が管理目的で使用するパブリック エンドポイントを特定します。 
- [組み込みのファイアウォール保護を確認する](management-endpoint-verify-built-in-firewall.md): 必要なポート上でのみ SQL Managed Instance によってトラフィックが許可されること、およびその他の組み込みのファイアウォール規則を確認します。 
- [アプリケーションを接続する](connect-application-instance.md): アプリケーションを SQL Managed Instance に接続するさまざまなパターンについて説明します。

## <a name="feature-configuration"></a>機能の構成

- [Azure AD 認証の構成](../database/authentication-aad-configure.md)
- [条件付きアクセスの構成](../database/conditional-access-configure.md)
- [Multi-factor Azure AD 認証](../database/authentication-mfa-ssms-overview.md)
- [多要素認証の構成](../database/authentication-mfa-ssms-configure.md)
- [テンポラル アイテム保持ポリシーを構成する](../database/temporal-tables-retention-policy.md)
- [BYOK を使用した TDE の構成](../database/transparent-data-encryption-byok-configure.md)
- [TDE BYOK キーの回転](../database/transparent-data-encryption-byok-key-rotation.md)
- [TDE の保護機能を削除する](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [インメモリ OLTP の構成](../in-memory-oltp-configure.md)
- [Azure Automation の構成](../database/automation-manage.md)
- [トランザクション レプリケーション](replication-between-two-instances-configure-tutorial.md)では、マネージド インスタンス間でご利用のデータをレプリケートしたり、オンプレミスの SQL Server から SQL Managed Instance へ、またはその逆の方向にデータをレプリケートしたりすることができます。
- [脅威検出を構成する](threat-detection-configure.md) – [脅威検出](../database/threat-detection-overview.md)とは、SQL インジェクションや疑わしい場所からのアクセスなど、さまざまな潜在的な攻撃を検出する組み込みの Azure SQL Managed Instance 機能です。 
- [アラートの作成](alerts-create.md)では、SQL Managed Instance の監視対象メトリック (CPU 使用率、ストレージ領域の消費量、IOPS など) に関するアラートを設定できます。 

## <a name="monitoring-and-tuning"></a>監視とチューニング

- [手動チューニング](../database/performance-guidance.md)
- [DMV を使用したパフォーマンスの監視](../database/monitoring-with-dmvs.md)
- [クエリ ストアを使用したパフォーマンスの監視](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Intelligent Insights を使用したパフォーマンスのトラブルシューティング](../database/intelligent-insights-troubleshoot-performance.md)
- [Intelligent Insights 診断ログの使用](../database/intelligent-insights-use-diagnostics-log.md)
- [インメモリ OLTP スペースの監視](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>拡張イベント

- [拡張イベント](../database/xevent-db-diff-from-svr.md)
- [イベント ファイルへの拡張イベントの格納](../database/xevent-code-event-file.md)
- [リング バッファーへの拡張イベントの格納](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>アラート

- [マネージド インスタンスでのアラートの作成](alerts-create.md)

## <a name="operations"></a>Operations

- [SQL Managed Instance でユーザーによって開始される手動フェールオーバー](user-initiated-failover.md)

## <a name="develop-applications"></a>アプリケーションを開発する

- [接続](../database/connect-query-content-reference-guide.md#libraries)
- [Spark コネクタの使用](../../cosmos-db/spark-connector.md)
- [アプリの認証](../database/application-authentication-get-client-id-keys.md)
- [バッチ処理を使用したパフォーマンスの向上](../performance-improve-use-batching.md)
- [接続のガイダンス](../database/troubleshoot-common-connectivity-issues.md)
- [DNS エイリアス](../database/dns-alias-overview.md)
- [PowerShell を使用して DNS エイリアスを設定する](../database/dns-alias-powershell-create.md)
- [ポート - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C と C++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>アプリケーションの設計

- [ディザスター リカバリーの設計](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [エラスティック プールの設計](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [アプリのアップグレードの設計](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>マルチテナント SaaS アプリケーションの設計

- [SaaS の設計パターン](../database/saas-tenancy-app-design-patterns.md)
- [SaaS Video Indexer](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS アプリのセキュリティ](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>次のステップ

[SQL Managed Instance のデプロイ](instance-create-quickstart.md)から始めます。