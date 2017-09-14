---
title: "SQL Database の Azure PowerShell スクリプトの例 | Microsoft Docs"
description: "Azure SQL Database サーバー、エラスティック プール、データベース、ファイアウォールの作成と管理で役立つ Azure PowerShell スクリプトの例。"
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 5a5f77b9adafe32e8559d0b3396febca4b191de3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/05/2017

---

# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure SQL Database 用の Azure PowerShell サンプル

次の表には、Azure SQL Database の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

| |  |
|---|---|
|**単一のデータベースとエラスティック プールを作成する**||
| [単一のデータベースを作成し、ファイアウォール規則を構成する](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、単一の Azure SQL Database を作成し、サーバー レベルのファイアウォール規則を構成します。 |
| [エラスティック プールを作成し、プールされているデータベースを移動する](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトでは、Azure SQL Database のエラスティック プールを作成し、プールされているデータベースを移動して、パフォーマンス レベルを変更します。|
|**geo レプリケーションを構成しフェールオーバーする**||
| [アクティブ geo レプリケーションを使用して、単一のデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、単一の Azure SQL Database のアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。 |
| [アクティブ geo レプリケーションを使用して、プールされているデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、SQL エラスティック プールにある Azure SQL Database のアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。 |
| [単一のデータベースのフェールオーバー グループを構成およびフェールオーバーする (プレビュー)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、Azure SQL Database サーバー インスタンスのフェールオーバー グループを構成し、このフェールオーバー グループにデータベースを追加し、セカンダリ サーバーにフェールオーバーします |
|**単一のデータベースとエラスティック プールをスケーリングする**||
| [単一のデータベースをスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、Azure SQL Database のパフォーマンス メトリックを監視し、そのデータベースを上位のパフォーマンス レベルにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成します。 |
| [エラスティック プールをスケーリングする](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、Azure SQL Database のエラスティック プールのパフォーマンス メトリックを監視し、そのデータベースを上位のパフォーマンス レベルにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成します。  |
| **監査と脅威の検出** |
| [監査と脅威検出を構成する](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Azure SQL Database の監査と脅威検出ポリシーを構成します。 |
| **データベースを復元、コピー、インポートする**||
| [データベースを復元する](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、geo 冗長バックアップから Azure SQL Database を復元し、削除された Azure SQL Database を最新のバックアップに復元します。 |
| [新しいサーバーにデータベースをコピーする](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、新しい Azure SQL サーバーに既存の Azure SQL Database のコピーを作成します。 |
| [データベースを bacpac ファイルからインポートする](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、bacpac ファイルから Azure SQL サーバーにデータベースをインポートします。 |
| **データベース間でデータを同期する**||
| [SQL Database 間でデータを同期する](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、複数の Azure SQL Database 間で同期するデータ同期を構成します。 |
| [SQL Database とオンプレミスの SQL Server のデータを同期する](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | この PowerShell スクリプトは、Azure SQL Database とオンプレミスの SQL Server データベースのデータ同期を構成します。 |
|||
|||

