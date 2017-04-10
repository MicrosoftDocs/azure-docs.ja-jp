---
title: "REST API: Azure SQL Database のサーバーレベルのファイアウォール規則 | Microsoft Docs"
description: "REST API を使用して、Azure SQL データベースにアクセスする IP アドレス用にサーバーレベルのファイアウォール規則を構成する方法について説明します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
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
ms.openlocfilehash: e164e1da7ec5f2da157900c86b3313fff1affed9
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>REST API を使用して Azure SQL Database ファイアウォールを構成する

Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。 データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [!IMPORTANT]
> Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。 ファイアウォール規則と Azure からの接続を有効にする方法については、「 [Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。 Azure クラウド境界内で接続を行う場合、追加の TCP ポートを開くことが必要な場合があります。 詳細については、「 **ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート** 」の「 [SQL Database V12: 外部と内部](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>REST API を使用してサーバー レベルのファイアウォール規則を管理する
1. REST API を介してファイアウォール規則を管理する場合、認証される必要があります。 詳細については、「 [Azure Resource Manager API を使用した承認の開発者ガイド](../azure-resource-manager/resource-manager-api-authentication.md)」をご覧ください。
2. サーバーレベルの規則は、REST API を使用して、作成、更新、または削除できます。
   
    サーバーレベルのファイアウォール規則を作成または更新するには、次を使用して PUT メソッドを実行します。
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    要求本文
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    既存のサーバーレベルのファイアウォール規則を削除するには、次を使用して、DELETE メソッドを実行します。

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>REST API を使用したファイアウォール規則の管理
* [ファイアウォール規則の作成または更新](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [ファイアウォール規則の削除](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [ファイアウォール規則の取得](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [すべてのファイアウォール規則の表示](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>次のステップ
Transact-SQL を使用して、サーバー レベルとデータベース レベルのファイアウォール規則を作成する方法については、「[T-SQL を使用して Azure SQL Database のサーバー レベルとデータベース レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-tsql.md)」をご覧ください。 

他の方法でサーバー レベルのファイアウォール規則を作成する方法については、次の記事をご覧ください。 

* [Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings.md)
* [PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md)

データベース作成のチュートリアルについては、「[初めての Azure SQL Database](sql-database-get-started.md)」をご覧ください。
オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、 [SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。
データベースに移動する方法については、 [データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [データベースの保護](sql-database-security-overview.md)
* [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



