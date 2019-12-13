---
title: Azure Automation でのグラフィカル作成
description: グラフィカル作成では、コードを操作せずに Azure Automation の Runbook を作成することができます。 この記事では、グラフィカル作成の概要と、グラフィカル Runbook の作成を開始するうえで必要なすべての詳細情報を示します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82a06510bd9d1e0de2b38260773cb4848156bf12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850297"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure Automation でのグラフィカル作成

グラフィカル作成では、基になる Windows PowerShell または PowerShell Workflow の複雑なコードを使用しなくても、Azure Automation の Runbook を作成することができます。 コマンドレットや Runbook のライブラリからキャンバスにアクティビティを追加し、そのアクティビティどうしをリンクして、ワークフローを形成するように構成します。 これまでに System Center Orchestrator または Service Management Automation (SMA) で作業したことがあれば、これは見覚えがあるはずです。

この記事では、グラフィカル作成の概要と、グラフィカル Runbook の作成を開始するうえで必要な概念について説明します。

## <a name="graphical-runbooks"></a>グラフィック Runbook

Azure Automation の Runbook はすべて Windows PowerShell ワークフローです。 グラフィカル Runbook とグラフィカル PowerShell ワークフロー Runbook は、Automation ワーカーによって実行される PowerShell コードを生成しますが、ユーザーがそのコードを表示したり、直接変更したりすることはできません。 グラフィカル Runbook はグラフィカル PowerShell ワークフロー Runbook に変換でき、その逆も可能ですが、これらをテキスト形式の Runbook に変換することはできません。 既存のテキスト形式の Runbook は、グラフィカル エディターにインポートできません。

## <a name="overview-of-graphical-editor"></a>グラフィカル エディターの概要

Azure ポータルでグラフィカル Runbook を作成または編集することで、グラフィカル エディターを開くことができます。

