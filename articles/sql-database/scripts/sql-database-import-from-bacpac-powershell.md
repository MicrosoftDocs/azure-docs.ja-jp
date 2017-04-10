---
title: "Azure PowerShell スクリプト - bacpac から SQL データベースへのインポート | Microsoft Docs"
description: "Azure PowerShell サンプル スクリプト - PowerShell を使用した bacpac から SQL データベースへのインポート"
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
ms.openlocfilehash: 0afe2d9e8cb6d26c43830df0ebfe2cea5c2f7665
ms.lasthandoff: 03/30/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>PowerShell を使用した bacpac から SQL データベースへのインポート

この PowerShell のサンプル スクリプトでは、bacpac からデータベースをインポートします。  

[!INCLUDE [sample-cli-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "SQL データベースの作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup]() | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmSqlServer]() | SQL Database をホストする論理サーバーを作成します。 |
| [New-AzureRmSqlServerFirewallRule]() | サーバー上のすべての SQL データベースに対する入力した IP アドレス範囲からのアクセスを許可するファイアウォール規則を作成します。 |
| [New-AzureRmSqlDatabase]() | 論理サーバー内に SQL Database を作成します。 |
| [Remove-AzureRmResourceGroup]() | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
