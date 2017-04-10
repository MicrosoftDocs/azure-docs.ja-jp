---
title: "REST API: Azure SQL Database 監査を管理する | Microsoft Docs"
description: "REST API を使用して、データベース イベントを追跡して Azure Storage アカウントの監査ログに書き込むように Azure SQL Database 監査を構成します。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9516dd1ae6a67fb9402591fe1906ee7554c1495c
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-the-rest-api"></a>REST API を使用して SQL Database 監査を構成して管理する

このトピックでは、REST API を使用して監査を構成および管理する方法について説明します。 Azure Portal を使用して監査を構成および管理する方法については、[Azure Portal での監査の構成](sql-database-auditing-portal.md)に関するページを参照してください。 PowerShell を使用して監査を構成および管理する方法については、[PowerShell を使用した監査の構成](sql-database-auditing-powershell.md)に関するページを参照してください。

監査の概要については、[SQL Database 監査](sql-database-auditing.md)に関するページを参照してください。

## <a name="rest-api---blob-auditing"></a>REST API - BLOB 監査

   * [データベース BLOB 監査ポリシーの作成または更新](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [サーバー BLOB 監査ポリシーの作成または更新](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [データベース BLOB 監査ポリシーの取得](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [サーバー BLOB 監査ポリシーの取得](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [サーバー BLOB 監査操作結果の取得](https://msdn.microsoft.com/library/azure/mt771862.aspx)


## <a name="rest-api---table-auditing"></a>REST API - テーブル監査

   * [データベース監査ポリシーの作成または更新](https://msdn.microsoft.com/library/azure/mt604471.aspx)
   * [サーバー監査ポリシーの作成または更新](https://msdn.microsoft.com/library/azure/mt604383.aspx)
   * [データベース監査ポリシーの取得](https://msdn.microsoft.com/library/azure/mt604381.aspx)
   * [サーバー監査ポリシーの取得](https://msdn.microsoft.com/library/azure/mt604382.aspx)

## <a name="next-steps"></a>次のステップ

* Azure Portal を使用して監査を構成および管理する方法については、[Azure Portal でのデータベース監査の構成](sql-database-auditing-portal.md)に関するページを参照してください。 
* PowerShell を使用して監査を構成および管理する方法については、[PowerShell を使用したデータベース監査の構成](sql-database-auditing-powershell.md)に関するページを参照してください。
* 監査の概要については、[データベース監査](sql-database-auditing.md)に関するページを参照してください。
