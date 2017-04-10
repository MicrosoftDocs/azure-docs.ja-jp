---
title: "Azure PowerShell スクリプト - geo レプリケーションの設定 - 単一の SQL Database | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - PowerShell を使用した単一の Azure SQL Database のアクティブ geo レプリケーションの設定"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: bae3a92d861570a67d3d1af79822ff21b9c748af
ms.lasthandoff: 03/30/2017

---

# <a name="configure-active-geo-replication-for-a-single-azure-sql-database-using-powershell"></a>PowerShell を使用して単一の Azure SQL Database のアクティブ geo レプリケーションを構成する

この PowerShell のサンプル スクリプトは、単一のデータベースのアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-scripts"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover-database/setup-geodr-and-failover-database.ps1 "単一のデータベースのアクティブ geo レプリケーションを設定する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myPrimaryResourceGroup"
Remove-AzureRmResourceGroup -ResourceGroupName "mySecondaryResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | データベースまたはエラスティック プールをホストする論理サーバーを作成します。 |
| [New-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlelasticpool) | 論理サーバー内にエラスティック プールを作成します。 |
| [Set-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabase) | データベースのプロパティを更新するか、エラスティック プールに対して、エラスティック プールから、またはエラスティック プール間でデータベースを移動します。 |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabasesecondary)| 既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldatabase)| 1 つまたは複数のデータベースを取得します。 |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabasesecondary)| セカンダリ データベースをプライマリに切り替えて、フェールオーバーを開始します。|
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldatabasereplicationlink) | Azure SQL Database とリソース グループまたは SQL Server 間の geo レプリケーション リンクを取得します。 |
| [Remove-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/remove-azurermsqldatabasesecondary) | SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。

