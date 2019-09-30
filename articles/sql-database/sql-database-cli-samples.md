---
title: SQL Database の Azure CLI スクリプト サンプル | Microsoft Docs
description: Azure SQL Database サーバー、エラスティック プール、データベース、ファイアウォールを作成し、管理するための Azure CLI スクリプト サンプル。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 5366fb1d32020bfbcfaba36c60c0eb5441e92070
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71055220"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure SQL Database 用の Azure CLI サンプル

Azure SQL Database は、<a href="/cli/azure">Azure CLI</a> を使用して構成できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="single-database--elastic-poolstabsingle-database"></a>[単一データベースとエラスティック プール](#tab/single-database)

次の表には、Azure SQL Database の Azure CLI スクリプト例へのリンクが含まれています。

| |  |
|---|---|
|**単一のデータベースとエラスティック プールを作成する**||
| [単一のデータベースを作成し、ファイアウォール規則を構成する](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | この CLI サンプル スクリプトでは、1 つの Azure SQL データベースが作成され、サーバーレベルのファイアウォール規則が構成されます。 |
| [エラスティック プールを作成し、プールされたデータベースを移動する](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | この CLI サンプル スクリプトでは、SQL エラスティック プールが作成され、プールされた Azure SQL データベースが移動され、コンピューティング サイズが変更されます。|
|**単一のデータベースとエラスティック プールをスケーリングする**||
| [単一のデータベースをスケーリングする](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | この CLI サンプル スクリプトでは、データベースのサイズ情報の問い合わせ後、1 つの Azure SQL データベースが別のコンピューティング サイズにスケーリングします。 |
| [エラスティック プールをスケーリングする](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | この CLI サンプル スクリプトでは、SQL エラスティック プールが別のコンピューティング サイズにスケーリングします。  |
|**フェールオーバー グループ**||
| [フェールオーバー グループへの単一データベースの追加](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| この CLI スクリプトでは、データベースとフェールオーバー グループを作成し、データベースをフェールオーバー グループに追加して、セカンダリ サーバーへのフェールオーバーをテストします。|
|||

[単一データベースの Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) に関するセクションを参照してください。

## <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

次の表には、Azure SQL Database Managed Instance の Azure CLI スクリプト例へのリンクが含まれています。

| |  |
|---|---|
| [マネージド インスタンスを作成する](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | この CLI スクリプトでは、マネージド インスタンスの作成方法を紹介します。 |
| [マネージド インスタンスを更新する](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | この CLI スクリプトでは、マネージド インスタンスを更新する方法を紹介します。 |
| [別のマネージド インスタンスにデータベースを移動する](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | この CLI スクリプトでは、あるインスタンスから別のインスタンスにデータベースのバックアップを復元する方法を紹介します。 |
|||

詳細については、[マネージド インスタンスの Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) に関するセクションを参照してください。また、[こちらで他の例](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)もご覧いただけます。

---
