---
title: Azure Automation での Az モジュールの使用
description: この記事では、Azure Automation での Az モジュールの使用について説明します
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 23869647b5ad04d24f0b700a1433482d4ae15fd3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850535"
---
# <a name="az-module-support-in-azure-automation"></a>Azure Automation での Az モジュールのサポート

Azure Automation では、Runbook で [Azure Powershell の Az モジュール](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)を使用できます。 Az モジュールが新規または既存の Automation アカウントに自動的にインポートされることはありません。 この記事では、Azure Automation で Az モジュールを使用する方法について説明します。

## <a name="considerations"></a>考慮事項

Azure Automation で Az モジュールを使用するときは、考慮することが多くあります。 Runbook とモジュールは、Automation アカウントの上位レベルのソリューションで使用できます。 Runbook を編集したり、モジュールをアップグレードしたりすると、Runbook で問題が発生する可能性があります。 新しい `Az` モジュールをインポートする前に、別の Automation アカウントですべての Runbook とソリューションを十分にテストする必要があります。 モジュールを変更すると、[起動/停止](automation-solution-vm-management.md)ソリューションに悪影響を及ぼす可能性があります。 ソリューションが含まれる Automation アカウントでは、モジュールと Runbook を変更しないことをお勧めします。 この動作は、Az モジュールに固有のものではありません。 Automation アカウントで何らかの変更を行うときは、この動作を考慮する必要があります。

Automation アカウントに `Az` モジュールをインポートしても、Runbook で使用される PowerShell セッションにモジュールが自動的にインポートされることはありません。 モジュールは、次の状況で PowerShell セッションにインポートされます。

* モジュールのコマンドレットが Runbook から呼び出されたとき
* Runbook で `Import-Module` コマンドレットを使用してモジュールが明示的にインポートされたとき
* モジュールに依存する別のモジュールが、PowerShell セッションにインポートされたとき

> [!IMPORTANT]
> Automation アカウントの Runbook によって使用される PowerShell セッションに、`Az` または `AzureRM` どちらか一方のモジュールだけがインポートされ、両方がインポートされないようにすることが重要です。 Runbook で `AzureRM` の前に `Az` がインポートされた場合、Runbook は完了しますが、[get_SerializationSettings メソッドの参照エラー](troubleshoot/runbooks.md#get-serializationsettings)がジョブ ストリームで表示され、コマンドレットが適切に実行されない可能性があります。 `AzureRM` をインポートしてから `Az` をインポートした場合でも、Runbook は完了しますが、`Az` と `AzureRM` の両方を同じセッションにインポートできない、または同じ Runbook で使用できないことを示すエラーが、ジョブ ストリームで発生します。

## <a name="migrating-to-az-modules"></a>Az モジュールへの移行

テスト用の Automation アカウントで、AzureRM モジュールの使用から Az モジュールの使用への移行をテストすることをお勧めします。 その Automation アカウントを作成した後は、以下の手順を使用して、移行が円滑に進むことを確認できます。

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>AzureRM モジュールを使用するすべての Runbook を停止してスケジュールを解除する

`AzureRM` コマンドレットを使用する既存の Runbook が確実に実行されないようにするため、`AzureRM` モジュールを使用しているすべての Runbook を停止し、スケジュールの設定を解除する必要があります。 次の例を実行して、存在するスケジュールおよび削除する必要があるスケジュールを確認できます。

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

各スケジュールを個別に確認し、後で必要に応じて Runbook で再スケジュールできるようにすることが重要です。

### <a name="import-the-az-modules"></a>Az モジュールをインポートする

Runbook に必要な Az モジュールのみをインポートします。 ロールアップの `Az` モジュールをインポートしないでください。それには、インポートされるすべての `Az.*` モジュールが含まれます。 このガイダンスは、すべてのモジュールについて同じです。

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) モジュールは、他の `Az.*` モジュールに対する依存関係です。 そのため、他のすべてのモジュールをインポートする前に、このモジュールを Automation アカウントにインポートする必要があります。

Automation アカウントから、 **[共有リソース]** の **[モジュール]** を選択します。 **[ギャラリーの閲覧]** をクリックして、 **[ギャラリーの閲覧]** ページを開きます。  検索バーにモジュール名を入力します (`Az.Accounts` など)。 [PowerShell モジュール] ページで、 **[インポート]** をクリックしてモジュールを Automation アカウントにインポートします。

![Automation アカウントからモジュールをインポートする](media/az-modules/import-module.png)

このインポート プロセスは、[PowerShell ギャラリー](https://www.powershellgallery.com)でモジュールを検索して行うこともできます。 モジュールを見つけたら、それを選択し、 **[Azure Automation]** タブで **[Deploy to Azure Automation]\(Azure Automation にデプロイ\)** をクリックします。

![ギャラリーからモジュールを直接インポートする](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Runbook をテストする

`Az` モジュールを Automation アカウントにインポートした後は、代わりに Az モジュールを使用するように Runbook を編集できます。 大部分のコマンドレットは同じ名前ですが、`AzureRM` だけは `Az` に変更されています。 このプロセスに従わないモジュールの一覧については、[例外の一覧](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)をご覧ください。

新しいコマンドレットを使用するように Runbook を変更する前に、Runbook をテストする方法の 1 つは、Runbook の先頭で `Enable-AzureRMAlias -Scope Process` を使用することです。 Runbook にこれを追加することにより、変更しないで Runbook を実行できます。

## <a name="after-migration-details"></a>移行後の詳細

移行が完了した後は、`AzureRM` モジュールを使用する Runbook をアカウントで開始しないでください。 また、このアカウントでは `AzureRM` モジュールのインポートまたは更新を行わないこともお勧めします。 この時点から、このアカウントは `Az` に移行されたものと見なし、`Az` モジュールのみで運用してください。 新しい Automation アカウントを作成すると、既存の `AzureRM` モジュールもまだインストールされ、チュートリアル Runbook はまだ `AzureRM` コマンドレットで作成されています。 これらの Runbook は実行しないでください。

## <a name="next-steps"></a>次の手順

Az モジュールの使用について詳しくは、[Az モジュールの入門](/powershell/azure/get-started-azureps?view=azps-1.1.0)に関する記事をご覧ください。
