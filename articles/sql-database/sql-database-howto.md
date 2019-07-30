---
title: Azure SQL Database を構成する方法 | Microsoft Docs
description: Azure SQL Database を構成して管理する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: cd2406bb4825ac295a6f9a3a18ba466cdc20c55d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120854"
---
# <a name="how-to-use-azure-sql-database"></a>Azure SQL Database を使用する方法

このセクションでは、お客様の Azure SQL データベースを管理して構成するのに役立つさまざまなガイド、スクリプト、説明をご紹介します。 また、[単一データベース](sql-database-howto-single-database.md)と [Managed Instance](sql-database-howto-managed-instance.md) については個別の攻略ガイドもご覧いただけます。

## <a name="load-data"></a>データを読み込む

- [Azure 内での単一データベースまたはプールされたデータベースのコピー](sql-database-copy.md)
- [BACPAC からの DB のインポート](sql-database-import.md)
- [BACPAC への DB のエクスポート](sql-database-export.md)
- [BCP を使用したデータの読み込み](sql-database-load-from-csv-with-bcp.md)
- [ADF を使用したデータの読み込み](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>データ同期

- [SQL データ同期](sql-database-sync-data.md)
- [データ同期エージェント](sql-database-data-sync-agent.md)
- [スキーマ変更のレプリケート](sql-database-update-sync-schema.md)
- [OMS で監視する](sql-database-sync-monitor-oms.md)
- [データ同期のベスト プラクティス](sql-database-best-practices-data-sync.md)
- [データ同期のトラブルシューティング](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>監視とチューニング

- [手動チューニング](sql-database-performance-guidance.md)
- [DMV を使用したパフォーマンスの監視](sql-database-monitoring-with-dmvs.md)
- [クエリ ストアを使用したパフォーマンスの監視](sql-database-operate-query-store.md)
- [Intelligent Insights を使用したパフォーマンスのトラブルシューティング](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Intelligent Insights 診断ログの使用](sql-database-intelligent-insights-use-diagnostics-log.md)
- [インメモリ OLTP スペースの監視](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>拡張イベント

- [拡張イベント](sql-database-xevent-db-diff-from-svr.md)
- [イベント ファイルへの拡張イベントの格納](sql-database-xevent-code-event-file.md)
- [リング バッファーへの拡張イベントの格納](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>機能の構成

- [Azure AD 認証の構成](sql-database-aad-authentication-configure.md)
- [条件付きアクセスを構成する](sql-database-conditional-access.md)
- [多要素 AAD 認証](sql-database-ssms-mfa-authentication.md)
- [多要素認証の構成](sql-database-ssms-mfa-authentication-configure.md)
- [一時的なアイテム保持ポリシーの構成](sql-database-temporal-tables-retention-policy.md)
- [BYOK を使用した TDE の構成](transparent-data-encryption-byok-azure-sql-configure.md)
- [TDE BYOK キーの回転](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [TDE 保護機能の削除](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [インメモリ OLTP の構成](sql-database-in-memory-oltp-migration.md)
- [Azure Automation の構成](sql-database-manage-automation.md)

## <a name="develop-applications"></a>アプリケーションを開発する

- [接続](sql-database-libraries.md)
- [Spark コネクタの使用](sql-database-spark-connector.md)
- [アプリの認証](sql-database-client-id-keys.md)
- [エラー メッセージ](sql-database-develop-error-messages.md)
- [バッチ処理を使用したパフォーマンスの向上](sql-database-use-batching-to-improve-performance.md)
- [接続のガイダンス](sql-database-connectivity-issues.md)
- [DNS エイリアス](dns-alias-overview.md)
- [DNS エイリアスの PowerShell の設定](dns-alias-powershell.md)
- [ポート - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C と C++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>アプリケーションの設計

- [ディザスター リカバリーの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [エラスティック プールの設計](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [アプリのアップグレードの設計](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>マルチテナント SaaS アプリケーションの設計

- [SaaS の設計パターン](saas-tenancy-app-design-patterns.md)
- [SaaS Video Indexer](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS アプリのセキュリティ](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>次の手順

- [マネージド インスタンスの攻略ガイド](sql-database-howto-managed-instance.md)の詳細を確認する。
- [単一データベースの攻略ガイド](sql-database-howto-single-database.md)の詳細を確認する。
