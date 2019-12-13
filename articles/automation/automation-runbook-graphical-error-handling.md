---
title: Azure Automation のグラフィカル Runbook におけるエラー処理
description: この記事では、Azure Automation のグラフィカル Runbook でエラー処理ロジックを実装する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cb4f6827f58b882b8b1ae0ef8c093a09e090f17a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850705"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure Automation のグラフィカル Runbook におけるエラー処理

Runbook の重要な設計原則として考慮する必要があるのは、Runbook で発生する可能性のあるさまざまな問題を特定することです。 それらの問題には、成功、予期されるエラー状態、予期しないエラー状態などがあります。

Runbook には、エラー処理が含まれている必要があります。 グラフィカル Runbook でアクティビティの出力を検証したり、エラーを処理したりするために、Windows PowerShell コード アクティビティを使用して、アクティビティの出力リンクに条件付きロジックを定義したり、他の方法を適用したりすることができます。          

多くの場合、Runbook アクティビティで強制終了にならないエラーが発生すると、後続のアクティビティはエラーに関係なく処理されます。 エラーで例外が生成される場合がありますが、それでも次のアクティビティは実行できます。 これが、PowerShell で設計されているエラー処理方法です。    

実行中に発生する可能性がある PowerShell エラーには、強制終了になるエラーと強制終了にならないエラーがあります。 強制終了になるエラーと強制終了にならないエラーの違いは、次のとおりです。

* **強制終了になるエラー**:実行中の重大なエラー。コマンド (またはスクリプトの実行) は完全に停止します。 例として、存在しないコマンドレットの使用、コマンドレットが実行できなくなるような構文エラー、その他の致命的なエラーなどが挙げられます。

* **強制終了にならないエラー**:重大でないエラー。無視して実行を継続できます。 例として、ファイルが見つからない、アクセス許可に問題があるなど、操作上のエラーが挙げられます。

Azure Automation グラフィカル Runbook は、機能が向上して、エラー処理ができるようになりました。 例外を強制終了にならないエラーに変換して、アクティビティ間のエラー リンクを作成することができます。 この処理により、Runbook の作成者はエラーをキャッチし、実行後の状態または予期しない状態を管理することができます。  

## <a name="when-to-use-error-handling"></a>エラー処理を使用する場合

エラーまたは例外をスローする重大なアクティビティが存在する場合は、Runbook 内の次のアクティビティが処理されないようにして、エラーを適切に処理することが重要です。 これは特に、Runbook がビジネス操作プロセスまたはサービス操作プロセスをサポートしている場合に重要です。

Runbook の作成者は、エラーが発生する可能性のある各アクティビティに、他の任意のアクティビティを指すエラー リンクを追加できます。 リンク先アクティビティとしては、コード アクティビティ、コマンドレットの呼び出し、別の Runbook の呼び出しなど、任意の種類のアクティビティを指定できます。

また、リンク先アクティビティにもさらに出力方向のリンクを追加することができます。 それらのリンクは、通常のリンクにすることもエラー リンクにすることもできます。 つまり、Runbook の作成者は、コード アクティビティに頼らずに複雑なエラー処理ロジックを実装できます。 一般的な機能を備えた専用のエラー処理 Runbook を作成することをお勧めしますが、必須ではありません。 PowerShell コード アクティビティでのエラー処理ロジックは、唯一のオプションではありません。  

たとえば、VM を起動し、その VM にアプリケーションをインストールしようとする Runbook があるとします。 VM が正しく起動しない場合は、次の 2 つのアクションを実行します。

1. この問題に関する通知を送信する。
2. 代わりの新しい VM を自動的にプロビジョニングする別の Runbook を開始する。

ソリューションの 1 つは、手順 1. を処理するアクティビティを指すエラー リンクを作成することです。 たとえば、**Write-Warning** コマンドレットを、手順 2. のアクティビティ (**Start-AzureRmAutomationRunbook** コマンドレットなど) に接続することができます。

この動作は複数の Runbook で使用できるように汎用化することもできます。また、以前に提示したガイダンスに従い、これら 2 つのアクティビティを個別のエラー処理 Runbook に分けることもできます。 このエラー処理 Runbook を呼び出す前に、元の Runbook 内のデータからカスタム メッセージを構築し、パラメーターとしてエラー処理 Runbook に渡すことができます。

## <a name="how-to-use-error-handling"></a>エラー処理を使用する方法

各アクティビティには、例外を強制終了にならないエラーに変換する構成設定が用意されています。 既定では、この設定は無効です。 エラーを処理するアクティビティでは、この設定を有効にすることをお勧めします。  

この構成を有効にすることで、アクティビティの強制終了になるエラーとならないエラーの両方が強制終了にならないエラーとして処理され、エラー リンクで処理できるようになります。  

この設定を構成した後で、エラーを処理するアクティビティを作成します。 アクティビティで何らかのエラーが発生すると、出力方向のエラー リンクが使用されます。アクティビティが通常の出力を生成した場合でも、通常リンクは使用されません。<br><br> ![Automation Runbook のエラー リンクの例](media/automation-runbook-graphical-error-handling/error-link-example.png)

次の例では、Runbook が仮想マシンのコンピューター名を含む変数を取得します。 その後、次のアクティビティで、仮想マシンの起動を試みます。<br><br> ![Automation Runbook のエラー処理の例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

**Get-AutomationVariable** アクティビティと **Start-AzureRmVm** は、例外をエラーに変換するように構成されています。 変数の取得や VM の起動に関する問題がある場合には、エラーが生成されます。<br><br> ![Automation Runbook のエラー処理アクティビティの設定](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

エラー リンクは、これらのアクティビティから単一の**エラー管理**アクティビティ (コード アクティビティ) にリンクされています。 このアクティビティは単純な PowerShell の式で構成されており、*Throw* キーワードで処理を停止し、 *$Error.Exception.Message* で現在の例外に関するメッセージを取得するようになっています。<br><br> ![Automation Runbook のエラー処理コードの例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>次の手順

* グラフィカル Runbook でのリンクおよびリンクの種類の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md#links-and-workflow)」を参照してください。

* Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、[Runbook ジョブの追跡](automation-runbook-execution.md)に関するページを参照してください。