![グラフィカル ワークスペース](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

次のセクションでは、グラフィカル エディターのコントロールについて説明します。

### <a name="canvas"></a>キャンバス

キャンバスは Runbook を設計する場所です。 ライブラリ コントロールのノードから Runbook にアクティビティを追加し、リンクで接続して Runbook のロジックを定義します。

キャンバスの下部にあるコントロールを使用して、拡大または縮小できます。

### <a name="library-control"></a>ライブラリ コントロール

ライブラリ コントロールは、Runbook に追加する [アクティビティ](#activities) を選択する場所です。 アクティビティをキャンバスに追加し、そこで他のアクティビティに接続します。 これには、以下の表で説明する次の 4 つのセクションが含まれます。

| Section | Description |
|:--- |:--- |
| コマンドレット |Runbook で使用できるすべてのコマンドレットが含まれています。 コマンドレットはモジュールごとに整理されます。 オートメーション アカウントにインストールしたモジュールをすべて使用できます。 |
| Runbooks |お使いの Automation アカウントの Runbook が含まれています。 これらの Runbook は、子 Runbook として使用するためにキャンバスに追加できます。 表示されるのは、編集中の Runbook と同じコアの種類の Runbook のみです。つまり、グラフィカル Runbook の場合は、PowerShell ベースの Runbook のみが表示されるのに対し、グラフィカル PowerShell ワークフロー Runbook の場合は、PowerShell ワークフロー ベースの Runbook のみが表示されます。 |
| アセット |Runbook で使用できるオートメーション アカウントに [オートメーション資産](/previous-versions/azure/dn939988(v=azure.100)) が含まれています。 Runbook に資産を追加すると、選択した資産を取得するワークフロー アクティビティが追加されます。 変数資産の場合は、変数を取得するアクティビティを追加するか、変数を設定するアクティビティを追加するかを選択できます。 |
| Runbook コントロール |現在の Runbook で使用できる Runbook コントロール アクティビティが含まれています。 *ジャンクション* では複数の入力を受け取り、すべてが完了するまで待機してから、ワークフローを続行します。 *コード* アクティビティは、グラフィカル Runbook の種類に応じて、PowerShell または PowerShell Workflow の 1 行以上のコードを実行します。 このアクティビティは、カスタム コードや、他のアクティビティでの実現が難しい機能に使用できます。 |

### <a name="configuration-control"></a>構成コントロール

構成コントロールは、キャンバスで選択したオブジェクトの詳細を指定する場所です。 このコントロールで使用できるプロパティは、選択したオブジェクトの種類によって異なります。 構成コントロールでオプションを選択すると、追加情報を提供するための追加のブレードが開きます。

### <a name="test-control"></a>テスト コントロール

グラフィカル エディターが初めて起動された場合、テスト コントロールは表示されません。 対話形式で [グラフィカル Runbook をテスト](#graphical-runbook-procedures)する際に開きます。

## <a name="graphical-runbook-procedures"></a>グラフィカル Runbook の手順

### <a name="exporting-and-importing-a-graphical-runbook"></a>グラフィカル Runbook のエクスポートとインポート

グラフィカル Runbook は、発行済みのバージョンのみエクスポートできます。 Runbook がまだ発行されていない場合、 **[エクスポート]** ボタンは無効になります。 **[エクスポート]** ボタンをクリックすると、Runbook がローカル コンピューターにダウンロードされます。 ファイル名は、Runbook の名前に *graphrunbook* 拡張子が付いたものになります。

グラフィカル Runbook ファイルまたはグラフィカル PowerShell ワークフロー Runbook ファイルは、Runbook の追加時に **[インポート]** オプションを選択するとインポートできます。 インポートするファイルを選択したときは、同じ**名前**を保持するか、新しい名前を指定できます。 [Runbook の種類] フィールドには、選択したファイルが評価された後に Runbook の種類が表示されます。また、適切ではない別の種類を選択しようとすると、競合する可能性があることを示すメッセージが表示され、変換中に構文エラーが発生することがあります。

![Runbook のインポート](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>グラフィカル Runbook のテスト

発行バージョンの Runbook を変更せずに、ドラフト バージョンの Runbook を Azure ポータルでテストしたり、新しい Runbook を発行前にテストしたりすることができます。 これにより、発行バージョンを置き換える前に、Runbook が正常に機能していることを確認することができます。 Runbook のテスト時に、ドラフトの Runbook が実行され、行われたすべての操作が完了します。 ジョブ履歴は作成されませんが、出力がテスト出力ペインに表示されます。

Runbook のテスト コントロールを開くには、編集対象の Runbook を開いてから **[テスト ペイン]** ボタンをクリックします。

テスト コントロールは入力パラメーターに関するプロンプトを出します。Runbook は、 **[開始]** ボタンをクリックすることで開始できます。

### <a name="publishing-a-graphical-runbook"></a>グラフィカル Runbook の発行

Azure Automation の各 Runbook には、ドラフト バージョンと発行バージョンがあります。 実行できるのは発行バージョンのみで、編集できるのはドラフト バージョンのみです。 ドラフト バージョンを変更しても発行バージョンに影響はありません。 ドラフト バージョンを使用する準備ができたら、それを発行します。これにより、発行バージョンがドラフト バージョンで上書きされます。

グラフィカル Runbook は、編集対象の Runbook を開いてから **[発行]** ボタンをクリックすることで発行できます。

Runbook がまだ発行されていない場合、状態は **[新規]** です。 発行されている場合、状態は **[発行済み]** です。 Runbook を発行後に編集する際に、ドラフトと発行のバージョンが異なる場合、Runbook の状態は **[編集]** になります。

![Runbook のステータス](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

また、発行バージョンの Runbook に戻すこともできます。 これにより、Runbook が最後に発行されてから加えられた変更が破棄され、ドラフト バージョンの Runbook が発行バージョンに置き換えられます。

## <a name="activities"></a>Activities

アクティビティは Runbook の構成要素です。 アクティビティは、PowerShell コマンドレット、子 Runbook、またはワークフロー アクティビティである場合があります。 Runbook にアクティビティを追加する場合は、ライブラリ　コントロールを右クリックして、 **[キャンバスに追加]** を選択します。 その後、アクティビティをクリックしてドラッグし、キャンバス上の任意の場所に配置できます。 キャンバス上のアクティビティの場所が、Runbook の操作に影響することは決してありません。 Runbook をレイアウトできますが、その操作を視覚化するために最適なレイアウトを見つけてください。

![キャンバスに追加](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

構成ブレードでプロパティとパラメーターを構成するには、キャンバス上でアクティビティを選択します。 アクティビティの **ラベル** は、わかりやすいものに変更することができます。 元のコマンドレットは引き続き実行されるため、グラフィカル エディターで使用される表示名を変更するだけです。 ラベルは、Runbook 内で一意である必要があります。

### <a name="parameter-sets"></a>パラメーター セット

パラメーター セットは、特定のコマンドレットの値を受け入れる必須および省略可能なパラメーターを定義します。 すべてのコマンドレットには 1 つ以上のパラメーター セットがあり、複数存在する場合があります。 コマンドレットに複数のパラメーター セットがある場合は、使用するものを選択してからパラメーターを構成する必要があります。 構成可能なパラメーターは、選択するパラメーター セットによって異なります。 アクティビティで使用されるパラメーター セットは、 **[パラメーター セット]** を選択してから別のセットを選択することで変更できます。 この場合、構成したパラメーター値はすべて失われます。

次の例では、Get-AzureRmVM コマンドレットに 3 つのパラメーター セットがあります。 パラメーター セットのいずれかを選択するまでは、パラメーター値を構成することはできません。 ListVirtualMachineInResourceGroupParamSet パラメーター セットは、リソース グループ内のすべての仮想マシンを返すためのものであり、1 つの省略可能なパラメーターがあります。 **GetVirtualMachineInResourceGroupParamSet** は、返される仮想マシンを指定するためのものであり、2 つの必須パラメーターと 1 つの省略可能なパラメーターがあります。

![パラメーター セット](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>パラメーターの値

パラメーターの値を指定する場合は、データ ソースを選択して値の指定方法を決定します。 特定のパラメーターに使用できるデータ ソースは、そのパラメーターの有効な値によって異なります。 たとえば、null 値が許可されていないパラメーターに対するオプションとして Null を使用することはできません。

| データ ソース | Description |
|:--- |:--- |
| 定数値 |パラメーターの値を入力します。 これを使用できるのは、Int32、Int64、String、Boolean、DateTime、Switch というデータ型の場合のみです。 |
| アクティビティの出力 |ワークフローの現在のアクティビティより前のアクティビティからの出力です。 有効なすべてのアクティビティが一覧表示されます。 パラメーター値にその出力を使用するアクティビティだけを選択します。 アクティビティが複数のプロパティを持つオブジェクトを出力する場合は、アクティビティを選択してからプロパティの名前を入力できます。 |
| Runbook の入力 |Runbook の入力パラメーターをアクティビティ パラメーターへの入力として選択します。 |
| 変数資産 |Automation の変数を入力として選択します。 |
| 資格情報資産 |Automation の資格情報を入力として選択します。 |
| 証明書資産 |Automation の証明書を入力として選択します。 |
| 接続資産 |Automation の接続を入力として選択します。 |
| PowerShell 式 |簡単な [PowerShell](#powershell-expressions)式を指定します。 式は、アクティビティとパラメーター値に使用される結果の前に評価されます。 変数を使用すれば、アクティビティの出力または Runbook の入力パラメーターを参照することができます。 |
| 未構成 |以前に構成された値をクリアします。 |

#### <a name="optional-additional-parameters"></a>省略可能な追加パラメーター

すべてのコマンドレットで、追加のパラメーターを指定することができます。 これらは、PowerShell の共通パラメーターまたはその他のカスタム パラメーターです。 PowerShell 構文を使用してパラメーターを指定できるテキスト ボックスが表示されます。 たとえば、**Verbose** 共通パラメーターを使用する場合は、 **"-Verbose: $True"** を指定します。

### <a name="retry-activity"></a>再試行アクティビティ

**再試行動作** を使用すると、ループのように、特定の条件を満たすまで、アクティビティを複数回実行できます。 この機能は、複数回実行する必要があるアクティビティ、エラーが発生しやすく、成功までに複数回の試行が必要になる可能性があるアクティビティ、またはアクティビティの出力情報が有効なデータかどうかをテストするアクティビティに使用できます。

アクティビティの再試行を有効にするときに、遅延と条件を設定できます。 遅延とは、アクティビティを次回実行するまでに Runbook が待機する時間 (秒単位または分単位) です。 遅延を指定しない場合、アクティビティの完了後すぐに再実行されます。

![アクティビティ再試行の遅延](media/automation-graphical-authoring-intro/retry-delay.png)

再試行の条件とは、アクティビティの実行後のたびに評価される PowerShell 式です。 式が True に解決されると、アクティビティは再実行されます。 式が False に解決されると、アクティビティは再実行されず、Runbook は次のアクティビティに移ります。

![アクティビティ再試行の遅延](media/automation-graphical-authoring-intro/retry-condition.png)

再試行の条件は、アクティビティの再試行に関する情報にアクセスできる $RetryData という変数を使用できます。 この変数には、次の表のプロパティがあります。

| プロパティ | Description |
|:--- |:--- |
| NumberOfAttempts |アクティビティが実行された回数。 |
| Output |最後に実行されたアクティビティの出力。 |
| TotalDuration |初回のアクティビティが開始されてからの経過時間。 |
| StartedAt |初回のアクティビティが開始された時刻 (UTC 形式)。 |

次に、アクティビティの再試行条件の例を示します。

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

アクティビティの再試行条件を構成すると、そのアクティビティには、通知用に 2 つの視覚的な合図が含まれます。 1 つはアクティビティ内に表示され、もう 1 つは、アクティビティの構成を確認するときに表示されます。

![Activity Retry Visual Indicators](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>ワークフロー スクリプト コントロール

コード コントロールは特別なアクティビティで、機能を提供する (そうしないと使用できない場合がある) ために、作成するグラフィカル Runbook の種類に応じて、PowerShell スクリプトか PowerShell Workflow スクリプトを受け入れます。 これはパラメーターを受け入れることはできませんが、アクティビティの出力と Runbook の入力パラメーターで変数を使用できます。 発信リンクがない (Runbook の出力に追加される) 場合を除き、アクティビティの出力はデータバスに追加されます。

たとえば、次のコードでは $NumberOfDays という Runbook 入力変数を使用して、日付の計算を実行します。 次に、計算した日時が出力として送信され、Runbook の後続のアクティビティで使用されます。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>リンクとワークフロー

グラフィカル Runbook の **リンク** は 2 つのアクティビティを接続します。 キャンバスでは、ソース アクティビティから接続先アクティビティを指す矢印として表示されます。 アクティビティは矢印の方向に実行され、ソース アクティビティが完了すると接続先アクティビティが開始されます。

### <a name="create-a-link"></a>リンクの作成

ソース アクティビティを選択し、図形の下部にある円をクリックして、2 つのアクティビティ間のリンクを作成します。 接続先アクティビティまで矢印をドラッグして離します。

![リンクの作成](media/automation-graphical-authoring-intro/create-link-revised20165.png)

リンクを選択して、構成ブレードでそのプロパティを構成します。 これには、次の表で説明されているリンクの種類が含まれます。

| リンクの種類 | Description |
|:--- |:--- |
| パイプライン |ソース アクティビティからのオブジェクト出力ごとに 1 回、接続先アクティビティが実行されます。 ソース アクティビティからの出力がない場合、接続先アクティビティは実行されません。 ソース アクティビティからの出力はオブジェクトとして使用できます。 |
| シーケンス |接続先アクティビティは 1 回だけ実行されます。 ソース アクティビティからのオブジェクトの配列を受け取ります。 ソース アクティビティからの出力は、オブジェクトの配列として使用できます。 |

### <a name="starting-activity"></a>アクティビティの開始

グラフィカル Runbook は、受信リンクのないすべてのアクティビティから開始します。 多くの場合、Runbook の開始アクティビティとして動作するアクティビティは 1 つだけです。 複数のアクティビティに受信リンクがない場合、Runbook は並列で実行して開始します。 その後、リンクに従って、各アクティビティの完了時に別のアクティビティを実行します。

### <a name="conditions"></a>条件

リンクに条件を指定すると、その条件が true に解決された場合にのみ、接続先アクティビティが実行されます。 ソース アクティビティからの出力を取得する場合、通常は条件に $ActivityOutput 変数を使用します

パイプライン リンクの場合は、1 つのオブジェクトに対して条件を指定します。この条件は、ソース アクティビティによってオブジェクトの出力ごとに評価されます。 その後、条件を満たす各オブジェクトに対して、接続先アクティビティが実行されます。 たとえば、Get-AzureRmVm のソース アクティビティでは、条件付きパイプライン リンクに次の構文を使用すると、*Group1* という名前のリソース グループ内の仮想マシンのみを取得できます。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

シーケンス リンクの場合は、ソース アクティビティからのオブジェクト出力をすべて含む 1 つの配列が返されるため、条件は一度だけ評価されます。 そのため、シーケンス リンクをパイプライン リンクのようにフィルター処理に使用することはできませんが、次のアクティビティが実行されるかどうかは簡単に判別されます。 たとえば、この Start VM Runbook では次のアクティビティのセットを使用します。

![Conditional Link with Sequences](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

3 つの異なるシーケンス リンクがあり、実行する適切なアクション (1 つの VM を開始する、リソース グループ内のすべての VM を開始する、サブスクリプション内のすべての VM を開始する) を判別するために、VM 名とリソース グループ名を表す 2 つの Runbook 入力パラメーターに値が指定されたことを検証しています。 Connect to Azure と Get single VM の間のシーケンス リンクの場合、条件のロジックは次のとおりです。

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

条件付きリンクを使用する場合は、その分岐でソース アクティビティから他のアクティビティまでの使用可能なデータが、条件によってフィルター処理されます。 アクティビティが複数のリンクに対するソースの場合、各分岐のアクティビティで使用できるデータは、その分岐に接続するリンクの条件によって異なります。

たとえば、以下の Runbook の **Start-AzureRmVm** アクティビティはすべての仮想マシンを開始します。 このアクティビティには 2 つの条件付きリンクがあります。 最初の条件付きリンクでは、式 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true* を使用して、Start-AzureRmVm アクティビティが正常に完了した場合にフィルター処理します。 2 つ目の条件付きリンクでは、式 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true* を使用して、Start-AzureRmVm アクティビティが仮想マシンを開始できなかった場合にフィルター処理します。

![条件付きリンクの例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

最初のリンクに従って、Get-azurevm からのアクティビティ出力を使用するアクティビティでは、Get-AzureVM の実行時に開始された仮想マシンのみを取得します。 2 番目のリンクに従うアクティビティでは、Get-AzureVM の実行時に停止された仮想マシンのみを取得します。 3 番目のリンクに従うアクティビティでは、実行状態に関係なく、すべての仮想マシンを取得します。

### <a name="junctions"></a>接合

接合は特別なアクティビティで、受信分岐がすべて完了するまで待機します。 これにより、複数のアクティビティを並列で実行し、次に進む前にすべて完了したことを確認することができます。

接合できる受信リンクの数に制限はありませんが、パイプラインにすることができるのはこれらのリンクのうち 1 つだけです。 受信シーケンス リンクの数に制限はありません。 複数の受信パイプライン リンクを受け入れる接合点を作成し、Runbook を保存することはできますが、実行した場合は失敗します。

次の例は、一連の仮想マシンに同時に修正プログラムをダウンロードする間に、それらのマシンを開始する Runbook の一部です。 接合点を使用して、Runbook を続行する前に両方のプロセスが完了したことを確認します。

![ジャンクション](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>サイクル

サイクルとは、接続先アクティビティがリンクに従ってそのソース アクティビティ、または最終的にソースに戻る別のアクティビティに戻ることです。 現在、グラフィカル作成ではサイクルを使用することはできません。 Runbook にサイクルがある場合、保存は正常に行われますが、実行時にエラーが発生します。

![サイクル](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>アクティビティ間のデータの共有

発信リンクがあるアクティビティで出力されるデータは、Runbook の *データバス* に書き込まれます。 Runbook のすべてのアクティビティはデータバスのデータを使用して、パラメーターの値を設定したり、スクリプト コードに含めたりすることができます。 アクティビティは、ワークフロー内の前のアクティビティの出力にアクセスできます。

データバスにデータが書き込まれる方法は、アクティビティのリンクの種類によって異なります。 **パイプライン**の場合、データは複数のオブジェクトとして出力されます。 **シーケンス** リンクの場合、データは配列として出力されます。 値が 1 つだけある場合、1 つの要素配列として出力されます。

2 つの方法のいずれかを使用して、データバス上のデータにアクセスできます。 1 つは、 **アクティビティの出力** データ ソースを使用して、別のアクティビティのパラメーターを設定する方法です。 出力がオブジェクトの場合は、1 つのプロパティを指定できます。

![アクティビティの出力](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

**PowerShell 式**データ ソースでアクティビティの出力を取得することも、ActivityOutput 変数を使用して**ワークフロー スクリプト** アクティビティから取得することもできます。 出力がオブジェクトの場合は、1 つのプロパティを指定できます。 ActivityOutput 変数には次の構文を使用します。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>チェックポイント

任意のアクティビティで [Runbook チェックポイント](automation-powershell-workflow.md#checkpoints) を選択することで、グラフィカル PowerShell ワークフロー Runbook の *チェックポイント* を設定できます。 この場合、アクティビティの実行後にチェックポイントが設定されます。

![チェックポイント](media/automation-graphical-authoring-intro/set-checkpoint.png)

チェックポイントは、グラフィカル PowerShell ワークフロー Runbook のみで有効で、グラフィカル Runbook では使用できません。 Runbook で Azure コマンドレットを使用する場合、Runbook が一時停止され、別のワーカーでこのチェックポイントから再起動するときは、Connect-AzureRmAccount を使用してチェックポイントを設定したアクティビティに従います。

## <a name="authenticating-to-azure-resources"></a>Azure リソースの認証

Azure リソースを管理する、Azure Automation の Runbook は、Azure に対する認証が必要になります。 [実行アカウント](automation-create-runas-account.md) (サービス プリンシパルとも呼ばれます) は、サブスクリプション内の Azure Resource Manager リソースに Automation Runbook でアクセスするための既定の方法です。 この機能をグラフィカル Runbook に追加するには、**AzureRunAsConnection** 接続資産を追加します。これは、PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) コマンドレットと、キャンバスに対する [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) コマンドレットを使用しています。 これを次の例に示します。

![Run As Authentication アクティビティ](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Get Run As Connection アクティビティ (つまり Get-AutomationConnection) は、AzureRunAsConnection という名前の定数値データ ソースで構成されています。

![Run As Connection 構成](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

次のアクティビティ Connect-AzureRmAccount は、Runbook で使用する認証済み実行アカウントを追加します｡

![Connect-AzureRmAccount パラメーター セット](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> これで、**Connect-AzureRmAccount** のエイリアスは **Add-AzureRMAccount** に設定されました。 ライブラリ項目を検索して **Connect-AzureRMAccount** が表示されない場合は、**Add-AzureRmAccount** を使用するか、Automation アカウントでモジュールを更新できます。

**APPLICATIONID**、**CERTIFICATETHUMBPRINT**、**TENANTID** パラメーターでは、[フィールド パス] にプロパティの名前を指定する必要があります。これは、アクティビティが複数のプロパティを持つオブジェクトを出力するためです。 指定しなかった場合は、Runbook を実行するときに、認証しようとして失敗します。 これは、実行アカウントで Runbook を認証するために最低限必要なことです。

Azure クラシック デプロイメントまたは Azure Resource Manager のリソースを管理するために [Azure AD ユーザー アカウント](automation-create-aduser-account.md)を使用して Automation アカウントを作成したサブスクライバーのために下位互換性を維持するには、認証方法として、Azure アカウントへのアクセス権を持つ Active Directory ユーザーを表す[資格情報資産](automation-credentials.md)を指定した Add-AzureAccount コマンドレットがあります。

グラフィカル Runbook にこの機能を追加するには、キャンバスに資格情報資産を追加します。この後に Add-AzureAccount アクティビティが続きます。 Add-AzureAccount では、資格情報アクティビティを使用して入力します。 これを次の例に示します。

![認証アクティビティ](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook の開始時と各チェックポイント後に認証を行う必要があります。 つまり、すべての Checkpoint-Workflow アクティビティの後に Add-AzureAccount アクティビティを追加します。 資格情報アクティビティは同じものを使用できるため、追加する必要はありません。

![アクティビティの出力](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook の入力と出力

### <a name="runbook-input"></a>Runbook の入力

Runbook では、Azure ポータルで Runbook を開始する場合はユーザーから、現在の Runbook が子として使用されている場合は別の Runbook からの入力が必要になる可能性があります。
たとえば、仮想マシンを作成する Runbook がある場合、Runbook を開始するたびに仮想マシンの名前や他のプロパティなどの情報提供が必要になることがあります。

Runbook の入力を受け入れる場合は、1 つ以上の入力パラメーターを定義します。 Runbook が開始されるたびに、これらのパラメーターの値を指定します。 Azure Portal で Runbook を開始すると、Runbook の入力パラメーターのそれぞれの値を指定するように求められます。

Runbook の入力パラメーターには、Runbook ツールバーの **[入力と出力]** ボタンをクリックしてアクセスできます。

これにより、 **[入力と出力]** コントロールが開き、そこで既存の入力パラメーターを編集したり、 **[入力の追加]** をクリックして新しいものを作成したりできます。

![入力の追加](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

各入力パラメーターは、次の表のプロパティで定義されます。

| プロパティ | Description |
|:--- |:--- |
| Name |パラメーターの一意の名前です。 これに含めることができるのは英数字のみです。スペースを含めることはできません。 |
| Description |入力パラメーターのオプションの説明です。 |
| 種類 |パラメーター値に必要なデータ型です。 Azure Portal では、入力を求められた場合に各パラメーターのデータ型に適したコントロールが提供されます。 |
| Mandatory |パラメーターの値を指定する必要があるかどうかを示します。 既定値が定義されていない各必須パラメーターの値を指定しないと、Runbook を開始できません。 |
| Default value |パラメーターに値が指定されていない場合は、使用する値を指定します。 Null または特定の値を指定できます。 |

### <a name="runbook-output"></a>Runbook の出力

発信リンクがない任意のアクティビティによって作成されたデータは、[Runbook の出力](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)に保存されます。 出力は Runbook ジョブと共に保存され、Runbook を子として使用する場合に親 Runbook で使用できます。

## <a name="powershell-expressions"></a>PowerShell の式

グラフィカル作成の利点の 1 つは、PowerShell の最小限の知識があれば Runbook を作成できることです。 ただし、現時点では、特定の[パラメーター値](#activities)の設定および[リンク条件](#links-and-workflow)の設定に関して PowerShell の若干の知識が必要です。 ここでは、PowerShell の式のことをよく知らないユーザーのためにその概要について簡単に説明します。 PowerShell の詳細については、「 [Windows PowerShell を使用したスクリプト](https://technet.microsoft.com/library/bb978526.aspx)」を参照してください。

### <a name="powershell-expression-data-source"></a>PowerShell 式のデータ ソース
PowerShell 式をデータ ソースとして使用し、 [アクティビティ パラメーター](#activities) の値に PowerShell コードの結果を設定できます。 簡単な関数を実行する 1 行のコードでも、複雑なロジックを実行する複数行のコードでもかまいません。 変数に割り当てられていないコマンドからの出力は、パラメーター値に出力されます。

たとえば、次のコマンドは現在の日付を出力します。

たとえば、次のコマンドは現在の日付を出力します。

```powershell-interactive
Get-Date
```

次のコマンドは、現在の日付から文字列を作成し、変数に割り当てます。 その後、変数の内容は出力に送られます。

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

### <a name="activity-output"></a>アクティビティの出力

前のアクティビティからの出力を Runbook で使用するには、次の構文で $ActivityOutput 変数を使用します。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

たとえば、仮想マシンの名前を必要とするプロパティを持つアクティビティがある場合、次の式を使用します。

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

プロパティだけでなく仮想マシン オブジェクトを必要とするプロパティの場合は、次の構文を使用してオブジェクト全体を返します。

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

テキストを仮想マシン名に連結する次のようなさらに複雑な式で、アクティビティの出力を使用することもできます。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>条件

値を比較する場合、または値が指定したパターンに一致するかどうかを判定する場合は、 [比較演算子](https://technet.microsoft.com/library/hh847759.aspx) を使います。 比較からは、値 $true または $false が返ります。

たとえば、次の条件は、*Get-AzureVM* という名前のアクティビティの仮想マシンが現在 "*停止済み*" かどうかを判定します。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

次の条件は、同じ仮想マシンが *停止済み*以外の状態かどうかを調べます。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

**-and** や **-or** などの[論理演算子](https://technet.microsoft.com/library/hh847789.aspx)を使用して、複数の条件を結合できます。 たとえば、次の条件は、前の例と同じ仮想マシンの状態が "*停止済み*" または "*停止中*" かどうかをチェックします。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>ハッシュテーブル

[ハッシュテーブル](https://technet.microsoft.com/library/hh847780.aspx) は、値のセットを返すのに便利な名前と値のペアです。 一部のアクティビティのプロパティでは、単純な値ではなくハッシュテーブルが想定されている場合があります。 ハッシュテーブルはディクショナリと考えることもできます。

ハッシュテーブルは次の構文で作成します。 ハッシュテーブルは任意の数のエントリを含むことができますが、エントリごとに名前と値によって定義されます。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

たとえば、次の式は、インターネット検索用の値のハッシュテーブルを期待するアクティビティ パラメーターに対するデータ ソースで使用されるハッシュテーブルを作成します。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

次の例は、 *Get Twitter Connection* という名前のアクティビティからの出力を使用して、ハッシュテーブルを設定します。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>次の手順

* PowerShell ワークフロー Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
* グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)
* Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)
* Automation の実行アカウントを使った認証方法を理解するには、 [Azure 実行アカウントの構成](automation-sec-configure-azure-runas-account.md)
