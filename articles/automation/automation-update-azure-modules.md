---
title: Azure Automation で Azure モジュールを更新する
description: この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03174e6336589f8aa49a7fc7197da1301ff54400
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009781"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する方法

Automation アカウントの Azure モジュールを更新するには、現在はオープン ソースになっている [Azure モジュールの更新 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) を使用することをお勧めします。 さらに、引き続きポータルの **[Azure モジュールの更新]** ボタンを使用して Azure モジュールを更新することもできます。 オープン ソースの Runbook の使用方法について詳しくは、「[オープン ソースの Runbook で Azure モジュールを更新する](#open-source)」をご覧ください。

各 Automation アカウントでは、最も一般的な Azure PowerShell モジュールが既定で提供されます。 Azure チームは Azure モジュールを定期的に更新しています。 Automation アカウントには、新しいバージョンがポータルから使用可能になったらそのアカウントで各モジュールを更新するための方法が用意されています。

モジュールは製品グループによって定期的に更新されるため、含まれているコマンドレットについて変更が発生することがあります。 そのため、変更の種類 (パラメーター名の変更、コマンドレット全体の非推奨化など) によっては、Runbook に悪影響が及ぶ可能性があります。

Runbook やそれにより自動化されるプロセスに影響を与えないようにするため、先に進む前にテストと検証を行ってください。 その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、Runbook の開発中の多数のさまざまなシナリオについてテストを行うことができます。 このテストで、PowerShell モジュールの更新など、反復的な変更を行ってください。 

スクリプトをローカルで開発する場合、確実に同じ結果が得られることをテストするとき、Automation アカウントに置いているものと同じバージョンのモジュールをローカルに用意することをお勧めします。 結果を検証し、必要な変更を適用した後、変更を運用環境に移行してください。

> [!NOTE]
> 新しい Automation アカウントには、最新のモジュールが含まれていない可能性があります。

## <a name="open-source"></a>オープン ソースの Runbook で Azure モジュールを更新する

**Update-AutomationAzureModulesForAccount** Runbook を使用した Azure モジュールの更新を始めるには、Runbook を GitHub の [Update Azure modules runbook repository (Azure モジュールの更新 Runbook リポジトリ)](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) からダウンロードします。 その後、それを Automation アカウントにインポートするか、スクリプトとして実行することができます。 これを行う方法の説明は、[Update Azure modules runbook repository (Azure モジュールの更新 Runbook リポジトリ)](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) にあります。

### <a name="considerations"></a>考慮事項

このプロセスを使用して Azure モジュールを更新するときの考慮事項を次に示します。

* 元の名前 `Update-AutomationAzureModulesForAccount` でこの Runbook をインポートした場合、この名前で内部にある Runbook が上書きされます。 その結果、インポートされた Runbook は、**[Azure モジュールの更新]** ボタンをクリックしたとき、または Azure Resource Manager API を使用してこの Automation アカウントに対してこの Runbook を直接呼び出したときに、実行されます。

* この Runbook は現在、**Azure** と **AzureRm** モジュールの更新のみをサポートしています。 [Azure PowerShell Az モジュール](/powershell/azure/new-azureps-module-az)は Automation アカウントでサポートされますが、この Runbook では更新できません。

* Az モジュールが含まれている Automation アカウントでは、この Runbook を開始しないでください。

* この Runbook を開始する前に、お使いの Automation アカウントに [Azure 実行アカウント資格情報](manage-runas-account.md)が作成されていることを確認すします。

* このコードは、Runbook ではなく通常の PowerShell スクリプトとして使用できます。そのためには、最初に [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) コマンドを使用して Azure にログインし、スクリプトに `-Login $false` を渡します。

* ソブリン クラウドでこの Runbook を使用するには、`AzureRmEnvironment` パラメーターを使用して、適切な環境を Runbook に渡します。  指定できる値は、**AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud**、および **AzureUSGovernment** です。 これらの値は `Get-AzureRmEnvironment | select Name` を使用して取得できます。 このパラメーターに値を渡さない場合、Runbook は既定で、Azure パブリック クラウドの **AzureCloud** を使用します

* PowerShell ギャラリーで提供されている最新のものではなく、Azure PowerShell モジュールの特定のバージョンを使用する場合は、それらのバージョンを、**Update-AutomationAzureModulesForAccount** Runbook の省略可能な `ModuleVersionOverrides` パラメーターに渡します。 例については、[Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook をご覧ください。 `ModuleVersionOverrides` パラメーターで指定されていない Azure PowerShell モジュールは、PowerShell ギャラリーにあるモジュールの最新バージョンで更新されます。 `ModuleVersionOverrides` パラメーターに何も渡さなかった場合は、すべてのモジュールが、PowerShell ギャラリーにある最新のモジュール バージョンで更新されます。 この動作は、 **[Azure モジュールの更新]** ボタンをクリックするのと同じことです。

## <a name="update-azure-modules-in-the-azure-portal"></a>Azure portal で Azure モジュールを更新する

1. Automation アカウントの [モジュール] ページに、**Update Azure Modules (Azure モジュールの更新)** オプションが追加されました。 このオプションは常に有効です。<br><br> ![[モジュール] ページの [Update Azure Modules] (Azure モジュールの更新) オプション](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

   > [!NOTE]
   > Azure モジュールを更新する前に、既存のスクリプトが予期したとおり確実に動作するように、テスト用の Automation アカウントでモジュールを更新することをお勧めします。
   >
   > **[Update Azure Modules] (Azure モジュールの更新)** ボタンは、パブリック クラウドでのみ使用できます。 [独立リージョン](https://azure.microsoft.com/global-infrastructure/)では使用できません。 Azure モジュールを更新するには、**Update-AutomationAzureModulesForAccount** Runbook を使用してください。 それは、[Update Azure modules runbook repository (Azure モジュールの更新 Runbook リポジトリ)](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) からダウンロードできます。 オープン ソースの Runbook の使用方法について詳しくは、「[オープン ソースの Runbook で Azure モジュールを更新する](#open-source)」をご覧ください。

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

## <a name="known-issues"></a>既知の問題

0 から始まる数値名を持つリソース グループにある、Automation Account の AzureRM モジュールの更新については、既知の問題があります。 Automation Account で Azure モジュールを更新するには、それが英数字名を持つリソース グループになければなりません。 0 から始まる数値名を持つリソース グループは、現時点で AzureRM モジュールを更新できません。

## <a name="next-steps"></a>次の手順

詳しくは、オープン ソースの [Azure モジュールの更新 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) をご覧ください。
