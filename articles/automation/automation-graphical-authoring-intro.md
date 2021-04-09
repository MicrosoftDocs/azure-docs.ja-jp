---
title: Azure Automation でグラフィカル Runbook を作成する
description: この記事では、コードを使用せず、グラフィカル Runbook を作成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: bbac794263fec176e03c7148d860c479a2ed9d39
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501230"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Azure Automation でグラフィカル Runbook を作成する

Azure Automation の Runbook はすべて Windows PowerShell ワークフローです。 グラフィカル Runbook とグラフィカル PowerShell ワークフロー Runbook は、Automation ワーカーが実行する PowerShell コードを生成しますが、これを表示したり変更したりすることはできません。 グラフィカル Runbook をグラフィカル PowerShell ワークフロー Runbook に変換することも、その逆を行うこともできます。 ただし、これらの Runbook をテキスト形式の Runbook に変換することはできません。 また、Automation のグラフィカル エディターでは、テキスト形式の Runbook をインポートできません。

グラフィカル作成では、基になる Windows PowerShell または PowerShell Workflow の複雑なコードを使用しなくても、Azure Automation の Runbook を作成することができます。 コマンドレットや Runbook のライブラリからキャンバスにアクティビティを追加し、それらをリンクして、ワークフローを形成するように構成できます。 これまでに System Center Orchestrator または Service Management Automation (SMA) で作業したことがあれば、グラフィカル作成には見覚えがあるはずです。 この記事では、グラフィカル Runbook の作成を開始するうえで必要な概念について説明します。

## <a name="overview-of-graphical-editor"></a>グラフィカル エディターの概要

Azure ポータルでグラフィカル Runbook を作成または編集することで、グラフィカル エディターを開くことができます。

