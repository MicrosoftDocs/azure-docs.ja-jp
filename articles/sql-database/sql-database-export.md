---
title: "Azure SQL Database を BACPAC ファイルにエクスポートする | Microsoft Docs"
description: "Azure Portal を使って BACPAC ファイルに Azure SQL Database をエクスポートする"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: ff24360696c54091ab93e7e1c8f28d95c226bc0a
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Azure SQL Database または SQL Server データベースを BACPAC ファイルにエクスポートする

この記事では、Azure SQL Database または SQL Server データベースの BACPAC ファイルへのエクスポートについて説明します。 

> [!IMPORTANT]
> Azure SQL Database の自動エクスポート機能は現在プレビュー段階であり、2017 年 3 月 1 日に廃止されます。 2016 年 12 月 1 日から、SQL データベースの自動エクスポートを構成することはできなくなります。 既存のエクスポート ジョブは、引き続き 2017 年 3 月 1 日まで実行されます。 2016 年 12 月 1 日以降は、[長期のバックアップ リテンション期間](sql-database-long-term-retention.md)または [Azure Automation](../automation/automation-intro.md) を使用してください。PowerShell を使用して、選択したスケジュールに従って定期的に SQL データベースをアーカイブできます。 サンプル スクリプトは [GitHub からダウンロード](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)できます。 
>

## <a name="overview"></a>概要

アーカイブのため、または別のプラットフォームに移行するためにデータベースをエクスポートする必要がある際は、データベース スキーマとデータを BACPAC ファイルにエクスポートできます。 BACPAC ファイルは、単に BACPAC の拡張子を持つ ZIP ファイルです。 BACPAC ファイルは、後で Azure Blob Storage またはオンプレミスの場所にあるローカル ストレージに格納することや、後で Azure SQL Database またはオンプレミス SQL Server インストールに戻ってインポートすることができます。 

* [Azure Portal](sql-database-export-portal.md)、[PowerShell](sql-database-export-powershell.md)、[SQLPackage](sql-database-export-sqlpackage.md)、または [SQL Server Management Studio](sql-database-export-ssms.md) を使用して Azure SQL Database をエクスポートできます。
* [PowerShell](sql-database-export-powershell.md)、[SQLPackage](sql-database-export-sqlpackage.md)、または [SQL Server Management Studio](sql-database-export-ssms.md) を使用して、SQL Server データベースをエクスポートできます。

> [!IMPORTANT]
> Azure SQL Database への移行準備として SQL Server からエクスポートする場合は、[SQL Server データベースの Azure SQL Database への移行](sql-database-cloud-migrate.md)に関するページを参照してください。
> 

## <a name="considerations"></a>考慮事項

* エクスポートにトランザクション一貫性を持たせるために、書き込みアクティビティがエクスポート中に行われないようにするか、Azure SQL Database の[トランザクション一貫性のあるコピー](sql-database-copy.md)からエクスポートする必要があります。
* Blob Storage にエクスポートする場合、BACPAC ファイルの最大サイズは 200 GB です。 大きな BACPAC ファイルをアーカイブするには、ローカル ストレージにエクスポートします。
* この記事で説明されている方法を用いた Azure Premium Storage への BACPAC ファイルのエクスポートはサポートされていません。
* Azure SQL Database からのエクスポート操作が 20 時間を超える場合は、操作が取り消されることがあります。 エクスポート中にパフォーマンスを向上させるには、次の操作を実行します。
  * サービス レベルを一時的に上げる。
  * エクスポート中のすべての読み取りアクティビティと書き込みアクティビティを中止する。
  * すべての大きなテーブルに null 以外の値を持つ [クラスター化インデックス](https://msdn.microsoft.com/library/ms190457.aspx) を使用する。 クラスター化インデックスがないと、エクスポートが 6 ～ 12 時間よりも時間が長くかかる場合には失敗することがあります。 これは、エクスポート サービスがテーブル スキャンを実行してテーブル全体をエクスポートしようとする必要があることが原因です。 テーブルがエクスポート向けに最適化されているかを判断するための適切な方法として、**DBCC SHOW_STATISTICS** を実行し、*RANGE_HI_KEY* が null 以外の値であり、分布が適切であることを確認する方法があります。 詳細については、「[DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)」を参照してください。

> [!NOTE]
> BACPAC はバックアップおよび復元操作に使用するためのものでありません。 Azure SQL Database では、すべてのユーザー データベースのバックアップが自動的に作成されます。 詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページと、[ バックアップ](sql-database-automated-backups.md)に関するページを参照してください。  
> 


## <a name="next-steps"></a>次のステップ

* SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。
* Azure でのデータベースのコピーに関する概要については、[Azure SQL Database のコピー](sql-database-copy.md)に関するページを参照してください。
* [Azure Portal](sql-database-copy-portal.md)、[PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md)、または [Transact-SQL](sql-database-copy-transact-sql.md) を使用して、Azure 内で Azure SQL Database をコピーできます。 

