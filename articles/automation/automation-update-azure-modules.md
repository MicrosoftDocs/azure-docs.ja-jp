---
title: Azure Automation で Azure モジュールを更新する
description: この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fc605ab45241280d9331ad7d515ba007a015daa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583655"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する方法

各 Automation アカウントでは、最も一般的な Azure PowerShell モジュールが既定で提供されます。 Azure チームは Azure モジュールを定期的に更新しています。 Automation アカウントには、新しいバージョンがポータルから使用可能になったらそのアカウントで各モジュールを更新するための方法が用意されています。

モジュールは製品グループによって定期的に更新されるため、含まれているコマンドレットについて変更が発生することがあります。 そのため、変更の種類 (パラメーター名の変更、コマンドレット全体の非推奨化など) によっては、Runbook に悪影響が及ぶ可能性があります。 Runbook やそれにより自動化されるプロセスに影響を与えないようにするため、先に進む前にテストと検証を行ってください。 その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、Runbook の開発中の多数のさまざまなシナリオについてテストを行うことができます。 このテストで、PowerShell モジュールの更新など、反復的な変更を行ってください。 結果を検証し、必要な変更を適用した後、変更を運用環境に移行してください。

> [!NOTE]
> 新しい Automation アカウントには、最新のモジュールが含まれていない可能性があります。

## <a name="updating-azure-modules"></a>Azure モジュールの更新

1. Automation アカウントの [モジュール] ページに、**Update Azure Modules (Azure モジュールの更新)** オプションが追加されました。 このオプションは常に有効です。<br><br> ![[モジュール] ページの [Update Azure Modules] (Azure モジュールの更新) オプション](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Azure モジュールを更新する前に、既存のスクリプトが予期したとおり確実に動作するように、テスト用の Automation アカウントでモジュールを更新することをお勧めします。
  >
  > **[Update Azure Modules] (Azure モジュールの更新)** ボタンは、パブリック クラウドでのみ使用できます。 [独立リージョン](https://azure.microsoft.com/global-infrastructure/)では使用できません。 詳細については、[モジュールを更新する別の方法](#alternative-ways-to-update-your-modules)についてのセクションを参照してください。


2. **[Azure モジュールの更新]** をクリックすると、続行するかどうかを訪ねる確認通知が表示されます。<br><br> ![Azure モジュールの更新通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. **[はい]** をクリックすると、モジュールの更新プロセスが開始されます。 更新プロセスの所要時間は約 15 分から 20 分であり、以下のモジュールが更新されます。

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    モジュールが既に最新の状態である場合、このプロセスは数秒で完了します。 更新プロセスが完了すると、通知が表示されます。<br><br> ![Azure モジュールの更新の更新状態](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    .NET Core の AzureRm モジュール (AzureRm.*.Core) は、Azure Automation ではサポートされておらず、インポートすることはできません。

> [!NOTE]
> Azure Automation は、スケジュール済みの新しいジョブの実行時に Automation アカウントの最新のモジュールを使用します。  

Runbook でこれらの Azure PowerShell モジュールのコマンドレットを使用する場合は、最新のモジュールになっていることを確認するために、この更新プロセスを 1 か月に 1 回程度実行する必要があります。 Azure Automation では、モジュールの更新時に `AzureRunAsConnection` 接続を使用して認証が行われます。 サービス プリンシパルが有効期限切れになっている場合や、サブスクリプション レベルで存在しなくなっている場合、モジュールの更新は失敗します。

## <a name="alternative-ways-to-update-your-modules"></a>モジュールを更新する別の方法

前述のように、**[Update Azure Modules] (Azure モジュールの更新)** ボタンはソブリン クラウドでは使用できず、グローバル Azure クラウドでのみ使用できます。 これは、PowerShell ギャラリーにある Azure PowerShell モジュールの最新バージョンは、これらのクラウドに現在デプロイされている Resource Manager リソースでは機能しないという事実が原因です。

[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook をインポートして実行すると、Automation アカウント内の Azure モジュールの更新を試行できます。 ギャラリーからインポートしようとしているバージョンが、ターゲットの Azure 環境に現在デプロイされている Azure サービスに対して互換性を持たない場合、このプロセスは失敗する可能性があります。 その場合は、Runbook のパラメーターで、互換性のあるバージョンのモジュールが指定されていることを確認する必要があります。

`AzureRmEnvironment` パラメーターを使用して、適切な環境を Runbook に渡します。  指定できる値は、**AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud**、および **AzureUSGovernment** です。 これらの値は `Get-AzureRmEnvironment | select Name` を使用して取得できます。 このパラメーターに値を渡さない場合、Runbook は既定で、Azure パブリック クラウドの **AzureCloud** を使用します

PowerShell ギャラリーで提供されている最新のものではなく、Azure PowerShell モジュールの特定のバージョンを使用する場合は、これらのバージョンを、**Update-AzureModule** Runbook のオプションの `ModuleVersionOverrides` パラメーターに渡します。 例については、[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook を参照してください。 `ModuleVersionOverrides` パラメーターで指定されていない Azure PowerShell モジュールは、PowerShell ギャラリーにあるモジュールの最新バージョンで更新されます。 `ModuleVersionOverrides` パラメーターに何も渡さなかった場合は、すべてのモジュールが、PowerShell ギャラリーにある最新のモジュール バージョンで更新されます。 この動作は、 **[Azure モジュールの更新]** ボタンをクリックするのと同じことです。

## <a name="next-steps"></a>次の手順

* 統合モジュールの詳細、およびカスタム モジュールを作成して Automation をさらに別のシステム、サービス、またはソリューションと統合する方法については、[統合モジュール](automation-integration-modules.md)に関するページを参照してください。

