---
title: Azure Automation の Runbook の種類
description: 'Azure Automation で使用できる Runbook の種類、および使用する種類を決定するときの考慮事項について説明します。 '
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1991b7c227e62efcd8c0b637f3e732d737cabb34
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850688"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation の Runbook の種類

Azure Automation がサポートする数種類の Runbook について次の表で簡単に説明します。  その後のセクションでは、使用するときの考慮事項など、各種類に関して詳しく説明します。

| 種類 | 説明 |
|:--- |:--- |
| [グラフィカル](#graphical-runbooks)|Windows PowerShell に基づいており、Azure ポータルのグラフィカル エディターで完全に作成および編集されます。 |
| [グラフィカル PowerShell ワークフロー](#graphical-runbooks)|Windows PowerShell ワークフローに基づいており、Azure ポータルのグラフィカル エディターで完全に作成および編集されます。 |
| [PowerShell](#powershell-runbooks) |Windows PowerShell スクリプトに基づくテキスト Runbook です。 |
| [PowerShell ワークフロー](#powershell-workflow-runbooks)|Windows PowerShell ワークフローに基づくテキスト Runbook です。 |
| [Python](#python-runbooks) |Python に基づくテキスト Runbook です。 |

## <a name="graphical-runbooks"></a>グラフィック Runbook

[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) とグラフィカル PowerShell ワークフロー Runbookは、Azure ポータルのグラフィカル エディターで作成および編集します。  ファイルにエクスポートして別の Automation アカウントにインポートできます。 ただし、別のツールで作成または編集することはできません。 グラフィカル Runbook は PowerShell のコードを生成しますが、そのコードを直接表示または変更することはできません。 グラフィカル Runbook をいずれかの[テキスト形式](automation-runbook-types.md)に変換すること、またはテキスト Runbook をグラフィカル形式に変換することはできません。 インポート時には、グラフィカル Runbook からグラフィカル PowerShell ワークフロー Runbook への変換、およびこの逆の変換を行うことができます。

### <a name="advantages"></a>長所

* 視覚的な insert-link-configure 作成モデル
* プロセスを介したデータの流れに専念
* 管理プロセスを視覚的に表現
* 他の Runbook を子 Runbook として含めることで、高度なワークフローが作成可能
* モジュール プログラミングを促進

### <a name="limitations"></a>制限事項

* Azure ポータルの外部では Runbook を編集できません。
* 複雑なロジックを実行するために、PowerShell ワークフローのコードを含むコード アクティビティが必要になる場合があります。
* グラフィカル ワークフローによって作成された PowerShell コードを表示したり、直接編集したりすることはできません。 コード アクティビティで作成したコードは表示できます。
* Linux Hybrid Runbook Worker 上では実行できません。

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook は、Windows PowerShell に基づきます。  Azure ポータルのテキスト エディターを使用して、Runbook のコードを直接編集します。  また、任意のオフライン テキスト エディターを使用したり、Azure Automation に [Runbook をインポート](manage-runbooks.md) したりすることもできます。

### <a name="advantages"></a>長所

* すべての複雑なロジックを PowerShell コードで実装でき、PowerShell ワークフローに関する複雑さが加わることはありません。
* 実行前にコンパイルする必要がないため、PowerShell ワークフロー Runbook より開始に時間がかかりません。
* Azure で実行できるほか、Linux と Windows 両方の Hybrid Runbook Worker 上で実行できます。

### <a name="limitations"></a>制限事項

* 作成者は、PowerShell スクリプトに熟知している必要があります。
* [並列処理](automation-powershell-workflow.md#parallel-processing)を使用して複数のアクションを並列に実行することはできません。
* エラーがある場合は、[チェックポイント](automation-powershell-workflow.md#checkpoints)を使用して Runbook を再開することはできません。
* PowerShell ワークフロー Runbook とグラフィカル Runbook を子 Runbook として組み込むには、新しいジョブを作成する Start-AzureAutomationRunbook コマンドレットを使用する必要があります。

### <a name="known-issues"></a>既知の問題

PowerShell Runbook に関する現在の既知の問題は次のとおりです。

* PowerShell Runbook では、null 値を含む暗号化されていない[変数資産](automation-variables.md)は取得できません。
* PowerShell Runbook では、名前に *~* が含まれる[変数資産](automation-variables.md)は取得できません。
* PowerShell Runbook のループ内の Get-Process が、約 80 回反復した後でクラッシュする可能性があります。
* 一度に大量のデータを出力ストリームに書き込もうとした場合、PowerShell Runbook が失敗する可能性があります。   通常は、大きいオブジェクトを使用する場合、必要な情報だけを出力することによってこの問題を回避できます。  たとえば、*Get-Process* などを出力するのではなく、*Get-Process | Select ProcessName, CPU* で必要なフィールドだけを出力できます。

## <a name="powershell-workflow-runbooks"></a>PowerShell ワークフロー Runbook

PowerShell ワークフロー Runbook は、 [Windows PowerShell ワークフロー](automation-powershell-workflow.md)に基づくテキスト Runbook です。  Azure ポータルのテキスト エディターを使用して、Runbook のコードを直接編集します。  また、任意のオフライン テキスト エディターを使用したり、Azure Automation に [Runbook をインポート](manage-runbooks.md) したりすることもできます。

### <a name="advantages"></a>長所

* すべての複雑なロジックを PowerShell ワークフローのコードで実装できます。
* エラーがある場合は、[チェックポイント](automation-powershell-workflow.md#checkpoints)を使用して Runbook を再開します。
* [並列処理](automation-powershell-workflow.md#parallel-processing) を使用して複数のアクションを並列に実行できます。
* 高度なワークフローを作成するために、他のグラフィカル Runbook や PowerShell ワークフロー Runbook を子 Runbook として組み込むことができます。

### <a name="limitations"></a>制限事項

* 作成者は、PowerShell ワークフローについて熟知する必要があります。
* [逆シリアル化されたオブジェクト](automation-powershell-workflow.md#code-changes)など、PowerShell ワークフローに関する別の複雑さに対応する必要があります。
* グラフィカル Runbook は、実行する前にコンパイルする必要があるため、PowerShell Runbook より開始に時間がかかります。
* PowerShell Runbook を子 Runbook として組み込むには、新しいジョブを作成する Start-AzureAutomationRunbook コマンドレットを使用する必要があります。
* Linux Hybrid Runbook Worker 上では実行できません。

## <a name="python-runbooks"></a>Python Runbook

Python Runbook は Python 2 でコンパイルします。  Azure portal でテキスト エディターを使用して Runbook のコードを直接編集したり、任意のオフライン テキスト エディターを使用して Azure Automation に [Runbook をインポート](manage-runbooks.md)したりできます。

### <a name="advantages"></a>長所

* 堅牢な Python ライブラリを利用します。
* Azure で実行できるほか、両方の Linux Hybrid Runbook Worker 上で実行できます。 Windows Hybrid Runbook Worker がサポートされるには、[Python2.7](https://www.python.org/downloads/release/latest/python2) がインストールされている必要があります。

### <a name="limitations"></a>制限事項

* 作成者は、Python スクリプトを使い慣れている必要があります。
* 現時点では Python 2 のみがサポートされているため、Python 3 の特定の機能は失敗します。
* サードパーティ製のライブラリを使用するには、Automation アカウントに[パッケージをインポート](python-packages.md)してライブラリを使用できるようにする必要があります。

## <a name="considerations"></a>考慮事項

特定の Runbook に使用する種類を決定するときは、さらに次のことを考慮します。

* グラフィカル Runbook からテキスト Runbook への変換、およびこの逆の変換を行うことはできません。
* 異なる種類の Runbook を子 Runbook として使用する場合は制限があります。 詳しくは、「[Azure Automation での子 Runbook](automation-child-runbooks.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* グラフィカル Runbook 作成の詳細については、「 [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* Runbook 用の PowerShell と PowerShell ワークフローとの違いについては、「 [Windows PowerShell ワークフローについて](automation-powershell-workflow.md)
* Runbook を作成またはインポートする方法については、「 [Runbook の作成またはインポート](manage-runbooks.md)
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)に関するページを参照してください。