![グラフィカル ワークスペース](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

次のセクションでは、グラフィカル エディターのコントロールについて説明します。

### <a name="canvas-control"></a>Canvas コントロール

キャンバス コントロールを使用すると、Runbook を設計できます。 ライブラリ コントロールのノードから Runbook にアクティビティを追加し、それらをリンクで接続して Runbook のロジックを定義できます。 キャンバスの下部にはコントロールがあり、それを使用して拡大および縮小できます。

### <a name="library-control"></a>ライブラリ コントロール

ライブラリ コントロールを使用すると、Runbook に追加する[アクティビティ](#use-activities)を選択できます。 それらをキャンバスに追加し、そこで他のアクティビティに接続できます。 ライブラリ コントロールには、次の表に定義されているセクションが含まれています。

| Section | 説明 |
|:--- |:--- |
| コマンドレット |Runbook で使用できるすべてのコマンドレット。 コマンドレットはモジュールごとに整理されます。 Automation アカウントにインストールしたすべてのモジュールを使用できます。 |
| Runbooks |お使いの Automation アカウントの Runbook。 これらの Runbook はキャンバスに追加して、子 Runbook として使用できます。 編集している Runbook と同じコアの種類の Runbook のみが表示されます。 グラフィカル Runbook の場合は、PowerShell ベースの Runbook のみが表示されます。 グラフィカル PowerShell ワークフロー Runbook の場合は、PowerShell ワークフローベースの Runbook のみが表示されます。 |
| アセット |Runbook で使用できる、Automation アカウント内の [オートメーション資産](/previous-versions/azure/dn939988(v=azure.100))。 Runbook に資産を追加すると、選択した資産を取得するワークフロー アクティビティが追加されます。 変数資産の場合は、変数を取得するアクティビティを追加するか、変数を設定するアクティビティを追加するかを選択できます。 |
| Runbook コントロール |現在の Runbook で使用できるコントロール アクティビティ。 ジャンクション アクティビティは複数の入力を受け取り、すべてが完了するまで待機してから、ワークフローを続行します。 コード アクティビティは、グラフィカル Runbook の種類に応じて、PowerShell または PowerShell ワークフローの 1 行以上のコードを実行します。 このアクティビティは、カスタム コードや、他のアクティビティでの実現が難しい機能に使用できます。 |

### <a name="configuration-control"></a>構成コントロール

構成コントロールを使用すると、キャンバスで選択されているオブジェクトの詳細を指定できます。 このコントロールで使用できるプロパティは、選択したオブジェクトの種類によって異なります。 構成コントロールでオプションを選択すると、詳しい情報を指定するための追加のブレードが開きます。

### <a name="test-control"></a>テスト コントロール

グラフィカル エディターが初めて起動された場合、テスト コントロールは表示されません。 グラフィカル Runbook を対話形式でテストするときに開きます。

## <a name="use-activities"></a>アクティビティを使用する

アクティビティは Runbook の構成要素です。 アクティビティは、PowerShell コマンドレット、子 Runbook、またはワークフローの場合があります。 Runbook にアクティビティを追加するには、ライブラリ コントロールでそれを右クリックし、 **[キャンバスに追加]** を選択します。 その後、アクティビティをクリックしてドラッグし、キャンバス上の任意の場所に配置できます。 キャンバス上のアクティビティの場所が、Runbook の操作に影響することはありません。 Runbook は、その操作を視覚化するのに最適な方法でレイアウトできます。

![キャンバスに追加](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

キャンバス上でアクティビティを選択し、構成ブレードでそのプロパティとパラメーターを構成します。 アクティビティのラベルは、わかりやすい名前に変更できます。 Runbook では引き続き元のコマンドレットが実行されます。 単に、グラフィカル エディターで使用される表示名を変更するだけです。 ラベルは、Runbook 内で一意でなければならないことに注意してください。

### <a name="parameter-sets"></a>パラメーター セット

パラメーター セットは、特定のコマンドレットの値を受け入れる必須および省略可能なパラメーターを定義します。 すべてのコマンドレットには 1 つ以上のパラメーター セットがあり、複数のセットがあるものもあります。 コマンドレットに複数のパラメーター セットがある場合は、使用するものを必ず選択してから、パラメーターを構成できます。 アクティビティで使用されるパラメーター セットは、 **[パラメーター セット]** を選択して別のセットを選ぶことで変更できます。 この場合、既に構成済みのパラメーター値はすべて失われます。

次の例で、[Get-AzVM](/powershell/module/az.compute/get-azvm) コマンドレットには 3 つのパラメーター セットがあります。 例では、**ListVirtualMachineInResourceGroupParamSet** という 1 つのセットを使用します。これには、リソース グループ内のすべての仮想マシンを返すための、1 つの省略可能なパラメーターがあります。 また、例では、返される仮想マシンを指定するために **GetVirtualMachineInResourceGroupParamSet** パラメーター セットも使用しています。 このセットには、2 つの必須パラメーターと 1 つの省略可能なパラメーターがあります。

![パラメーター セット](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>パラメーターの値

パラメーターの値を指定する場合は、データ ソースを選択して値の指定方法を決定します。 特定のパラメーターに使用できるデータ ソースは、そのパラメーターの有効な値によって異なります。 たとえば、null 値が許可されていないパラメーターに対するオプションとして Null を使用することはできません。

| Data Source | 説明 |
|:--- |:--- |
| 定数値 |パラメーターの値を入力します。 このデータ ソースは、次のデータ型に対してのみ使用できます。Int32、Int64、String、Boolean、DateTime、Switch です。 |
| アクティビティの出力 |ワークフローの現在のアクティビティより前のアクティビティからの出力を使用します。 有効なすべてのアクティビティが一覧表示されます。 パラメーター値には、出力を生成するアクティビティだけを使用します。 複数のプロパティを持つオブジェクトがアクティビティによって出力される場合は、アクティビティを選択した後に、特定のプロパティの名前を入力できます。 |
| Runbook の入力 |Runbook の入力をアクティビティ パラメーターの入力として選択します。 |
| 変数資産 |Automation の変数を入力として選択します。 |
| 資格情報資産 |Automation の資格情報を入力として選択します。 |
| 証明書資産 |Automation の証明書を入力として選択します。 |
| 接続資産 |Automation の接続を入力として選択します。 |
| PowerShell 式 |簡単な [PowerShell 式](#work-with-powershell-expressions)を指定します。 式は、アクティビティの前に評価され、結果がパラメーター値に使用されます。 変数を使用すれば、アクティビティの出力または Runbook の入力パラメーターを参照することができます。 |
| 未構成 |以前に構成されていた値をすべてクリアします。 |

#### <a name="optional-additional-parameters"></a>省略可能な追加パラメーター

すべてのコマンドレットで、追加のパラメーターを指定することができます。 これらは、PowerShell の共通パラメーターまたはその他のカスタム パラメーターです。 グラフィカル エディターには、PowerShell 構文を使用してパラメーターを指定できるテキスト ボックスが表示されます。 たとえば、`Verbose` 共通パラメーターを使用するには、`-Verbose:$True` を指定する必要があります。

### <a name="retry-activity"></a>再試行アクティビティ

アクティビティの再試行機能を使用すると、ループのように、特定の条件が満たされるまで複数回実行できます。 この機能は、複数回実行する必要があるアクティビティ、エラーが発生しやすく、成功までに複数回の試行が必要になる可能性があるアクティビティ、またはアクティビティの出力情報が有効なデータかどうかをテストするアクティビティに使用できます。

アクティビティの再試行を有効にするときに、遅延と条件を設定できます。 遅延とは、アクティビティを次回実行するまでに Runbook が待機する時間 (秒単位または分単位) です。 遅延を指定しない場合、アクティビティは完了した直後に再び実行されます。

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="再試行を有効にする機能の設定のスクリーンショット。":::

再試行の条件は、アクティビティが実行されるたびに評価される PowerShell 式です。 式が True に解決されると、アクティビティは再実行されます。 式が False に解決されると、アクティビティは再実行されず、Runbook は次のアクティビティに移ります。

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="[次の条件が TRUE になるまで再試行を実行する] フィールドと、再試行条件で使用できる PowerShell 式の例のスクリーンショット。":::

再試行の条件には、アクティビティの再試行に関する情報へのアクセスを提供する `RetryData` という名前の変数を使用できます。 この変数には、次の表のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| `NumberOfAttempts` |アクティビティが実行された回数。 |
| `Output` |最後に実行されたアクティビティの出力。 |
| `TotalDuration` |初回のアクティビティが開始されてからの経過時間。 |
| `StartedAt` |アクティビティが最初に開始された時刻 (UTC 形式)。 |

アクティビティの再試行条件の例を次に示します。

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

アクティビティの再試行条件を構成すると、そのアクティビティには、通知用に 2 つの視覚的な合図が含まれます。 1 つはアクティビティに表示され、もう 1 つはアクティビティの構成を確認するときに表示されます。

![Activity Retry Visual Indicators](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>ワークフロー スクリプト コントロール

ワークフロー スクリプト コントロールは、作成するグラフィカル Runbook の種類に応じて、PowerShell または PowerShell ワークフローのスクリプトを受け入れる特別なアクティビティです。 このコントロールは、他の方法では使用できない可能性がある機能を提供します。 これはパラメーターを受け入れることはできませんが、アクティビティの出力と Runbook の入力パラメーターで変数を使用できます。 アクティビティのすべての出力はデータバスに追加されます。 例外は発信リンクがない出力です。その場合、出力は Runbook の出力に追加されます。

たとえば、次のコードでは `NumberOfDays` という名前の Runbook 入力変数を使用して、日付の計算を実行します。 次に、計算された DateTime 値を、Runbook 内の後続のアクティビティで使用される出力として送信します。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>ワークフローのリンクを使用する

グラフィカル Runbook のリンクは 2 つのアクティビティを接続します。 キャンバスでは、ソース アクティビティから接続先アクティビティを指す矢印として表示されます。 アクティビティは矢印の方向に実行され、ソース アクティビティが完了すると接続先アクティビティが開始されます。

### <a name="create-a-link"></a>リンクの作成

ソース アクティビティを選択し、図形の下部にある円をクリックして、2 つのアクティビティ間のリンクを作成できます。 接続先アクティビティまで矢印をドラッグして離します。

![リンクの作成](media/automation-graphical-authoring-intro/create-link-options.png)

リンクを選択して、構成ブレードでそのプロパティを構成します。 プロパティには、次の表で説明されているリンクの種類が含まれます。

| リンクの種類 | 説明 |
|:--- |:--- |
| パイプライン |接続先アクティビティは、ソース アクティビティからのオブジェクト出力ごとに 1 回実行されます。 ソース アクティビティからの出力がない場合、接続先アクティビティは実行されません。 ソース アクティビティからの出力はオブジェクトとして使用できます。 |
| Sequence |接続先アクティビティは、ソース アクティビティからの出力を受け取ったときに 1 回だけ実行されます。 ソース アクティビティからの出力は、オブジェクトの配列として使用できます。 |

### <a name="start-runbook-activity"></a>Runbook アクティビティの開始

グラフィカル Runbook は、受信リンクのないすべてのアクティビティから開始します。 多くの場合、Runbook の開始アクティビティとして動作するアクティビティは 1 つだけです。 複数のアクティビティに受信リンクがない場合、Runbook はそれらを並列で実行して開始します。 その後、リンクに従って、各アクティビティの完了時に別のアクティビティを実行します。

### <a name="specify-link-conditions"></a>リンクの条件を指定する

リンクに条件を指定すると、その条件が True に解決される場合にのみ、接続先アクティビティが実行されます。 ソース アクティビティからの出力を取得するには、通常は条件に `ActivityOutput` 変数を使用します。

パイプライン リンクの場合は、1 つのオブジェクトに対して条件を指定する必要があります。 Runbook は、ソース アクティビティによる各オブジェクト出力の条件を評価します。 その後、条件を満たす各オブジェクトに対して接続先アクティビティが実行されます。 たとえば、`Get-AzVM` のソース アクティビティでは、条件付きパイプライン リンクに次の構文を使用すると、Group1 という名前のリソース グループ内の仮想マシンのみを取得できます。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

シーケンス リンクの場合は、ソース アクティビティからのすべてのオブジェクトを含む 1 つの配列が返されるため、Runbook は条件を一度だけ評価します。 このため、Runbook では、パイプライン リンクを使ってできるように、フィルター処理にシーケンス リンクを使用することはできません。 シーケンス リンクは、単純に、次のアクティビティが実行されるかどうかを判断できます。

たとえば、**Start VM** Runbook で次のアクティビティ セットを使用します。

![Conditional Link with Sequences](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

この Runbook では、入力パラメーターの `VMName` と `ResourceGroupName` の値を検証する 3 つの異なるシーケンス リンクを使用して、実行する適切なアクションを決定します。 実行可能なアクションは、1 つの VM の開始、リソース グループ内のすべての VM の開始、またはサブスクリプション内のすべての VM の開始です。 `Connect to Azure` と `Get single VM`間のシーケンス リンクの場合、条件ロジックは次のようになります。

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

条件付きリンクを使用する場合は、その分岐でソース アクティビティから他のアクティビティまでの使用可能なデータが、条件によってフィルター処理されます。 アクティビティが複数のリンクのソースの場合、各分岐でアクティビティが使用できるデータは、その分岐に接続するリンクの条件によって異なります。

たとえば、以下の Runbook の `Start-AzVM` アクティビティでは、すべての仮想マシンを起動します。 このアクティビティには 2 つの条件付きリンクがあります。 最初の条件付きリンクでは、`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` 式を使用して、`Start-AzVM` アクティビティが正常に完了したかどうかをフィルター処理します。 2 番目の条件付きリンクでは、`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` 式を使用して、`Start-AzVm` アクティビティで仮想マシンの起動が失敗したかどうかをフィルター処理します。

![条件付きリンクの例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

最初のリンクに従い、`Get-AzureVM` からのアクティビティ出力を使用するアクティビティでは、`Get-AzureVM` が実行されたときに起動された仮想マシンのみを取得します。 2 番目のリンクに従うアクティビティでは、`Get-AzureVM` が実行されたときに停止された仮想マシンのみを取得します。 3 番目のリンクに従うアクティビティでは、実行状態に関係なく、すべての仮想マシンを取得します。

### <a name="use-junctions"></a>接合を使用する

接合は特別なアクティビティで、受信分岐がすべて完了するまで待機します。 これにより、Runbook は、複数のアクティビティを並列で実行し、次に進む前にすべてを確実に完了できます。

ジャンクションが受け取れる受信リンクの数に制限はありませんが、パイプラインにすることができるのはそれらのリンクのうち 1 つだけです。 受信シーケンス リンクの数に制限はありません。 複数の受信パイプライン リンクを持つジャンクションを作成して、Runbook を保存することはできますが、それを実行すると失敗します。

次の例は、一連の仮想マシンに同時に修正プログラムをダウンロードする間に、それらのマシンを開始する Runbook の一部です。 ジャンクションを使用して、両方のプロセスが完了したことを確認してから、Runbook を続行します。

![ジャンクション](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>サイクルを操作する

サイクルは、接続先アクティビティがそのソース アクティビティに戻るようにリンクされている場合、または別のアクティビティにリンクされていて、それが最終的にそのソースに戻るようになっている場合に形成されます。 現在、グラフィカル作成ではサイクルはサポートされていません。 Runbook にサイクルがある場合、保存は正常に行われますが、実行時にエラーが発生します。

![サイクル](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>アクティビティ間でデータを共有する

発信リンクがあるアクティビティで出力されるデータは、Runbook のデータバスに書き込まれます。 Runbook のすべてのアクティビティはデータバスのデータを使用して、パラメーターの値を設定したり、スクリプト コードに含めたりすることができます。 アクティビティは、ワークフロー内の前のアクティビティの出力にアクセスできます。

データバスにデータが書き込まれる方法は、アクティビティのリンクの種類によって異なります。 パイプライン リンクの場合、データは複数のオブジェクトとして出力されます。 シーケンス リンクの場合、データは配列として出力されます。 値が 1 つしかない場合は、単一要素配列として出力されます。

Runbook がデータバス上のデータにアクセスするには、2 つの方法があります。 
* アクティビティ出力のデータ ソースを使用する。
* PowerShell 式のデータ ソースを使用する。

最初のメカニズムは、アクティビティ出力のデータ ソースを使用して、別のアクティビティのパラメーターを設定します。 出力がオブジェクトの場合、Runbook は 1 つのプロパティを指定できます。

![アクティビティの出力](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

2 番目のデータ アクセス メカニズムでは、PowerShell 式データ ソースでのアクティビティの出力を取得するか、以下に示す構文を使用して `ActivityOutput` 変数でワークフロー スクリプト アクティビティの出力を取得します。 出力がオブジェクトの場合、Runbook は 1 つのプロパティを指定できます。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>チェックポイントを使用する

任意のアクティビティで [[Checkpoint runbook]\(Runbook のチェックポイント\)](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) を選択することにより、グラフィカル PowerShell ワークフロー Runbook に **チェックポイント** を設定できます。 この場合、アクティビティの実行後にチェックポイントが設定されます。

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

チェックポイントは、グラフィカル PowerShell ワークフロー Runbook でのみ有効であり、グラフィカル Runbook では使用できません。 Runbook で Azure コマンドレットが使用される場合は、`Connect-AzAccount` アクティビティを使ってチェックポイントが設定されたアクティビティに従う必要があります。 接続操作は、Runbook が中断され、別のワーカーでこのチェックポイントから再起動する必要がある場合に使用されます。

## <a name="handle-runbook-input"></a>Runbook 入力を処理する

Runbook には、Azure portal を通じて Runbook を開始するユーザーから、または現在のものが子として使用されている場合は別の Runbook からの入力が必要です。 たとえば、仮想マシンを作成する Runbook の場合、ユーザーは、Runbook を開始するたびに仮想マシンの名前や他のプロパティなどの情報を提供しなければならない場合があります。

1 つ以上の入力パラメーターを定義することで、Runbook では入力を受け入れます。 ユーザーは、Runbook が開始されるたびに、これらのパラメーターの値を指定します。 ユーザーが Azure portal を使用して Runbook を開始すると、ユーザーは Runbook でサポートされている各入力パラメーターの値を入力するように求められます。

Runbook を作成するときは、Runbook ツールバーの **[入力と出力]** をクリックすることで、入力パラメーターにアクセスできます。 これにより、[入力と出力] コントロールが開き、そこで既存の入力パラメーターを編集したり、 **[入力の追加]** をクリックして新しいものを作成したりできます。

![入力の追加](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

各入力パラメーターは、次の表のプロパティで定義されます。

| プロパティ | 説明 |
|:--- |:--- |
| 名前 | 必須。 パラメーターの名前。 名前は Runbook 内で一意にする必要があります。 名前の先頭には英字を使用する必要があります。また、使用できるのは英字、数字、アンダースコアのみです。 名前に空白文字を含めることはできません。 |
| 説明 |省略可能。 入力パラメーターの目的に関する説明。 |
| Type | 省略可能。 パラメーター値に必要なデータ型です。 Azure Portal では、入力を求められた場合に各パラメーターのデータ型に適したコントロールが提供されます。 サポートされているパラメーター型は String、Int32、Int64、Decimal、Boolean、DateTime、および Object です。 データ型が選択されていない場合、既定値は String になります。|
| Mandatory | 省略可能。 パラメーターの値を指定する必要があるかどうかを指定する設定。 [`yes`] を選択した場合、Runbook の起動時に値を指定する必要があります。 [`no`] を選択した場合、Runbook の起動時に値は求められません。既定値を使用できます。 既定値が定義されていない各必須パラメーターの値を指定しないと、Runbook を開始できません。 |
| Default value | 省略可能。 Runbook の開始時に値が渡されなかった場合に、パラメーターに使用される値。 既定値を設定するには、[`Custom`] を選択します。 既定値を指定しない場合は、[`None`] を選択します。 |

## <a name="handle-runbook-output"></a>Runbook 出力を処理する

グラフィカル作成では、発信リンクを持たない任意のアクティビティによって作成されたデータは、[Runbook の出力](./automation-runbook-output-and-messages.md)に保存されます。 出力は Runbook ジョブと共に保存され、Runbook を子として使用する場合に親 Runbook で使用できます。

## <a name="work-with-powershell-expressions"></a>PowerShell 式を使用する

グラフィカル作成の利点の 1 つは、PowerShell の最小限の知識で Runbook を作成できることです。 ただし、現時点では、特定の[パラメーター値](#use-activities)を設定したり[リンク条件](#use-links-for-workflow)を設定したりするために、PowerShell の若干の知識が必要です。 このセクションでは、PowerShell 式を簡単に紹介します。 PowerShell の詳細については、「 [Windows PowerShell を使用したスクリプト](/powershell/scripting/overview)」を参照してください。

### <a name="use-a-powershell-expression-as-a-data-source"></a>PowerShell 式をデータ ソースとして使用する

PowerShell 式をデータ ソースとして使用し、PowerShell コードの結果で[アクティビティ パラメーター](#use-activities)の値を設定できます。 この式は、簡単な関数を実行する 1 行のコードでも、複雑なロジックを実行する複数行のコードでもかまいません。 変数に割り当てられていないコマンドからの出力は、パラメーター値に出力されます。

たとえば、次のコマンドは現在の日付を出力します。

```powershell-interactive
Get-Date
```

次のコード スニペットでは、現在の日付から文字列を作成して、変数に割り当てます。 コードは変数の内容を出力に送ります。

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

次のコマンドは、現在の日付を評価し、現在の日付が週末か平日かを示す文字列を返します。

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>アクティビティ出力を使用する

前のアクティビティからの出力を Runbook で使用するには、次の構文で `ActivityOutput` 変数を使用します。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

たとえば、仮想マシンの名前を必要とするプロパティを持つアクティビティを作成できます。 この場合、Runbook は次の式を使用できます。

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

プロパティが、名前だけでなく仮想マシン オブジェクトを必要とする場合、Runbook は次の構文を使用してオブジェクト全体を返します。

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook は、次のように、より複雑な式でアクティビティの出力を使用できます。 この式では、テキストが仮想マシン名に連結されます。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>値を比較する

値を比較する場合、または値が指定したパターンに一致するかどうかを判定する場合は、 [比較演算子](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) を使います。 比較では、True または False のいずれかの値が返されます。

たとえば、次の条件では、`Get-AzureVM` という名前のアクティビティの仮想マシンが現在、停止されているかどうかを判断します。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

次の条件は、同じ仮想マシンが停止済み以外の状態かどうかを判断します。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

`-and` や `-or` などの[論理演算子](/powershell/module/microsoft.powershell.core/about/about_logical_operators)を使用して、Runbook で複数の条件を結合できます。 たとえば、次の条件では、前の例の仮想マシンが停止済みと停止中のどちらの状態であるかを確認します。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>ハッシュテーブルを使用する

[ハッシュテーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)は、値のセットを返すのに便利な名前と値のペアです。 ハッシュテーブルはディクショナリと呼ばれる場合もあります。 一部のアクティビティのプロパティでは、単純な値ではなくハッシュテーブルが想定されています。

ハッシュテーブルは次の構文を使って作成します。 これには任意の数のエントリを含めることができますが、それぞれは名前と値で定義されています。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

たとえば、次の式は、インターネット検索用の値のハッシュテーブルが想定されているアクティビティ パラメーターのデータ ソースとして使用されるハッシュテーブルを作成します。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

次の例では、`Get Twitter Connection` というアクティビティからの出力を使用して、ハッシュテーブルを設定します。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Azure リソースに対して認証する

Azure リソースを管理する、Azure Automation の Runbook は、Azure に対する認証が必要になります。 [実行アカウント](./automation-security-overview.md) (サービス プリンシパルとも呼ばれる) は、サブスクリプション内の Azure Resource Manager リソースにアクセスするために Automation Runbook で使用される既定のメカニズムです。 この機能をグラフィカル Runbook に追加するには、PowerShell の [Get-AutomationConnection](/system-center/sma/manage-global-assets) コマンドレットを使用する `AzureRunAsConnection` 接続資産をキャンバスに追加します。 また、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを追加することもできます。 このシナリオを次の例で示します。

![Run As Authentication アクティビティ](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection` アクティビティ (`Get-AutomationConnection`) は、`AzureRunAsConnection` という名前の定数値のデータ ソースで構成されます。

![Run As Connection 構成](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

次のアクティビティ `Connect-AzAccount` では、Runbook で使用する認証済み実行アカウントを追加します。

![Connect-AzAccount パラメーター セット](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 これらの別名は、グラフィカルな Runbook では使用できないことに注意してください。 グラフィカルな Runbook で使用できるのは、`Connect-AzAccount` 自体のみです。

**APPLICATIONID**、**CERTIFICATETHUMBPRINT**、および **TENANTID** のパラメーター フィールドでは、フィールド パスに対してプロパティの名前を指定します。これは、アクティビティによって、複数のプロパティを持つオブジェクトが出力されるためです。 指定しなかった場合は、Runbook を実行するときに、認証の試行中に失敗します。 これは、実行アカウントで Runbook を認証するために最低限必要なことです。

一部のサブスクライバーは、Azure クラシック デプロイを管理するために、または Azure Resource Manager リソース用に、[Azure AD ユーザー アカウント](./shared-resources/credentials.md)を使用して Automation アカウントを作成します。 これらのサブスクライバーに対して下位互換性を維持するために、Runbook で使用する認証メカニズムは、[資格情報資産](./shared-resources/credentials.md)を使用する `Add-AzureAccount` コマンドレットです。 資産は、Azure アカウントへのアクセス権を持つ Active Directory ユーザーを表します。

グラフィカル Runbook のこの機能を有効にするには、キャンバスに資格情報資産を追加し、その後に、資格情報資産を入力に使用する `Add-AzureAccount` アクティビティを続けます。 次の例を参照してください。

![認証アクティビティ](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook は、その開始時と、各チェックポイントの後に認証する必要があります。 したがって、`Checkpoint-Workflow` アクティビティの後に `Add-AzureAccount` アクティビティを使用する必要があります。 追加の資格情報アクティビティを使用する必要はありません。

![アクティビティの出力](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>グラフィック Runbook をエクスポートする

グラフィカル Runbook は、発行済みのバージョンのみエクスポートできます。 Runbook がまだ発行されていない場合、 **[エクスポート]** ボタンは無効になっています。 **[エクスポート]** ボタンをクリックすると、Runbook がローカル コンピューターにダウンロードされます。 ファイル名は、Runbook の名前に **.graphrunbook** 拡張子が付いたものになります。

## <a name="import-a-graphical-runbook"></a>グラフィカル Runbook をインポートする

グラフィカル Runbook ファイルまたはグラフィカル PowerShell ワークフロー Runbook ファイルは、Runbook の追加時に **[インポート]** オプションを選択するとインポートできます。 インポートするファイルを選択するときは、同じ名前を保持することも、新しい名前を指定することもできます。 **[Runbook の種類]** フィールドには、選択したファイルを評価した後に Runbook の種類が表示されます。 正しくない別の種類を選択しようとすると、グラフィカル エディターは、競合の可能性があり、変換中に構文エラーが発生する可能性があることを示すメッセージを表示します。

![Runbook のインポート](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>グラフィック Runbook をテストする

Azure Automation の各グラフィカル Runbook には、ドラフト バージョンと発行済みバージョンがあります。 実行できるのは発行済みバージョンのみであり、編集できるのはドラフト バージョンのみです。 ドラフト バージョンを変更しても発行バージョンに影響はありません。 ドラフト バージョンを使用する準備ができたら、それを発行します。これにより、現在の発行済みバージョンがドラフト バージョンによって上書きされます。

発行済みバージョンを未変更のままにして、Azure portal で Runbook のドラフト バージョンをテストできます。 別の方法として、新しい Runbook を発行前にテストすることもできます。それにより、バージョンを置き換える前にその Runbook が正しく動作することを確認できます。 Runbook のテストではドラフト バージョンが実行され、行われるすべてのアクションが完了することが確認されます。 ジョブ履歴は作成されませんが、テスト出力ペインに出力が表示されます。

グラフィカル Runbook のテスト コントロールを開くには、編集対象の Runbook を開いてから **[テスト ペイン]** ボタンをクリックします。 テスト コントロールによって入力パラメーターの入力が求められます。 **[開始]** をクリックすると、Runbook を開始できます。

## <a name="publish-a-graphical-runbook"></a>グラフィック Runbook を発行する

グラフィカル Runbook を発行するには、編集対象の Runbook を開いてから **[発行]** ボタンをクリックします。 考えられる Runbook のステータスは次のとおりです。

* 新規 -- Runbook はまだ発行されていません。 
* 発行済み -- Runbook は発行されています。
* 編集中 -- Runbook は、発行後に編集されており、ドラフトと発行済みのバージョンが異なります。

![Runbook のステータス](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

発行済みバージョンの Runbook に戻すことを選択できます。 この操作により、Runbook が最後に発行されから行われた変更はすべて破棄されます。 Runbook のドラフト バージョンは、発行済みバージョンに置き換えられます。

## <a name="next-steps"></a>次のステップ

* グラフィカル Runbook の使用を開始するには、「[チュートリアル: グラフィカル Runbook を作成する](learn/automation-tutorial-runbook-graphical.md)」を参照してください。
* Runbook の種類と利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。
* Automation の実行アカウントを使った認証方法を理解するには、「[実行アカウント](automation-security-overview.md#run-as-account)」を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation/#automation)」をご覧ください。
