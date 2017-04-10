---
title: "Node.js を使用して SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続に使用できる Node.js コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: 6c3f6bc00d147bc498a859560ffe4719ecf888bf
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-nodejs"></a>Node.js を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、Node.js を使用して Azure SQL Database に接続し、クエリを実行する方法について説明します。 このサンプルは、Windows、Ubuntu Linux、または Mac のプラットフォームから実行できます。

## <a name="step-1-configure-development-environment"></a>手順 1: 開発環境を設定する
[Tedious Node.js Driver for SQL Server 使用の前提条件](https://docs.microsoft.com/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/)

## <a name="step-2-create-a-sql-database"></a>手順 2: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  ガイドに従って、 **AdventureWorks データベースのテンプレート**を作成することが重要です。 以下に示す例は、 **AdventureWorks スキーマ**とのみ動作します。

## <a name="step-3-get-connection-details"></a>手順 3: 接続の詳細を取得する
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>手順 4: サンプル コードを実行する
[Node.js を使用した SQL 接続の概念実証](https://docs.microsoft.com/sql/connect/node-js/step-3-proof-of-concept-connecting-to-sql-using-node-js/)

## <a name="next-steps"></a>次のステップ
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [Microsoft Node.js Driver for SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)


