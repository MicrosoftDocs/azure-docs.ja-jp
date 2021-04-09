---
title: Azure PowerShell スクリプトの例
description: Azure PowerShell のサンプル スクリプトを使用して、Azure SQL Database と Azure SQL Managed Instance のリソースを作成、管理することができます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 7d732f0e42bc5eef8f72a2b9ce2adee28deb9a67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690974"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database と Azure SQL Managed Instance の Azure PowerShell サンプル
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance では、Azure PowerShell を使用してデータベース、インスタンス、プールを構成することができます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは AZ PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

次の表には、Azure SQL Database の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

|Link|説明|
|---|---|
|**単一データベースとエラスティック プールを作成して構成する**||
| [単一データベースを作成し、サーバーレベルのファイアウォール規則を構成する](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、単一データベースを作成し、サーバーレベルの IP ファイアウォール規則を構成します。 |
| [エラスティック プールを作成し、プールされたデータベースを移動する](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、エラスティック プールを作成し、プールされたデータベースを移動して、コンピューティング サイズを変更します。|
|**geo レプリケーションを構成しフェールオーバーする**||
| [アクティブ geo レプリケーションを使用して、単一データベースを構成し、フェールオーバーする](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、単一データベースのアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。 |
| [アクティブ geo レプリケーションを使用して、プールされたデータベースを構成およびフェールオーバーする](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、エラスティック プール内のデータベースのアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。 |
|**フェールオーバー グループを構成する**||
| [単一データベースのフェールオーバー グループを構成する](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、データベースとフェールオーバー グループを作成し、データベースをフェールオーバー グループに追加して、セカンダリ サーバーへのフェールオーバーをテストします。 |
| [エラスティック プールに対してフェールオーバー グループを構成する](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、データベースを作成し、それをエラスティック プールに追加し、そのエラスティック プールをフェールオーバー グループに追加して、セカンダリ サーバーへのフェールオーバーをテストします。 |
|**単一のデータベースとエラスティック プールをスケーリングする**||
| [単一のデータベースをスケーリングする](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、単一データベースのパフォーマンス メトリックを監視し、そのデータベースを上位のコンピューティング サイズにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成します。 |
| [エラスティック プールをスケーリングする](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、エラスティック プールのパフォーマンス メトリックを監視し、そのエラスティック プールを上位のコンピューティング サイズにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成します。 |
| **監査と脅威の検出** |
| [監査と脅威検出を構成する](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、データベースの監査と脅威検出ポリシーを構成します。 |
| **データベースを復元、コピー、インポートする**||
| [データベースを復元する](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、geo 冗長バックアップからデータベースを復元し、削除されたデータベースを最新のバックアップに復元します。 |
| [新しいサーバーにデータベースをコピーする](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトでは、新しいサーバーに既存のデータベースのコピーを作成します。 |
| [データベースを bacpac ファイルからインポートする](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、bacpac ファイルから Azure SQL Database にデータベースをインポートします。 |
| **データベース間でデータを同期する**||
| [データベース間でデータを同期する](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、Azure SQL Database の複数のデータベース間で同期するデータ同期を構成します。 |
| [SQL Database とオンプレミスの SQL Server のデータを同期する](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、Azure SQL Database のデータベースとオンプレミスの SQL Server データベースのデータ同期を構成します。 |
| [SQL データ同期の同期スキーマを更新する](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、データ同期の同期スキーマから項目を追加または削除します。 |
|||

[単一データベースの Azure PowerShell API](single-database-manage.md#powershell) に関するセクションを参照してください。 

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

次の表には、Azure SQL Managed Instance の Azure PowerShell サンプル スクリプトへのリンクが記載されています。

|Link|説明|
|---|---|
|**マネージド インスタンスを作成して構成する**||
| [マネージド インスタンスを作成して管理する](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | この PowerShell スクリプトでは、Azure PowerShell を使用してマネージド インスタンスを作成、管理する方法を紹介します。 |
| [Azure Resource Manager テンプレートを使用してマネージド インスタンスを作成および管理する](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、Azure PowerShell と Azure Resource Manager テンプレートを使用してマネージド インスタンスを作成、管理する方法を紹介します。|
| [データベースを別の geo リージョンのマネージド インスタンスに復元する](../managed-instance/scripts/restore-geo-backup.md) | この PowerShell スクリプトでは、1 つのデータベースのバックアップが作成されて、別のリージョンに復元されます。 これは、geo リストア ディザスター リカバリー シナリオとして知られています。 |
| **Transparent Data Encryption を構成する**||
| [Azure Key Vault の独自のキーを使用してマネージド インスタンスで Transparent Data Encryption を管理する](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトによって、Azure Key Vault のキーを使用し、Azure SQL Managed Instance の Bring Your Own Key シナリオで Transparent Data Encryption が構成されます。|
|**フェールオーバー グループを構成する**||
| [マネージド インスタンスのフェールオーバー グループを構成する](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、2 つのマネージド インスタンスを作成し、それらをフェールオーバー グループに追加して、プライマリ マネージド インスタンスからセカンダリ マネージド インスタンスへのフェールオーバーをテストします。 |
|||

[Azure SQL Managed Instance の PowerShell コマンドレット](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)に関するセクションを参照してください。

---

## <a name="additional-resources"></a>その他のリソース

このページに記載されている例では、Azure SQL リソースの作成と管理に [PowerShell コマンドレット](/powershell/module/az.sql/)を使用しています。 クエリを実行し、多くのデータベース タスクを実行するための追加のコマンドレットは、[sqlserver](/powershell/module/sqlserver/) モジュールにあります。 詳細については、「[SQL Server PowerShell](/sql/powershell/sql-server-powershell/)」を参照してください。