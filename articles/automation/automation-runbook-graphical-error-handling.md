---
title: Azure Automation のグラフィカル Runbook におけるエラー処理
description: この記事では、グラフィカル Runbook でエラー処理ロジックを実装する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 24c7aaf08b4d22706bee8f37025b12a656ceaff5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98895902"
---
# <a name="handle-errors-in-graphical-runbooks"></a>グラフィック Runbook でのエラー処理

Azure Automation のグラフィカル Runbook について考慮する必要がある重要な設計原則は、実行中に Runbook で発生する可能性のある問題を識別することです。 それらの問題には、成功、予期されるエラー状態、予期しないエラー状態などがあります。

多くの場合、強制終了にならないエラーが Runbook アクティビティで発生すると、そのエラーに関係なく後続のすべてのアクティビティを処理することで、Windows PowerShell によってアクティビティが処理されます。 エラーで例外が生成される場合がありますが、それでも次のアクティビティは実行できます。

グラフィカル Runbook には、実行に関する問題に対処するためのエラー処理コードが含まれている必要があります。 アクティビティの出力を検証したり、エラーを処理したりするために、PowerShell コード アクティビティを使用して、アクティビティの出力リンクに条件付きロジックを定義したり、他の方法を適用したりすることができます。

Azure Automation グラフィカル Runbook は、機能が向上して、エラー処理ができるようになりました。 例外を強制終了にならないエラーに変換して、アクティビティ間のエラー リンクを作成することができます。 機能が向上した処理により、Runbook でエラーをキャッチし、実行後の状態または予期しない状態を管理できます。 

## <a name="powershell-error-types"></a>PowerShell のエラーの種類

Runbook 実行中に発生する可能性がある PowerShell のエラーの種類としては、強制終了になるエラーと、強制終了にならないエラーがあります。
 
### <a name="terminating-error"></a>強制終了になるエラー

強制終了になるエラーは、コマンドまたはスクリプトの実行が完全に停止する、実行中の重大なエラーです。 例として、存在しないコマンドレットの使用、コマンドレットが実行できなくなるような構文エラー、その他の致命的なエラーなどが挙げられます。

### <a name="non-terminating-error"></a>強制終了にならないエラー

強制終了にならないエラーは、エラー状態にもかかわらず実行を継続できる、重大でないエラーです。 例として、ファイルが見つからないエラーや、アクセス許可の問題など、操作関係のエラーが挙げられます。

## <a name="when-to-use-error-handling"></a>エラー処理を使用する場合

重要なアクティビティにおいてエラーまたは例外がスローされたときには、Runbook でエラー処理を使用します。 Runbook の次のアクティビティが処理されないようにして、エラーを適切に処理することが重要です。 エラー処理は特に、Runbook がビジネスやサービスの運用プロセスをサポートしている場合に重要です。

## <a name="add-error-links"></a>エラー リンクを追加する

エラーが発生する可能性のあるアクティビティごとに、他の任意のアクティビティを指し示すエラー リンクを追加できます。 リンク先としては、コード アクティビティ、コマンドレットの呼び出し、別の Runbook の呼び出しなど、任意の種類のアクティビティを指定できます。 リンク先アクティビティには出力方向のリンクを追加することもでき、通常のリンクまたはエラー リンクのいずれかを設定できます。 Runbook ではリンクによって、コード アクティビティに頼らずに複雑なエラー処理ロジックを実装できます。

一般的な機能を備えた専用のエラー処理 Runbook を作成することをお勧めしますが、これを実践することは必須ではありません。 たとえば、仮想マシンを起動し、そこにアプリケーションをインストールしようとする Runbook があるとします。 VM が正しく起動しない場合、以下が実行されます。

1. この問題に関する通知を送信する。
2. 代わりの新しい VM を自動的にプロビジョニングする別の Runbook を開始する。

解決策の 1 つは、手順 1. を処理するアクティビティを指し示すエラー リンクを Runbook 内に作成することです。 たとえば、Runbook は、`Write-Warning` コマンドレットを、[Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) コマンドレットなどの手順 2 のアクティビティに接続できます。

この動作は、多くの Runbook で使用するために、これら 2 つのアクティビティを別個のエラー処理用 Runbook に分けることで、汎用化することもできます。 元の Runbook からこのエラー処理 Runbook を呼び出す前に、データからカスタム メッセージを作成し、それをパラメーターとしてエラー処理 Runbook に渡すことができます。

## <a name="turn-exceptions-into-non-terminating-errors"></a>強制終了にならないエラーに例外を変換する

Runbook 内の各アクティビティには、例外を強制終了にならないエラーに変換する構成設定が用意されています。 既定では、この設定は無効です。 この設定は、Runbook でエラーが処理されるすべてのアクティビティに対して有効にすることが推奨されます。 この設定では、Runbook ではエラー リンクを利用して、アクティビティの強制終了になるエラーとならないエラーの両方が、強制終了にならないエラーとして処理されます。  

構成設定を有効にした後に、Runbook に、エラーを処理するアクティビティを作成します。 アクティビティで何らかのエラーが発生すると、出力方向のエラー リンクが使用されます。 アクティビティで通常の出力が生成された場合でも、通常リンクは使用されません。<br><br> ![Automation Runbook のエラー リンクの例](media/automation-runbook-graphical-error-handling/error-link-example.png)

次の例では、VM のコンピューター名を格納している変数が、Runbook によって取得されます。 その後、次のアクティビティで VM の起動が試みられます。<br><br> ![Automation Runbook のエラー処理の例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable` アクティビティと [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) コマンドレットは、例外をエラーに変換するように構成されています。 変数の取得や VM の起動で問題がある場合は、コードによってエラーが生成されます。<br><br> ![Automation Runbook のエラー処理アクティビティの設定](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)。

エラー リンクは、これらのアクティビティから 1 つの `error management` コード アクティビティにフローします。 このアクティビティは、`throw` キーワードを使用して処理を停止し、`$Error.Exception.Message` で現在の例外を説明するメッセージを取得する単純な PowerShell 式で構成されています。<br><br> ![Automation Runbook のエラー処理コードの例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>次のステップ

* グラフィカル Runbook エラーの解決については、[Runbook の問題のトラブルシューティング](troubleshoot/runbooks.md)に関するページを参照してください。
