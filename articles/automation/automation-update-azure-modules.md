---
title: Azure Automation の Azure PowerShell モジュールを更新する
description: この記事では、Azure Automation に既定で用意されている一般的な Azure PowerShell モジュールを更新する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: f05c3247ecdd77fe4cf02db70c7ba4b2c534f959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055350"
---
# <a name="update-azure-powershell-modules"></a>Azure PowerShell モジュールの更新

各 Automation アカウントでは、最も一般的な PowerShell モジュールが既定で提供されます。 「[既定のモジュール](shared-resources/modules.md#default-modules)」を参照してください。 Azure のモジュールは Azure チームによって定期的に更新されているため、同梱されるコマンドレットに変更が生じることがあります。 こうした変更 (パラメーター名の変更、コマンドレット全体の非推奨化など) により、Runbook に悪影響が及ぶ可能性があります。 

> [!NOTE]
> グローバル モジュールを削除することはできません。これらは、Automation で提供されるそのまま利用できるモジュールです。

## <a name="set-up-an-automation-account"></a>Automation アカウントをセットアップする

Runbook やそれにより自動化されるプロセスに影響を与えないようにするため、更新前にテストと検証を必ず行ってください。 その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、Runbook の開発中の多数のさまざまなシナリオについてテストを行うことができます。 このテストで、PowerShell モジュールの更新など、反復的な変更を行ってください。

お使いの Automation アカウントに [Azure 実行アカウント](automation-security-overview.md#run-as-accounts)が作成されていることを確認します。

スクリプトをローカルで開発する場合、テスト時に確実に同じ結果が得られるようにするために、Automation アカウントに置いているものと同じバージョンのモジュールをローカルに用意することをお勧めします。 結果を検証し、必要な変更を適用した後、変更を運用環境に移行してください。

> [!NOTE]
> 新しい Automation アカウントには、最新のモジュールが含まれていない可能性があります。

## <a name="obtain-a-runbook-to-use-for-updates"></a>更新に使用する Runbook を入手する

Automation アカウントの Azure モジュールを更新するには、オープン ソースとして提供されている [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) を使用する必要があります。 この Runbook を使用して Azure モジュールを更新する場合は、まず GitHub リポジトリから Runbook をダウンロードしてください。 その後、それを Automation アカウントにインポートするか、スクリプトとして実行することができます。 Automation アカウントに Runbook をインポートする方法については、[Runbook のインポート](manage-runbooks.md#import-a-runbook)に関する記事をご覧ください。

**Update-AutomationAzureModulesForAccount** Runbook では、Azure、AzureRM、Az モジュールの更新が既定でサポートされます。 この Runbook による Az.Automation モジュールの更新の詳細については、[Azure モジュールの更新 Runbook の README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) を確認してください。 Automation アカウントで Az モジュールを使用するときに考慮しなければならないその他の重要な要素があります。 詳細については、「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>更新の Runbook コードを通常の PowerShell スクリプトとして使用する

Runbook のコードは、Runbook としてではなく、通常の PowerShell スクリプトとして使用することができます。 そのためには、まず [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure にサインインし、スクリプトに `-Login $false` を渡します。

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>更新の Runbook をソブリン クラウドで使用する

ソブリン クラウドでこの Runbook を使用するには、`AzEnvironment` パラメーターを使用して、適切な環境を Runbook に渡します。 指定できる値は、AzureCloud (Azure パブリック クラウド)、AzureChinaCloud、AzureGermanCloud、および AzureUSGovernment です。 これらの値は `Get-AzEnvironment | select Name` を使用して取得できます。 このコマンドレットに値を渡さないと、Runbook は既定で AzureCloud を使用します。

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>更新の Runbook を使用して特定のモジュールのバージョンを更新する

PowerShell ギャラリーで提供されている最新のモジュールではなく、Azure PowerShell モジュールの特定のバージョンを使用する場合は、それらのバージョンを、**Update-AutomationAzureModulesForAccount** Runbook の省略可能な `ModuleVersionOverrides` パラメーターに渡します。 例については、[Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) Runbook をご覧ください。 `ModuleVersionOverrides` パラメーターで指定されていない Azure PowerShell モジュールは、PowerShell ギャラリーにあるモジュールの最新バージョンで更新されます。 `ModuleVersionOverrides` パラメーターに何も渡さなかった場合は、すべてのモジュールが、PowerShell ギャラリーにある最新のモジュール バージョンで更新されます。 この動作は、Azure portal の **[Azure モジュールの更新]** ボタンに相当します。

## <a name="next-steps"></a>次のステップ

* モジュールの使用について詳しくは、「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。
* 更新の Runbook については、[Azure モジュールの更新 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) に関するページを参照してください。
