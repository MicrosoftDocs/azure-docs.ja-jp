---
title: "SQL Database の Azure PowerShell サンプル | Microsoft Docs"
description: "Azure PowerShell サンプル - Azure SQL Database サーバー、エラスティック プール、データベース、ファイアウォールの作成と管理で役立つスクリプト。"
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8057747aea2725abf3b37481858c7902ff8fe4da
ms.lasthandoff: 03/30/2017

---

# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure SQL Database 用の Azure PowerShell サンプル

次の表には、Azure SQL Database の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

| |  |
|---|---|
|**単一のデータベースとエラスティック プールを作成する**||
| [単一のデータベースを作成し、ファイアウォール規則を構成する](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2ftoc.json) | 単一の Azure SQL Database を作成し、サーバー レベルのファイアウォール規則を構成します。 |
| [エラスティック プールを作成し、プールされているデータベースを移動する](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2ftoc.json) | エラスティック プールを作成し、プールされているデータベースを移動して、パフォーマンス レベルを変更します。|
|**geo レプリケーションを構成しフェールオーバーする**||
| [アクティブ geo レプリケーションを使用して、単一のデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2ftoc.json)| 単一の Azure SQL データベースのアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。 |
| [アクティブ geo レプリケーションを使用して、プールされているデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2ftoc.json)| エラスティック プール内の Azure SQL データベース用にアクティブ geo レプリケーションを構成し、その データベースをセカンダリ レプリカにフェールオーバーします。 |
|**単一のデータベースとエラスティック プールをスケーリングする**||
| [単一のデータベースをスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2ftoc.json) | Azure SQL データベースのパフォーマンス メトリックを監視し、そのデータベースを上位のパフォーマンス レベルにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成します。 |
| [エラスティック プールをスケーリングする](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2ftoc.json) | エラスティック プールのパフォーマンス メトリックを監視し、そのプールを上位のパフォーマンス レベルにスケーリングして、パフォーマンス メトリックの 1 つにアラート ルールを作成します。  |
| **監査と脅威の検出** |
| [監査と脅威検出を構成する](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2ftoc.json)| Azure SQL データベースの監査と脅威検出ポリシーを構成します。 |
| **データベースを復元、コピー、インポートする**||
| [データベースを復元する](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2ftoc.json)| geo 冗長バックアップから Azure SQL データベースを復元し、削除された Azure SQL データベースを最新のバックアップに復元します。 |
| [新しいサーバーにデータベースをコピーする](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2ftoc.json)| 新しい Azure SQL サーバーに既存の Azure SQL データベースのコピーを作成します。 |
| [データベースを bacpac ファイルからインポートする](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2ftoc.json)| bacpac ファイルから Azure SQL サーバーにデータベースをインポートします。 |
|||

