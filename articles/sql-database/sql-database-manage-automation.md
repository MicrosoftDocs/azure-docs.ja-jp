---
title: "Azure Automation を使用した Azure SQL データベースの管理 | Microsoft Docs"
description: "Azure Automation サービスを使用して、規模に応じて Azure SQL データベースを管理する方法について説明します。"
services: sql-database, automation
documentationcenter: 
author: jodoglevy
manager: jhubbard
editor: monicar
ms.assetid: 77c262a1-9b93-456d-b3c7-b2f23bdfcd61
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jhubbard
translationtype: Human Translation
ms.sourcegitcommit: 46f65a2898c39774bd63ff2f8f053224d7238fb0
ms.openlocfilehash: 0abfceb43ec5c333e9800ee6fca4e31fb317208d
ms.lasthandoff: 02/07/2017


---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure Automation を使用した Azure SQL データベースの管理
このガイドでは、Azure Automation サービスと、このサービスを使用して Azure SQL データベースの管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT/DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Azure Automation を Azure SQL データベースの管理に役立てる方法
[Azure PowerShell ツール](https://docs.microsoft.com/en-us/powershell/)で利用可能な [Azure SQL Database PowerShell コマンドレット](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/)を使用することにより、Azure Automation で Azure SQL データベースを管理できます。 Azure Automation には、このような Azure SQL Database PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべての SQL DB 管理タスクを実行することができます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

さらに、Azure Automation には、PowerShell を使用して SQL コマンドを発行することにより、SQL サーバーと直接通信する機能もあります。

[Azure Automation Runbook ギャラリー](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) には、Azure SQL データベース、その他の Azure サービス、サード パーティ製システムの管理の自動化を開始するためのさまざまな製品チームおよびコミュニティの Runbook が含まれています。 ギャラリーに含まれている Runbook の例を示します。

* [SQL Server データベースに対する SQL クエリの実行](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [スケジュールに従った Azure SQL データベースの垂直スケール (アップまたはダウン)](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [データベースがその最大サイズに近づいた場合の SQL テーブルの切り捨て](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Azure SQL データベースにおけるテーブルの断片化率が高い場合のインデックス作成](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>次のステップ
ここまで、Azure Automation の基本と Azure Automation を使用して Azure SQL データベースを管理する方法について説明しました。Azure Automation の詳細については、これらのリンクを参照してください。

* [Azure Automation Overview (Azure Automation の概要)](../automation/automation-intro.md)
* [初めての Runbook](../automation/automation-first-runbook-graphical.md)
* [Azure Automation の学習マップ](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Azure Automation: クラウド内の SQL エージェント](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 


