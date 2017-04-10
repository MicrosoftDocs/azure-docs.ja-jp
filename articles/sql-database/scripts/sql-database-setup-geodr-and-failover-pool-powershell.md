---
title: "Azure PowerShell スクリプト - geo レプリケーションの設定 - プールされた SQL データベース | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - PowerShell を使用して、プールされた Azure SQL Database のアクティブ geo レプリケーションを設定する"
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
ms.openlocfilehash: ccb5b0545dd8b0eea47b17a06e18e4ae63024ea3
ms.lasthandoff: 03/30/2017

---

# <a name="configure-active-geo-replication-for-a-pooled-azure-sql-database-using-powershell"></a>PowerShell を使用してプールされた Azure SQL Database のアクティブ geo レプリケーションを構成する

この PowerShell のサンプル スクリプトは、エラスティック プール内のデータベースのアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-scripts"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover-pool/setup-geodr-and-failover-pool.ps1 "エラスティック プールのアクティブ geo レプリケーションを設定する")]

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
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | 単一のデータベースまたはプールされたデータベースとして論理サーバーにデータベースを作成します。 |
| [Set-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabase) | データベースのプロパティを更新するか、エラスティック プールに対して、エラスティック プールから、またはエラスティック プール間でデータベースを移動します。 |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabasesecondary)| 既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldatabase)| 1 つまたは複数のデータベースを取得します。 |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabasesecondary)| セカンダリ データベースをプライマリに切り替えて、フェールオーバーを開始します。|
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldatabasereplicationlink) | Azure SQL Database とリソース グループまたは SQL Server 間の geo レプリケーション リンクを取得します。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。

