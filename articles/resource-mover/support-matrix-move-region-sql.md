---
title: Azure Resource Mover を使用して Azure SQL リソースをリージョン間で移動するためのサポート。
description: Azure Resource Mover を使用して Azure SQL リソースをリージョン間で移動するためのサポートを確認します。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452078"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Azure SQL リソースをリージョン間で移動するためのサポート

この記事では、Azure Resource Mover を使用して Azure SQL リソースを Azure リージョン間で移動するためのサポートと前提条件について説明します。

## <a name="requirements"></a>必要条件

要件の概要については、次の表で説明しています。

**機能** | **サポートされるかどうか** | **詳細**
--- | --- | ---
**Azure SQL Database Hyperscale** | サポートされていません | Resource Mover を使用して Azure SQL Hyperscale サービス レベルでデータベースを移動することはできません。
**ゾーン冗長性** | サポートされています |  サポートされている移動オプション:<br/><br/> - ゾーン冗長性をサポートしているリージョン間。<br/><br/> - ゾーン冗長性をサポートしていないリージョン間。<br/><br/> - ゾーン冗長性をサポートしているリージョンからゾーン冗長性をサポートしていないリージョン間。<br/><br/> - ゾーン冗長性をサポートしていないリージョンからゾーン冗長性をサポートしているリージョン間。 
**データ同期** | ハブ/同期データベース:サポートされていません<br/><br/> 同期メンバー:サポートされています。 | 同期メンバーが移動される場合は、新しいターゲット データベースへのデータ同期を設定する必要があります。
**既存の geo レプリケーション** | サポートされています | 既存の geo レプリカは、ターゲット リージョンの新しいプライマリに再マップされます。<br/><br/> 移動後にシード処理を初期化する必要があります。 [詳細情報](../azure-sql/database/active-geo-replication-configure-portal.md)
**Bring Your Own Key (BYOK) による Transparent Data Encryption (TDE)** | サポートされています | キー コンテナーのリージョン間の移動については、[こちら](../key-vault/general/move-region.md)を参照してください。
**サービス マネージド キーを使用する TDE** | サポートされています。 |  キー コンテナーのリージョン間の移動については、[こちら](../key-vault/general/move-region.md)を参照してください。
**動的データ マスク ルール** | サポートされています。 | ルールは、移動の一部としてターゲット リージョンに自動的にコピーされます。 [詳細については、こちらを参照してください](../azure-sql/database/dynamic-data-masking-configure-portal.md)。
**Advanced Data Security** | サポートされていません。 | 対処法:ターゲット リージョンで SQL Server レベルで設定します。 [詳細については、こちらを参照してください](../azure-sql/database/azure-defender-for-sql.md)。
**ファイアウォール規則** | サポートされていません。 | 対処法:ターゲット リージョンで SQL Server 用のファイアウォール規則を設定します。 データベースレベルのファイアウォール規則は、ソース サーバーからターゲット サーバーにコピーされます。 [詳細については、こちらを参照してください](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)。
**監査ポリシー** | サポートされていません。 | 移動後に、ポリシーは既定値にリセットされます。 再設定方法については、[こちら](../azure-sql/database/auditing-overview.md)を参照してください。
**バックアップ保有期間** | サポートされています。 | ソース データベースのバックアップ保有ポリシーは、ターゲット データベースに引き継がれます。 移動後の設定の変更方法については、[こちら](../azure-sql/database/long-term-backup-retention-configure.md)を参照してください。
**自動チューニング** | サポートされていません。 | 対処法:移動後に、自動チューニング設定を行います。 [詳細については、こちらを参照してください](../azure-sql/database/automatic-tuning-enable.md)。
**データベースのアラート** | サポートされていません。 | 対処法:移動後に、アラートを設定します。 [詳細については、こちらを参照してください](../azure-sql/database/alerts-insights-configure-portal.md)。
**Azure SQL Server 拡張データベース** | サポートされていません | Resource Mover を使用して SQL Server 拡張データベースを移動することはできません。
**Azure Synapse Analytics** | サポートされていません | Azure Synapse Analytics を Resource Mover で移動することはできません。
## <a name="next-steps"></a>次の手順

Resource Mover を使用して [Azure SQL リソース](tutorial-move-region-sql.md)を別のリージョンに移動してみる