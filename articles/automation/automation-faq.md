---
title: Azure Automation の FAQ | Microsoft Docs
description: この記事では、Azure Automation についてよく寄せられる質問に対する回答を提供します。
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724274"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation についてよく寄せられる質問

この Microsoft FAQ は、Azure Automation についてよく寄せられる質問の一覧です。 その機能について他にご質問がある場合は、ディスカッション フォーラムにアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="update-management"></a>更新管理

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>予期しない OS レベルのアップグレードを防止することはできますか?

Red Hat Enterprise Linux など、Linux バリエーションの中には、パッケージを使用して OS レベルのアップグレードが発生することがあります。 これにより、OS バージョン番号の変更を伴う Update Management が実行される可能性があります。 Update Management でパッケージを更新するときに使用される方法は、管理者がローカルで Linux マシンに使用するものと同じであるため、この動作は意図的なものです。

Update Management のデプロイによる OS バージョンの更新を回避するには、**除外** 機能を使用します。

Red Hat Enterprise Linux で、除外するパッケージ名は `redhat-release-server.x86_64` です。

### <a name="why-arent-criticalsecurity-updates-applied"></a>重要な更新プログラムまたはセキュリティ更新プログラムが適用されないのはなぜですか?

更新プログラムを Linux マシンにデプロイする場合、更新プログラムの分類を選択できます。 このオプションにより、更新プログラムがフィルター処理され、指定した条件を満たすものに絞られます。 このフィルターは、更新プログラムが展開されるときに、マシンに対してローカルに適用されます。

Update Management では更新プログラムの強化がクラウドで実行されるため、Update Management で一部の更新プログラムに対して、セキュリティへの影響ありというフラグを設定できますが、ローカル マシンにはその情報がありません。 重大な更新プログラムを Linux マシンに適用する場合、そのマシンにセキュリティへの影響ありとマークされていない更新プログラムが存在する可能性があり、そのため、これらの更新プログラムは適用されません。 ただし、Update Management には関連する更新プログラムに関する追加情報があるため、そのマシンは引き続き非対応として報告されることがあります。

更新プログラムの分類ごとの更新プログラムのデプロイは、RTM バージョンの CentOS では動作しません。 CentOS に更新プログラムを正しくデプロイするには、更新プログラムが確実に適用されるように、すべての分類を選択します。 SUSE では、分類として **[他の更新プログラム]** のみを選択すると、その他のセキュリティ更新プログラムが zypper (パッケージ マネージャー) に関連している場合、またはその依存関係がまず必要になる場合は、それらがインストールされることがあります。 この動作は、zypper の制限です。 場合によっては、更新プログラムのデプロイを再実行してから、更新プログラムのログでデプロイを確認する必要があります。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure テナントの境界を越えて更新プログラムをデプロイすることはできますか?

Update Management への報告を行う別の Azure テナントに、修正プログラムを適用する必要があるマシンが存在する場合は、次の対処法を使用して、それらをスケジュールを設定する必要があります。 スケジュールを作成するには、`ForUpdateConfiguration` パラメーターを指定して [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) コマンドレットを使用します。 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) コマンドレットを使用して、他のテナントのマシンを `NonAzureComputer` パラメーターに渡すことができます。 次の例は、その方法を示したものです。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>プロセスの自動化 - Python Runbook

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure Automation ではどの Python 3 バージョンがサポートされていますか?

クラウド ジョブの場合、Python 3.8 がサポートされています。 コードが異なるバージョン間で互換性がある場合は、3.x バージョンのスクリプトとパッケージが動作する可能性があります。

Windows Hybrid Runbook Worker 上のハイブリッド ジョブの場合、使用したい任意の 3.x バージョンをインストールすることを選択できます。 Linux Hybrid Runbook Worker 上のハイブリッド ジョブの場合、DSC OMSConfig と Linux Hybrid Worker を実行するために、そのマシンにインストールされている Python 3 バージョンに依存します。 バージョン 3.6 をインストールすることをお勧めします。ただし、Python 3 のバージョン間でメソッド シグネチャまたはコントラクトに破壊的変更がない場合には、異なるバージョンでも機能します。

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 と Python 3 の Runbook は同じ Automation アカウントで実行できますか?

はい。同じ Automation アカウントで Python 2 と Python 3 の Runbook を使用することについて、制限はありません。  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>既存の Python 2 Runbook およびパッケージの Python 3 への移行に関して、どのような予定がありますか?

Azure Automation では、Python 2 Runbook およびパッケージを Python 3 に移行する予定はありません。 この移行は、ご自身で実行していただく必要があります。 既存および新しい Python 2 Runbook とパッケージは、引き続き機能します。

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Python 3 環境では、既定でどのようなパッケージがサポートされますか?

Azure パッケージ 4.0.0 が Python 3 Automation 環境に既定でインストールされます。 より新しいバージョンの Azure パッケージを手動でインポートし、既定のバージョンを上書きすることができます。

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Python 2 パッケージを参照する Python 3 Runbook (またはその逆) を実行した場合、どうなりますか?

Python 2 と Python 3 の実行環境は異なります。 Python 2 Runbook を実行している間は Python 2 パッケージのみをインポートでき、Python 3 についても同様です。

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Python 2 と Python 3 の Runbook およびパッケージを区別するにはどうすればよいですか?

Python 3 は新しい Runbook の定義であり、これによって Python 2 と Python 3 の Runbook を区別できます。 同様に、別のパッケージの種類が Python 3 パッケージに導入されています。

## <a name="next-steps"></a>次のステップ

こちらでご質問の回答が見つからない場合は、次のソースでさらに質問と回答を参照できます。

- [Azure Automation](/answers/topics/azure-automation.html)
- [フィードバック フォーラム](https://feedback.azure.com/forums/905242-update-management)
