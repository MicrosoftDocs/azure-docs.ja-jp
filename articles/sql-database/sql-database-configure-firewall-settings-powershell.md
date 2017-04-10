---
title: "PowerShell: Azure SQL Database ファイアウォール規則の構成 | Microsoft Docs"
description: "PowerShell を使用して、Azure SQL データベースにアクセスする IP アドレス用にサーバーレベルのファイアウォール規則を構成する方法について説明します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: a88c38f01b62dee9454f612e795b6722db2547e8
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>PowerShell を使用して Azure SQL Database のサーバーレベルのファイアウォール規則を構成する

Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。 データベースへのアクセスを選択的に許可するには、SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [!IMPORTANT]
> Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。 ファイアウォール規則と Azure からの接続を有効にする方法については、「 [Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。 Azure クラウド境界内で接続を行う場合、追加の TCP ポートを開くことが必要な場合があります。 詳細については、「 [Ports beyond 1433 for ADO.NET 4.5, and SQL Database V12 (ADO.NET 4.5、SQL Database V12 における 1433 以外のポート)](sql-database-develop-direct-route-ports-adonet-v12.md)」の「SQL Database V12: 外部と内部」セクションを参照してください。
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>サーバー ファイアウォール規則の作成
Azure PowerShell を使用すると、サーバーレベルのファイアウォール規則を作成、更新、および削除できます。

新しいサーバーレベルのファイアウォール規則を作成するには、[New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) コマンドレットを実行します。 次の例では、サーバー Contoso の IP アドレス範囲を有効にします。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

既存のサーバーレベルのファイアウォール規則を変更するには、[Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx) コマンドレットを実行します。 次の例では、ContosoFirewallRule という名前の規則で許容される IP アドレスの範囲を変更します。

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -StartIPAddress 192.168.1.4 -EndIPAddress 192.168.1.10 -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'

既存のサーバーレベルのファイアウォール規則を削除するには、[Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx) コマンドレットを実行します。 次の例では、ContosoFirewallRule という名前の規則を削除します。

    Remove-AzureRmSqlServerFirewallRule -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>PowerShell を使用したファイアウォール規則の管理
ファイアウォール規則の管理に PowerShell を使用することもできます。 詳細については、次のトピックを参照してください。

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>次のステップ
Transact-SQL を使用して、サーバーレベルおよびデータベースレベルのファイアウォール規則を作成する方法の詳細については、「 [TSQL を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-tsql.md)」をご覧ください。

他の方法でサーバーレベルのファイアウォール規則を作成する方法の詳細については、次の記事をご覧ください。

* [Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings.md)
* [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「[初めての Azure SQL Database](sql-database-get-started.md)」をご覧ください。
オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、 [SQL Database のクライアント クイックスタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関するページをご覧ください。
データベースに移動する方法については、 [データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関するページをご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [データベースの保護](sql-database-security-overview.md)
* [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

