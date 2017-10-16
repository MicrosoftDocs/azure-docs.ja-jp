---
title: "Azure Automation の Azure モジュールの更新 | Microsoft Docs"
description: "この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.openlocfilehash: 6bd259f3da1005228b8137415c30660221507909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する方法

各 Automation アカウントでは、最も一般的な Azure PowerShell モジュールが既定で提供されます。  Azure モジュールは Azure チームにより定期的に更新されるため、Automation アカウントでは、新しいバージョンがポータルから利用可能になったときにアカウント内の各モジュールを更新できるようになりました。  

モジュールは製品グループによって定期的に更新されるため、含まれているコマンドレットと共に変更が発生する可能性があります。変更の種類 (パラメーター名を変更する、コマンドレット全体を非推奨にするなど) によっては、Runbook に悪影響が及ぶことがあります。 Runbook およびそれらが自動化するプロセスに影響が及ばないようにするには、処理を続行する前にテストと検証を行うことを強くお勧めします。  その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、PowerShell モジュールの更新などの反復的な変更に加えて、Runbook の開発中の多数のさまざまなシナリオや変更についてテストを行うことができます。  結果を検証して必要な変更をすべて適用したら、修正が必要であった Runbook の移行の調整を進め、後述のとおり、運用環境で更新を実行してください。     

## <a name="updating-azure-modules"></a>Azure モジュールの更新

1. Automation アカウントの [モジュール] ブレードに、**Update Azure Modules (Azure モジュールの更新)** オプションが追加されました。  このオプションは常に有効です。<br><br> ![[モジュール] ブレードの [Update Azure Modules] (Azure モジュールの更新) オプション](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. **Update Azure Modules (Azure モジュールの更新)** をクリックすると、操作を続けるかどうかを尋ねる確認通知が表示されます。<br><br> ![Azure モジュールの更新通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. **[はい]** をクリックすると、モジュールの更新プロセスが開始されます。  更新プロセスの所要時間は約 15 分から 20 分であり、以下のモジュールが更新されます。

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    モジュールが既に最新の状態である場合、プロセスは数秒で完了します。  更新プロセスが完了すると通知が表示されます。<br><br> ![Azure モジュールの更新の更新状態](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation は、スケジュール済みの新しいジョブの実行時に Automation アカウントの最新のモジュールを使用します。    

runbook で Azure PowerShell モジュールのコマンドレットを使用して Azure リソースを管理する場合、モジュールを最新に保つために 1 か月に 1 回程度この更新プロセスを実行する必要があります。

## <a name="next-steps"></a>次のステップ

* 統合モジュールの詳細、およびカスタム モジュールを作成して Automation をさらに別のシステム、サービス、またはソリューションと統合する方法については、[統合モジュール](automation-integration-modules.md)に関するページを参照してください。

* ソース管理の統合を検討してください。そのためには、[GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) または [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) を使用して、Automation Runbook と構成ポートフォリオのリリースを一元的に管理および制御します。  