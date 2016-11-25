---
title: "SQL Database チュートリアル: セキュリティの概要"
description: "ユーザー アカウントを作成してアクセスし、データベースを管理する方法について説明します。"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3bd8ac5466ae67df0a1865509c8fb897d1374ec


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL Database チュートリアル: データベースにアクセスして管理するための SQL Database ユーザー アカウントを作成する
> [!div class="op_single_selector"]
> * [チュートリアルの開始](sql-database-get-started-security.md)
> * [アクセス権の付与](sql-database-manage-logins.md)
> 
> 

このチュートリアルでは、SQL Server Management Studio (SSMS) を使用して以下の操作を行う方法を学習します。

* サーバー レベルのプリンシパル ログインを使用して SQL Database にログインする。
* SQL Database ユーザー アカウントを作成する。
* SQL Database ユーザーに [db_owner アクセス許可](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0)を付与する。
* サーバー レベルのプリンシパルではないユーザー アカウントを使用して SQL データベースに接続する。

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>次のステップ
この SQL Database チュートリアルを完了して、ユーザー アカウントを作成し、ユーザー アカウントに dbo アクセス許可を付与した後は、 [SQL Database のセキュリティ](sql-database-manage-logins.md)についてさらに詳しく学習できます。




<!--HONumber=Nov16_HO2-->


