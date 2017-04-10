---
title: "Azure Portal: Azure SQL Database のサーバーレベルのファイアウォール規則 | Microsoft Docs"
description: "Azure Portal を使用して、Azure SQL サーバーにアクセスする IP アドレス用にサーバーレベルのファイアウォール規則を構成する方法について説明します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 1939b69ee806d9091a21f3b21a276175d5a19a62
ms.lasthandoff: 02/17/2017


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Azure Portal を使用した Azure SQL Database のサーバーレベルのファイアウォール規則の作成と管理

サーバーレベルのファイアウォール規則を使用すると、管理者は、指定された IP アドレスまたは IP アドレス範囲から SQL Database サーバーにアクセスできます。 アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの構成に時間を費やしたくない場合は、ユーザーにサーバーレベルのファイアウォール規則を使用することもできます。 ただし、セキュリティを強化しデータベースの移植性を高めるため、可能な限りデータベースレベルのファイアウォール規則を使用することをお勧めします。 SQL Database ファイアウォールの概要については、[SQL Database ファイアウォール規則の概要](sql-database-firewall-configure.md)に関する記事を参照してください。

> [!Note]
> ビジネス継続性のコンテキストにおける移植可能なデータベースについては、[障害復旧の認証要件](sql-database-geo-replication-security-config.md)に関する記事を参照してください。
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する
サーバーレベルのファイアウォール規則を管理する手順を繰り返します。

* 現在のコンピューターを追加するには、[クライアント IP の追加] をクリックします。
* さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。
* 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。
* 既存の規則を削除するには、行の最後に X が表示されるまで、規則上にポインターを置きます。 [X] をクリックして、規則を削除します。

**[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ

- サーバーレベルのファイアウォールを使用したサーバーのプロビジョニングと接続のチュートリアルについては、「[チュートリアル: Azure Portal と SQL Server Management Studio を使用した Azure SQL データベースのプロビジョニングとアクセス](sql-database-get-started.md)」を参照してください。
- SQL Server 認証とデータベースレベルのファイアウォールのチュートリアルについては、[SQL の認証と承認](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。
- オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、 [SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。
- データベースに接続可能な追加ユーザーの作成方法については、「[SQL Database 認証および承認： アクセス権を付与する](https://msdn.microsoft.com/library/azure/ee336235.aspx)」を参照してください。

## <a name="additional-resources"></a>その他のリソース
* [データベースの保護](sql-database-security-overview.md)   
* [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)   




