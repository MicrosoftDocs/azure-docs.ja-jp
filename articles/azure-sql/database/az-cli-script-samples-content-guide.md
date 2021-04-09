---
title: Azure CLI スクリプトの例
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database と Azure SQL Managed Instance を作成、管理するための Azure CLI サンプル スクリプト
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 439167f29bb53d4a6e90b95826faa56e3c3170da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563360"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance のための Azure CLI サンプル 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と SQL Managed Instance は、<a href="/cli/azure">Azure CLI</a> を使用して構成できます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

次の表には、Azure SQL Database の単一データベースとプールされたデータベースを管理する Azure CLI サンプル スクリプトへのリンクが記載されています。 

|領域|説明|
|---|---|
|**Azure SQL Database にデータベースを作成する**||
| [単一のデータベースを作成し、ファイアウォール規則を構成する](scripts/create-and-configure-database-cli.md) | SQL Database を作成し、サーバーレベルのファイアウォール規則を構成します。 |
| [エラスティック プールを作成し、プールされたデータベースを移動する](scripts/move-database-between-elastic-pools-cli.md) | エラスティック プールを作成し、プールされたデータベースを移動して、コンピューティング サイズを変更します。 |
|**Azure SQL Database のデータベースをスケーリングする**||
| [単一のデータベースをスケーリングする](scripts/monitor-and-scale-database-cli.md) | データベースのサイズ情報の照会後、SQL Database 内のデータベースを別のコンピューティング サイズにスケーリングします。 |
| [エラスティック プールをスケーリングする](scripts/scale-pool-cli.md) | SQL エラスティック プールを別のコンピューティング サイズにスケーリングします。 |
|**geo レプリケーションを構成しフェールオーバーする**||
| [フェールオーバー グループに単一データベースを追加する](scripts/add-database-to-failover-group-cli.md)| データベースとフェールオーバー グループを作成し、データベースをフェールオーバー グループに追加して、セカンダリ サーバーへのフェールオーバーをテストします。 |
| [エラスティック プールに対してフェールオーバー グループを構成する](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | データベースを作成し、それをエラスティック プールに追加し、そのエラスティック プールをフェールオーバー グループに追加して、セカンダリ サーバーへのフェールオーバーをテストします。 |
| [アクティブ geo レプリケーションを使用して、単一データベースを構成し、フェールオーバーする](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Azure SQL Database のデータベースのアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。 |
| [アクティブ geo レプリケーションを使用して、プールされたデータベースを構成およびフェールオーバーする](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| エラスティック プール内のデータベース用にアクティブ geo レプリケーションを構成してから、そのデータベースをセカンダリ レプリカにフェールオーバーします。 |
| **監査と脅威の検出** |
| [監査と脅威検出を構成する](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Azure SQL Database にあるデータベースの監査と脅威検出ポリシーを構成します。 |
| **データベースをバックアップ、復元、コピー、インポートする**||
| [データベースをバックアップする](../../sql-database/scripts/sql-database-backup-database-cli.md)| Azure Storage バックアップに SQL Database のデータベースをバックアップします。 |
| [データベースを復元する](../../sql-database/scripts/sql-database-restore-database-cli.md)| geo 冗長バックアップから SQL Database のデータベースを復元し、削除されたデータベースを最新のバックアップに復元します。 |
| [新しいサーバーにデータベースをコピーする](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | 新しいサーバーの SQL Database に既存のデータベースのコピーを作成します。 |
| [データベースを BACPAC ファイルからインポートする](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| BACPAC ファイルから SQL Database にデータベースをインポートします。 |
|||

[単一データベースの Azure CLI API](single-database-manage.md#the-azure-cli) に関するセクションを参照してください。

# <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

次の表には、Azure SQL Managed Instance の Azure CLI サンプル スクリプトへのリンクが記載されています。

|領域|説明|
|---|---|
| **SQL Managed Instance を作成する**||
| [SQL Managed Instance を作成する](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| SQL Managed Instance を作成します。 |
| **透過的なデータ暗号化 (TDE) の構成**||
| [Azure Key Vault を使用して SQL Managed Instance で Transparent Data Encryption を管理する](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| SQL Managed Instance で、Azure Key Vault とさまざまなキーのシナリオを使用して Transparent Data Encryption (TDE) を構成します。 |
|**フェールオーバー グループを構成する**||
| [SQL Managed Instance のフェールオーバー グループを構成する](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | SQL Managed Instance のインスタンスを 2 つ作成し、それらをフェールオーバー グループに追加して、プライマリ SQL Managed Instance からセカンダリ SQL Managed Instance へのフェールオーバーをテストします。 |
|||

その他の SQL Managed Instance サンプルについては、データベースの[作成](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli)、[更新](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli)、[移動](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)、[操作](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)に関するスクリプトを参照してください。

[SQL Managed Instance の Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances) について理解を深めましょう。

---