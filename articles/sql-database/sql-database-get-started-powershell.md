---
title: "Azure PowerShell: SQL データベースを作成する | Microsoft Docs"
description: "Azure Portal で SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、データベースを作成する方法について説明します。"
keywords: "SQL データベース チュートリアル, SQL データベースの作成"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 70cf89a5832aee2d0c303e0d40e104d84837b50c
ms.lasthandoff: 03/25/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>PowerShell を使用して単一の Azure SQL データベースを作成する

PowerShell は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、PowerShell を使用して Azure SQL データベースを [Azure SQL Database 論理サーバー](sql-database-features.md)内の [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)にデプロイする方法について詳しく説明します。

開始する前に、最新バージョンの PowerShell がインストールされていることを確認してください。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。 

## <a name="log-in-to-azure"></a>Azure へのログイン

[Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>論理サーバーの作成

[New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) コマンドで [Azure SQL Database 論理サーバー](sql-database-features.md)を作成します。 論理サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 次の例では、管理者ログイン `ServerAdmin` とパスワード `ChangeYourAdminPassword1` を使用し、ランダムに名前を付けたサーバーをリソース グループ内に作成しています。 これらの定義済みの値は、必要に応じて別の値に置き換えてください。

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

[New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) コマンドで [Azure SQL Database のサーバーレベルのファイアウォール規則](sql-database-firewall-configure.md)を作成します。 サーバーレベルのファイアウォール規則を作成すると、SQL Server Management Studio や SQLCMD ユーティリティのような外部アプリケーションから、SQL Database サービスのファイアウォールを介して SQL データベースに接続できます。 次の例では、定義済みのアドレス範囲に対するファイアウォール規則が作成されます。この例でのアドレス範囲は、IP アドレスの範囲として可能な全範囲です。 これらの定義済みの値は、自分の外部 IP アドレスや IP アドレス範囲に置き換えてください。 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>空のデータベースの作成

[New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) コマンドでサーバー内に [S0 パフォーマンス レベル](sql-database-service-tiers.md)の空の SQL データベースを作成します。 次の例では、`mySampleDatabase` というデータベースが作成されます。 この定義済みの値は、必要に応じて別の値に置き換えてください。

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイック スタートは、このクイック スタートに基づいています。 引き続きクイック スタートまたはチュートリアルの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 作業する予定がない場合は、次のコマンドを使用して、このクイック スタートで作成したすべてのリソースを削除してください。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

- SQL Server Management Studio を使用して接続とクエリを実行するには、[SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)に関するページを参照してください。
- Visual Studio を使用して接続を行うには、[Visual Studio を使用した接続とクエリ](sql-database-connect-query.md)に関するページを参照してください。
* SQL Database の技術的な概要については、[SQL Database サービスの説明](sql-database-technical-overview.md)に関するページを参照してください。

