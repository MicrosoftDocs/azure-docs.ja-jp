---
title: "Azure Automation を使用した Azure Web App の管理 | Microsoft Docs"
description: "Azure Automation サービスを使用して、Azure Web App を管理する方法について説明します。"
services: app-service\web, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: c960a44f-f941-401d-afba-a4bc0f0394eb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 4edd2696c9a5709ded6e2a3e352090775335f0d2
ms.openlocfilehash: 4fcfa2e7ec2e8257407026ed4cca0e15fd0b5bb6


---
# <a name="managing-azure-web-app-using-azure-automation"></a>Azure Automation を使用した Azure Web App の管理
このガイドでは、Azure Automation サービスと、このサービスを使用して Azure Web App の管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](../automation/automation-intro.md) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して手動タスク、繰り返しタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT と DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。

## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Azure Automation を Azure Web App の管理に役立てる方法
[Azure PowerShell モジュール](/powershell/azureps-cmdlets-docs)で利用可能な PowerShell コマンドレットを使用することにより、Azure Automation で Web アプリを管理できます。 [こうした Azure Web App PowerShell コマンドレットを Azure Automation にインストール](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/)することで、サービス内ですべての Web App 管理タスクを実行できます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスとサード パーティ システム全体の複雑なタスクを自動化することもできます。

Automation で App Services を管理する例については、

* [Web Apps を管理するためのスクリプト](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>次のステップ
ここまで、Azure Automation の基本と Azure Automation を使用して Azure Web App を管理する方法について説明しました。Azure Automation の詳細については、これらのリンクをご覧ください。

*  [Azure Automation の使用](../automation/automation-first-runbook-graphical.md)




<!--HONumber=Dec16_HO1-->


