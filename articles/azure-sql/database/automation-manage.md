---
title: Azure Automation を使用してデータベースを管理する
description: Azure Automation サービスを使用して、規模に応じて Azure SQL Database を管理する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: faf5b1108e28b352a0b8622feed8bdd99264ff16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91327580"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Azure Automation を使用して Azure SQL Database のデータベースを管理する

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure SQL Database 内のデータベースの管理を簡略化する方法について紹介します。

## <a name="about-azure-automation"></a>Azure Automation について

[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。 作業を開始するための詳細は、「[Azure Automation の導入](../../automation/automation-intro.md)」を参照してください。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT/DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Azure Automation がデータベースの管理にどのように役立つか

Azure Automation では、[Azure PowerShell ツール](/powershell/azure/)で利用可能な [PowerShell コマンドレット](/powershell/module/servicemanagement/azure.service/#sql)を使用することにより、 Azure SQL Database 内のデータベースを管理できます。 Azure Automation には、このような Azure SQL Database PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべての SQL Database 管理タスクを実行することができます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスとサード パーティ システム全体の複雑なタスクを自動化することもできます。

さらに、Azure Automation には、PowerShell を使用して SQL コマンドを発行することにより、SQL サーバーと直接通信する機能もあります。

[Azure Automation](../../automation/automation-runbook-gallery.md) の Runbook ギャラリーとモジュール ギャラリーでは、Microsoft およびコミュニティから、Azure Automation にインポートできる各種の Runbook が提供されています。 使用するには、ギャラリーから Runbook をダウンロードするか、Azure portal でギャラリーから、または Automation アカウントから、Runbook を直接インポートできます。

## <a name="next-steps"></a>次のステップ

ここまで、Azure Automation の基本と、Azure Automation を使用して Azure SQL Database を管理する方法について説明しました。Azure Automation の詳細については、次のリンクを参照してください。

- [Azure Automation Overview (Azure Automation の概要)](../../automation/automation-intro.md)
- [初めての Runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
