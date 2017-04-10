---
title: "Azure Automation を使用した VM の管理 | Microsoft Docs"
description: "Azure Automation サービスを使用して大規模に Azure 仮想マシンを管理する方法について説明します。"
services: virtual-machines-windows, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: ce49f83a-f409-42ee-af74-a8ea2caa9ae8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d9c161b58419bb85c7ec473d853db4cbc40dd1


---
# <a name="managing-azure-virtual-machines-using-azure-automation"></a>Azure Automation を使用した Azure Virtual Machines の管理
このガイドでは、Azure Automation サービスと、このサービスを使用して Azure 仮想マシンの管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。

## <a name="how-can-azure-automation-help-manage-azure-virtual-machines"></a>Azure Automation を Azure 仮想マシンの管理に役立てる方法
Virtual Machines は、 [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)を使用して  Azure Automation での管理できます。 Azure Automation は Azure PowerShell コマンドレットを含むため、サービス内ですべての仮想マシン管理タスクを実行することができます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

## <a name="next-steps"></a>次のステップ
ここまでは、Azure Automation の基本と Azure Automation を使用して仮想マシンを管理する方法について説明しました。詳細については、次のリンクを参照してください。

* [Azure Automation Overview (Azure Automation の概要)](../automation/automation-intro.md)
* [初めての Runbook](../automation/automation-first-runbook-graphical.md)
* [Azure Automation の学習マップ](https://azure.microsoft.com/documentation/learning-paths/automation/)




<!--HONumber=Nov16_HO3-->


