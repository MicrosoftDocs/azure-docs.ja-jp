---
title: "Azure PowerShell スクリプト - データベースの監査と脅威検出の構成 | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - PowerShell を使用して、SQL Database の監査と脅威検出を構成します。"
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
ms.openlocfilehash: 736da582caea53d236add0fdb6ebd46f9a58b64a
ms.lasthandoff: 03/30/2017

---

# <a name="configure-sql-database-auditing-and-threat-detection-using-powershell"></a>PowerShell を使用して SQL Database の監査と脅威検出を構成する

この PowerShell のサンプル スクリプトは、SQL Database の監査と脅威検出を構成します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1 "監査と脅威検出の構成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | データベースまたはエラスティック プールをホストする論理サーバーを作成します。 |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | 単一のデータベースまたはプールされているデータベースとして論理サーバーにデータベースを作成します。 |
| [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.4.0/new-azurermstorageaccount) | ストレージ アカウントを作成します。 |
| [Set-AzureRmSqlDatabaseAuditingPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabaseauditingpolicy) | データベースの監査ポリシーを設定します。 |
| [Set-AzureRmSqlDatabaseThreatDetectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabasethreatdetectionpolicy) | データベースに脅威検出ポリシーを設定します。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。

