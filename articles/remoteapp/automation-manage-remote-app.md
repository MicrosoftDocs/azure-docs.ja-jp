---
title: "Azure Automation を使用した Azure RemoteApp の管理 | Microsoft Docs"
description: "Azure Automation サービスを使用して Azure RemoteApp を管理する方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 589f01ef-f9c1-4e7b-a040-1b46862d3544
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e21af65ecb6f0ce70048846a9786271dd4cc985


---
# <a name="managing-azure-remoteapp-using-azure-automation"></a>Azure Automation を使用した Azure RemoteApp の管理
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure RemoteApp の管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](../automation/automation-intro.md) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して手動タスク、頻繁に繰り返されるタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。

## <a name="how-can-azure-automation-help-manage-azure-remoteapp"></a>Azure Automation を Azure RemoteApp の管理に役立てる方法
[Azure PowerShell ツール](https://msdn.microsoft.com/library/azure/jj156055.aspx)で利用可能な PowerShell コマンドレットを使用することにより、Azure Automation で RemoteApp を管理できます。 Azure Automation には、このような RemoteApp PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべての RemoteApp 管理タスクを実行することができます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

## <a name="next-steps"></a>次のステップ
ここまで、Azure Automation の基本と Azure Automation を使用して Azure RemoteApp を管理する方法について説明しました。Azure Automation の詳細については、これらのリンクを参照してください。

*  [Azure Automation の使用](../automation/automation-first-runbook-graphical.md)




<!--HONumber=Nov16_HO3-->


