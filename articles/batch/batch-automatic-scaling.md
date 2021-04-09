---
title: Azure Batch プール内のコンピューティング ノードの自動スケール
description: クラウド プールで自動スケールを有効にして、プール内のコンピューティング ノードの数を動的に調整します。
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 06f717e7c3ab8285b494f89c39838af6b0d96c8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381428"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Batch プール内のコンピューティング ノードをスケーリングするための自動式を作成する

Azure Batch では定義したパラメーターに基づいてプールが自動的にスケールされるため、時間とコストを節約できます。 自動スケーリングにより、Batch ではタスクの需要が増えるとノードが動的にプールに追加され、タスクの需要が減ると計算ノードが削除されます。

計算ノードのプールで自動スケーリングを有効にするには、定義した *自動スケーリングの数式* をプールに関連付けます。 Batch サービスでは、自動スケーリングの数式を使用して、ワークロードを実行するために必要なノードの数が決定されます。 これらのノードは、専用ノードまたは[優先順位の低いノード](batch-low-pri-vms.md)のいずれかです。 その後、Batch では、サービス メトリック データが定期的に確認され、それを使用して、式に基づいて定義した間隔でプール内のノード数が調整されます。

自動スケーリングは、プールの作成時に有効にするか、既存のプールに適用できます。 Batch では、数式をプールに割り当てる前に数式の評価を行うことができるほか、自動スケールの実行状態を監視することができます。 自動スケーリングによってプールを構成すると、後で数式に変更を加えることができます。

> [!IMPORTANT]
> Batch アカウントを作成する際に、[プール割り当てモード](accounts.md)を指定できます。これにより、プールが Batch Service サブスクリプションに割り当てられる (既定) か、ユーザー サブスクリプションに割り当てられるかを決定します。 Batch Service の既定の構成で Batch アカウントを作成した場合、アカウントは処理に使用できるコアの最大数に制限されます。 そのコア数が、Batch サービスでスケール可能な計算ノードの最大数になります。 このことにより、Batch サービスが自動スケールの数式によって指定された目標数に到達しない場合があります。 アカウントのクォータを表示する方法および増やす方法については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md) 」を参照してください。
>
>ユーザー サブスクリプション モードでアカウントを作成した場合、アカウントはそのサブスクリプションのコア クォータで共有します。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」の「[Virtual Machines の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits)」をご覧ください。

## <a name="autoscale-formulas"></a>自動スケーリングの数式

自動スケーリングの数式は、ユーザーが定義する文字列値であり、1 つまたは複数のステートメントが含まれています。 自動スケーリングの数式は、プールの [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) 要素 (Batch REST) または [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) プロパティ (Batch .NET) に割り当てられます。 Batch サービスは、定義された数式を使用して、次の処理期間中のプール内の計算ノードの目標数を決定します。 この数式は 8 KB 以下の文字列で、最大 100 個のステートメントをセミコロンで区切って指定できます。また、改行やコメントを使用することもできます。

自動スケールの数式は、Batch 自動スケール "言語" と捉えることができます。 数式は自由形式のステートメントになっていて、サービス定義の変数 (Batch サービスによって定義された) とユーザー定義の変数の両方を含めることができます。 数式では、組み込みの型、演算子、関数を使用して、これらの値に対する各種の操作を実行できます。 たとえば、ステートメントは次の形式を使用する場合があります。

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

一般に、数式には複数のステートメントが存在し、先行するステートメントで取得された値に対してさまざまな操作が実行されます。 たとえば `variable1` の値を取得しておき、それを関数に渡して `variable2` に代入します。

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

計算ノードが目標の数に到達するように、自動スケールの数式にこれらのステートメントを含めます。 専用ノードと優先順位の低いノードにはそれぞれ独自のターゲット設定があります。 自動スケールの数式には、専用ノード、優先順位の低いノード、またはその両方に対して目標値を含めることができます。

ノードの目標数は、現在のプール内のその種類のノードの数より多くなることも、少なくなることも、同じになることもあります。 プールの自動スケーリングの数式は、Batch によって特定の[自動スケーリング間隔](#automatic-scaling-interval)で評価されます。 Batch はプール内の各種類のノードの目標数を、自動スケールの数式の評価時に割り出されたノード数と一致するように調整します。

### <a name="sample-autoscale-formulas"></a>自動スケールの数式の例

ほとんどのシナリオで機能するように調整できる 2 つの自動スケールの数式の例を次に示します。 必要に応じて、例の式の `startingNumberOfVMs` 変数と `maxNumberofVMs` 変数を調整できます。

#### <a name="pending-tasks"></a>保留中のタスク

この自動スケールの数式では、プールは最初は 1 つの VM で作成されます。 `$PendingTasks` メトリックにより、実行中またはキューに置かれているタスクの数が定義されます。 この数式により、最後の 180 秒間の保留タスク平均数が判明し、`$TargetDedicatedNodes` 変数が適宜設定されます。 この数式により、専用ノードの目標数が 25 台の VM を超えることはありません。 新しいタスクが送信されると、プールは自動的に拡大します。 タスクが完了すると、VM が解放され、自動スケーリングの数式によってプールが縮小します。

この数式は専用ノードをスケールしますが、優先順位の低いノードのスケールにも適用されるように変更できます。

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Preempted Node (割り込まれたノード)

この例では、25 個の優先順位の低いノードから始まるプールを作成します。 優先順位の低いノードが割り込まれるたびに、専用のノードに置き換えられます。 最初の例と同様に、`maxNumberofVMs` 変数は、プールが 25 台の VM を超過することを防ぎます。 この例は、優先順位の低い VM を活用する場合に役立ちます。また、プールの有効期間中、割り込みが一定数に留まるようにします。

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

このトピックの後半で、[自動スケーリングの数式を作成する方法](#write-an-autoscale-formula)の詳細について説明し、追加の[自動スケーリングの数式の例](#example-autoscale-formulas)を確認します。

## <a name="variables"></a>変数

自動スケールの数式には、**サービス定義** の変数と **ユーザー定義** の変数の両方を使用できます。

サービス定義の変数は Batch サービスに組み込まれています。 サービス定義の変数には、読み取り/書き込み可能な変数と読み取り専用の変数があります。

ユーザー定義の変数は、ユーザーが定義する変数です。 上記の数式の例では、`$TargetDedicatedNodes` と `$PendingTasks` はサービス定義変数であり、`startingNumberOfVMs` と `maxNumberofVMs` はユーザー定義変数です。

> [!NOTE]
> サービス定義の変数は、常にドル記号 ($) が前に付きます。 ユーザー定義の変数では、ドル記号は省略可能です。

次の表に、Batch サービスで定義されている読み取り/書き込み変数と読み取り専用変数を示します。

### <a name="read-write-service-defined-variables"></a>読み取り/書き込み可能なサービス定義変数

これらのサービス定義の変数の値を取得および設定することで、プール内の計算ノードの数を管理できます。

| 変数 | 説明 |
| --- | --- |
| $TargetDedicatedNodes |プールの専用計算ノードの目標数。 プールが目的の数のノードに常に到達するとは限らないため、これは目標として指定されます。 たとえば、自動スケーリング評価によって専用ノードの目標数がプールが最初に設定された目標に達する前に変更された場合、そのプールは目標に到達しない可能性があります。 <br /><br /> Batch サービス モードで作成されたアカウント内のプールの目標が Batch アカウント ノードまたはコア クォータを超える場合、その目標に到達しない可能性があります。 ユーザー サブスクリプション モードで作成されたアカウント内のプールの目標がそのサブスクリプションの共有コア クォータを超える場合、その目標に到達しない可能性があります。|
| $TargetLowPriorityNodes |プールの優先順位の低い計算ノードの目標数。 プールが目的の数のノードに常に到達するとは限らないため、これは目標として指定されます。 たとえば、自動スケール評価によって優先順位の低いノードの目標数がプールが最初に設定された目標に達する前に変更された場合、プールは目標に到達しない可能性があります。 また、目標が Batch アカウント ノードまたはコア クォータを超える場合、プールはその目標に到達しない可能性があります。 <br /><br /> 優先順位の低い計算ノードの詳細については、「[Batch で優先順位の低い VM を使用する](batch-low-pri-vms.md)」を参照してください。 |
| $NodeDeallocationOption |コンピューティング ノードがプールから削除されるときに発生するアクション。 次のいずれかの値になります。<ul><li>**requeue**:既定値。 すぐにタスクを終了し、再スケジュールされるようにジョブ キューに戻します。 このアクションにより、可能な限り早くノードの目標数に到達します。 ただし、実行中のタスクが中断され、完全に再起動される必要があるため、効率が低下する場合があります。 <li>**terminate**:タスクをすぐに終了し、ジョブ キューから削除します。<li>**taskcompletion**:現在実行中のタスクの完了を待ち、プールからノードを削除します。 タスクが中断されてキューに再登録されないようにして、タスクで行われた作業が無駄にならないようにするには、このオプションを使用します。<li>**retaineddata**:ノードでローカル タスクによって保持されているすべてのデータがクリーンアップされるまで待機してから、ノードをプールから削除します。</ul> |

> [!NOTE]
> 別名 `$TargetDedicated` を使用して、`$TargetDedicatedNodes` 変数を指定することもできます。 同様に、別名 `$TargetLowPriority` を使用して、`$TargetLowPriorityNodes` 変数を指定できます。 完全な名前の変数とその別名の両方が数式によって設定されている場合は、完全な名前の変数に割り当てられた値が優先されます。

### <a name="read-only-service-defined-variables"></a>読み取り専用のサービス定義変数

これらのサービス定義変数の値を取得して、Batch サービスのメトリックに基づいて調整できます。

> [!IMPORTANT]
> 現在、ジョブ解放タスクは、$ActiveTasks や $PendingTasks など、タスク数を提供する変数には含まれていません。 自動スケーリングの数式によっては、このためにノードが削除され、ジョブ解放タスクを実行するためのノードが使用できなくなることがあります。

| 変数 | 説明 |
| --- | --- |
| $CPUPercent |平均 CPU 使用率。 |
| $WallClockSeconds |使用された秒数。 |
| $MemoryBytes |使用された平均メガバイト数。 |
| $DiskBytes |ローカル ディスクで使用された平均ギガバイト数。 |
| $DiskReadBytes |読み取るバイト数。 |
| $DiskWriteBytes |書き込まれたバイト数。 |
| $DiskReadOps |実行された読み取りディスク操作の数。 |
| $DiskWriteOps |実行された書き込みディスク操作の数。 |
| $NetworkInBytes |受信バイト数。 |
| $NetworkOutBytes |送信バイト数。 |
| $SampleNodeCount |計算ノードの数。 |
| $ActiveTasks |実行する準備はできているがまだ実行されていないタスクの数。 これには、アクティブ状態にあり、依存関係が満たされているすべてのタスクが含まれます。 アクティブ状態にあるものの、依存関係が満たされていないタスクはすべて $ActiveTasks の数から除外されます。 マルチインスタンス タスクの場合、$ActiveTasks にはタスクに設定されているインスタンスの数が含まれます。|
| $RunningTasks |実行状態のタスクの数。 |
| $PendingTasks |$ActiveTasks と $RunningTasks の合計。 |
| $SucceededTasks |正常に完了したタスクの数。 |
| $FailedTasks |失敗したタスクの数。 |
| $TaskSlotsPerNode |プール内の単一の計算ノードで同時実行タスクを実行するために使用できるタスク スロットの数。 |
| $CurrentDedicatedNodes |専用コンピューティング ノードの現在の数。 |
| $CurrentLowPriorityNodes |優先順位の低い計算ノードの現在の数。割り込まれたノードも含まれます。 |
| $PreemptedNodeCount | 割り込み状態にあるプール内のノードの数。 |

> [!TIP]
> これらの読み取り専用のサービス定義変数は、それぞれに関連付けられたデータにアクセスするさまざまなメソッドを指定する *オブジェクト* です。 詳しくは、後述の「[サンプル データの取得](#obtain-sample-data)」をご覧ください。

> [!NOTE]
> 特定の時点で実行されているタスクの数に基づいてスケーリングする場合は `$RunningTasks` を使用し、実行するためにキューに配置されているタスクの数に基づいてスケーリングする場合は `$ActiveTasks` を使用します。

## <a name="types"></a>型

自動スケーリングの数式では、次の種類がサポートされています。

- double
- doubleVec
- doubleVecList
- string
- timestamp - 次のメンバーを含む複合構造です。
  - year
  - month (1 ～ 12)
  - day (1 ～ 31)
  - weekday (数値の形式で表記します。月曜は 1 など)
  - hour (24 時間の数字で表記します。午後 1 時は 13 など)
  - minute (00 ～ 59)
  - second (00 ～ 59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>操作

前のセクションに列挙されている型に対して、次の演算を実行できます。

| 操作 | サポートされている演算子 | 結果の種類 |
| --- | --- | --- |
| double *&lt;演算子&gt;* double |+, -, *, / |double |
| double *&lt;演算子&gt;* timeinterval |* |timeinterval |
| doubleVec *&lt;演算子&gt;* double |+, -, *, / |doubleVec |
| doubleVec *&lt;演算子&gt;* doubleVec |+, -, *, / |doubleVec |
| timeinterval *&lt;演算子&gt;* double |*, / |timeinterval |
| timeinterval *&lt;演算子&gt;* timeinterval |+、- |timeinterval |
| timeinterval *&lt;演算子&gt;* timestamp |+ |timestamp |
| timestamp *&lt;演算子&gt;* timeinterval |+ |timestamp |
| timestamp *<演算子>* timestamp |- |timeinterval |
| *演算子* double |-, ! |double |
| *演算子* timeinterval |- |timeinterval |
| double *&lt;演算子&gt;* double |<, <=, ==, >=, >, != |double |
| string *<演算子>* string |<, <=, ==, >=, >, != |double |
| timestamp *<演算子>* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *&lt;演算子&gt;* timeinterval |<, <=, ==, >=, >, != |double |
| double *&lt;演算子&gt;* double |&&, &#124;&#124; |double |

3 項演算子で double 型の値をテストするときに (`double ? statement1 : statement2`)、0 以外の値は **true**、0 は **false** になります。

## <a name="functions"></a>関数

自動スケーリングの数式を定義するときに、これらの定義済みの **関数** を使用できます。

| 機能 | の戻り値の型 : | 説明 |
| --- | --- | --- |
| avg(doubleVecList) |double |doubleVecList のすべての値の平均値を返します。 |
| len(doubleVecList) |double |doubleVecList から作成されたベクター長を返します。 |
| lg (double) |double |2 を底とする double の対数を返します。 |
| lg(doubleVecList) |doubleVec |2 を底とする doubleVecList の成分ごとの対数を返します。 このパラメーターには明示的に vec(double) を渡す必要があります。 そのようにしないと、double lg(double) として解釈されます。 |
| ln(double) |double |double の自然対数を返します。 |
| ln(doubleVecList) |doubleVec |double の自然対数を返します。 |
| log(double) |double |10 を底とする double の対数を返します。 |
| log(doubleVecList) |doubleVec |10 を底とする doubleVecList の成分ごとの対数を返します。 この単一の double パラメーターには明示的に vec(double) を渡す必要があります。 そのようにしないと、double log(double) として解釈されます。 |
| max(doubleVecList) |double |doubleVecList の最大値を返します。 |
| min(doubleVecList) |double |doubleVecList の最小値を返します。 |
| norm(doubleVecList) |double |doubleVecList から作成されたベクトルの 2 ノルムを返します。 |
| percentile(doubleVec v, double p) |double |ベクトル v の百分位要素を返します。 |
| rand() |double |0\.0 ～ 1.0 のランダムな値を返します。 |
| range(doubleVecList) |double |doubleVecList の最小値と最大値の差を返します。 |
| std(doubleVecList) |double |doubleVecList の値のサンプルの標準偏差を返します。 |
| stop() | |自動スケール式の評価を停止します。 |
| sum(doubleVecList) |double |doubleVecList のすべての成分の合計を返します。 |
| time(string dateTime="") |timestamp |パラメーターが渡されない場合は現在の時刻のタイムスタンプ、渡された場合は dateTime 文字列のタイムスタンプを返します。 サポートされている dateTime 形式は、W3C-DTF と RFC 1123 です。 |
| val(doubleVec v, double i) |double |開始インデックス 0 のベクター v の位置 i にある要素の値を返します。 |

前の表に示した一部の関数は、リストを引数として受け入れることができます。 コンマ区切りのリストは、*double* と *doubleVec* の任意の組み合わせです。 次に例を示します。

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*doubleVecList* 値は、評価の前に 1 つの *doubleVec* に変換されます。 たとえば、`v = [1,2,3]` の場合、`avg(v)` の呼び出しは、`avg(1,2,3)` の呼び出しに相当します。 `avg(v, 7)` の呼び出しは、`avg(1,2,3,7)` の呼び出しに相当します。

## <a name="metrics"></a>メトリック

数式を定義するときは、リソースとタスクの両方のメトリックを使用できます。 プール内の専用ノードの目標数は、収集して評価したメトリック データに基づいて調整します。 各メトリックの詳細については、前述の「[変数](#variables)」セクションをご覧ください。

<table>
  <tr>
    <th>メトリック</th>
    <th>説明</th>
  </tr>
  <tr>
    <td><b>リソース</b></td>
    <td><p>リソース メトリックは、計算ノードの CPU、帯域幅、およびメモリの使用状況とノード数に基づくメトリックです。</p>
        <p> 次のサービス定義の変数は、ノード数に基づいて調整を行う場合に有用です。</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>次のサービス定義の変数は、ノード リソースの使用状況に基づいて調整を行う場合に有用です。</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>タスク</b></td>
    <td><p>タスク メトリックは、タスクの状態 (アクティブ、保留中、完了) に基づくメトリックです。 次のサービス定義の変数は、タスク メトリックに基づいてプールのサイズを調整する場合に有用です。</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>サンプル データの取得

タスクおよびリソースのメトリック データ (サンプル) を取得し、そのデータに基づいてプールのサイズを調整することが、自動スケーリングの数式の主要な動作となります。 そのため、自動スケーリングの数式とサンプルとがどのように関与するのかをしっかりと把握しておくことが重要です。

### <a name="methods"></a>メソッド

自動スケーリングの数式は、Batch サービスから提供されるメトリック データのサンプルに基づいて作用します。 プール サイズは、数式に与えられる値に基づいて拡大されたり縮小されたりします。 サービス定義の変数は、オブジェクトに関連付けられたデータにアクセスするメソッドを提供するオブジェクトです。 たとえば、次の式は、最後の 5 分間の CPU 使用率を取得する要求を示しています。

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

次のメソッドは、サービス定義変数に関するサンプル データを取得するために使用できます。

| メソッド | 説明 |
| --- | --- |
| GetSample() |`GetSample()` メソッドは、データ サンプルのベクターを返します。<br/><br/>サンプルは、30 秒相当のメトリック データです。 つまり、30 秒ごとにサンプルが取得されます。 ただし、この後も説明しますが、サンプルが収集されてから、それが数式に使用できるようになるまでには時間差があります。 そのため、特定の期間に取得されたすべてのサンプルを数式の評価に使用できない可能性があります。<ul><li>`doubleVec GetSample(double count)`:最新の収集済みサンプルから取得するサンプル数を指定します。 `GetSample(1)` は、使用できる最新のサンプルを返します。 ただし、`$CPUPercent` などのメトリックの場合、サンプルが収集された *時間* がわからないので、`GetSample(1)` を使用できません。 最新の場合もありますが、システム上の問題が原因でかなり古い可能性があります。 このような場合は、次のように期間を使用することが適切です。<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`:サンプル データを収集する期間を指定します。 指定した期間内に必要となるサンプルの割合をオプションで指定できます。 たとえば、`$CPUPercent.GetSample(TimeInterval_Minute * 10)` は、過去 10 分間のサンプルがすべて `CPUPercent` 履歴に存在する場合、20 個のサンプルを返します。 過去 1 分間の履歴を使用できない場合は、18 個のサンプルのみが返されます。 この場合、`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` は、サンプルの 90% しか使用できないため、失敗しますが、`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` は成功します。<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`:開始時刻と終了時刻の両方を使用して、データを収集する期間を指定します。 前述のように、サンプルが収集される時間と、数式に使用できるようになる時間には遅延があります。 `GetSample` メソッドを使用する際にはこの遅延を考慮します。 後述の `GetSamplePercent` をご覧ください。 |
| GetSamplePeriod() |履歴のサンプル データ セットで受け取ったサンプルの期間を返します。 |
| Count() |メトリック履歴のサンプルの合計数を返します。 |
| HistoryBeginTime() |使用可能な最も古いメトリックのデータ サンプルのタイムスタンプを返します。 |
| GetSamplePercent() |特定の時間間隔で利用できるサンプルの割合を返します。 たとえば、「 `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )` 」のように入力します。 返されたサンプルの割合が指定した `samplePercent` 未満の場合、`GetSample` メソッドは失敗するので、まず `GetSamplePercent` メソッドを使用して確認します。 その後、十分なサンプルが存在しない場合は、自動スケール評価を停止せずに代替の操作を実行します。 |

### <a name="samples"></a>サンプル

Batch サービスでは、タスクおよびリソースのメトリックのサンプルを定期的に取得し、自動スケールの数式でこのサンプルを使用できるようにします。 これらのサンプルは 30 秒に 1 回、Batch サービスによって記録されます。 ただし通常、サンプルが記録されてからそれが自動スケールの数式で使用できるよう (読み取り可能) になるまでには、遅延が発生します。 さらに、ネットワークやその他のインフラストラクチャの問題などの要因により、特定の間隔でサンプルが記録されない場合があります。

### <a name="sample-percentage"></a>サンプルの割合

`samplePercent` を `GetSample()` メソッドに渡したり、`GetSamplePercent()` メソッドを呼び出したりするときの "_割合_" とは、Batch サービスによって記録されるサンプルの最大合計数と、自動スケールの数式で使用できるサンプル数との比を示します。

例として、10 分間の時間枠で説明します。 サンプルは 30 秒ごとに記録されるため、10 分間の期間内に Batch によって記録されるサンプルの最大合計数は 20 サンプルとなります (1 分あたり 2 つ)。 一方、レポートの機構上必然的に伴う時間差や Azure 内の問題により、自動スケールの数式で読み取り可能なサンプル数が 15 にしか到達しない可能性もあります。 このため、たとえばこの 10 分間に数式で使用可能なサンプル数は、記録されたサンプルの合計数の 75% に限られるということです。

### <a name="getsample-and-sample-ranges"></a>GetSample() とサンプルの範囲

自動スケーリングの数式により、ノードを追加または削除してプールを拡大および縮小します。 ノードには金銭的コストが伴うため、数式には、十分なデータに基づいたインテリジェントな分析方法を使用してください。 数式に傾向分析を使用することが推奨されます。 収集したサンプルの範囲に基づいて、プールの拡大と縮小を行うことをお勧めします。

そのためには、`GetSample(interval look-back start, interval look-back end)` を使用して、サンプルのベクターを返します。

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

上記の行は Batch によって評価されると、値のベクターとしてサンプルの範囲を返します。 次に例を示します。

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

サンプルのベクターを収集したら、`min()`、`max()`、`avg()` などの関数を使用して、収集した範囲から有意な値を導き出すことができます。

セキュリティを強化するために、特定の期間に使用できるサンプルの割合が特定の値を下回る場合に、数式による評価が強制的に失敗になるように設定できます。 指定したサンプルの割合を使用できない場合は、数式の評価を強制的に失敗させることで、数式のそれ以上の評価を中止するように Batch に指示します。 この場合、プール サイズの変更は行われません。 評価を成功させるために必要なサンプルの割合を指定するには、その割合を 3 番目のパラメーターとして `GetSample()` に指定します。 ここで、サンプルの 75% という要件は次のように指定します。

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

サンプルの可用性には遅延があるため、時間範囲を指定する際には、常に、開始時間を 1 分より長く遡って指定する必要があります。 サンプルがシステムを介して伝達されるまで約 1 分かかるため、`(0 * TimeInterval_Second, 60 * TimeInterval_Second)` の範囲内のサンプルは使用できない場合があります。 ここでも、 `GetSample()` の割合パラメーターを使用することで、サンプルの割合に関する特定の要件を適用できます。

> [!IMPORTANT]
> **自動スケールの数式では `GetSample(1)` に *のみ* 依存することは避ける** ことが強く推奨されます。 理由は、`GetSample(1)` は基本的には "どれほど前に取得したのかに関係なく、最後に取得したサンプルを渡す" よう Batch サービスに指示するためです。 それは単一のサンプルであり、また以前のサンプルであるため、最近のタスクまたはリソースの状態を表す情報として十分でない可能性があります。 `GetSample(1)`を使用する場合は、より大きなステートメントの一部であり、数式が依存する唯一のデータ ポイントになっていないことを確認してください。

## <a name="write-an-autoscale-formula"></a>自動スケールの数式の記述

自動スケールの数式は、これまでに挙げたさまざまな要素を使ってステートメントを記述し、それらを 1 つの数式として組み合わせることで作成します。 このセクションでは、実際のスケーリングの決定を行い、調整を行うことができる自動スケーリングの数式のサンプルを作成します。

まず、新しい自動スケールの数式の要件を定義します。 数式の要件は次のようになります。

- CPU 使用率が高い場合、プール内の専用計算ノードの目標数を増やす。
- CPU 使用率が低い場合、プール内の専用計算ノードの目標数を減らす。
- 常に専用ノードの最大数を 400 までに制限する。
- ノードの数を減らすときは、タスクを実行しているノードを削除しないでください。必要な場合は、タスクが完了するまで待ってから、ノードを削除してください。

この数式の最初のステートメントにより、CPU 使用率が高いときにノード数が増加されます。 過去 10 分間の平均 CPU 使用率の最小値が 70% を超えた場合にのみ、ユーザー定義変数 (`$totalDedicatedNodes`) を専用ノードの現在の目標数の 110% の値に設定するステートメントを定義します。 それ以外の場合は、専用ノードの現在の数の値を使用します。

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

CPU 使用率が低いときに専用ノードの数を減らすには、数式の次のステートメントで、過去 60 分間の平均 CPU 使用率が 20% を下回る場合に、同じ `$totalDedicatedNodes` 変数を専用ノードの現在の目標数の 90% に設定します。 それ以外の場合は、上記のステートメントに入力した `$totalDedicatedNodes` の現在の値を使用します。

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

ここでは、専用計算ノードの目標数を最大 400 に制限します。

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

最後に、タスクが完了するまでノードが削除されないことを確認します。

```
$NodeDeallocationOption = taskcompletion;
```

完全な数式を次に示します。

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> これを選択する場合は、数式文字列にコメントと改行の両方を含めることができます。 また、セミコロンがないと、評価エラーが発生する可能性があることにも注意してください。

## <a name="automatic-scaling-interval"></a>自動スケールの間隔

既定では、Batch サービスは自動スケールの数式に従って 15 分ごとにプールのサイズを調整します。 この間隔は、次のプール プロパティを使用して構成できます。

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

間隔の最小値は 5 分、最大値は 168 時間です。 この範囲外の間隔が指定されると、Batch サービスは「正しくない要求 (400)」エラーを返します。

> [!NOTE]
> 現行の自動スケール機能は、1 分以内に起こった変化に対応するというよりは、ワークロードを実行する過程でプールのサイズを少しずつ調整することを意図しています。

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Batch の SDK で自動スケーリング対応のプールを作成する

プールの自動スケーリングは、[Batch の SDK](batch-apis-tools.md#azure-accounts-for-batch-development)、[Batch REST API](/rest/api/batchservice/)、[Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)、および [Batch CLI](batch-cli-get-started.md) を使用して構成できます。 このセクションでは、.NET と Python の両方の例を紹介します。

### <a name="net"></a>.NET

.NET で自動スケール対応のプールを作成するには、次の手順を実行します。

1. [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) でプールを作成します。
1. [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) プロパティを `true` に設定します。
1. [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) プロパティを自動スケールの数式で設定します。
1. (省略可能) [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) プロパティを設定します (既定では 15 分)。
1. [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) または [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) で、プールをコミットします。

次の例では、.NET で自動スケーリング対応のプールを作成します。 プールの自動スケーリングの数式によって、専用ノードの目標数を月曜日は 5 に、それ以外の曜日は 1 に設定しています。 [自動スケールの間隔](#automatic-scaling-interval)は、30 分に設定されます。 次に示す C# スニペットまたはこの記事で示すその他の C# スニペットでは、`myBatchClient` は適切に初期化された [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) クラスのインスタンスです。

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> 自動スケーリング対応のプールを作成する際には、**CreatePool** の呼び出しに _targetDedicatedNodes_ パラメーターや _targetLowPriorityNodes_ パラメーターを指定しないでください。 代わりに、プールの **AutoScaleEnabled** プロパティと **AutoScaleFormula** プロパティを指定します。 これらのプロパティの値は各種類のノードの目標数を決定します。
>
> 自動スケーリング対応のプールのサイズを手動で変更する場合 ([BatchClient.PoolOperations.ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync) などによって)、最初にプールで自動スケーリングを無効にしてから、プールのサイズを変更する必要があります。

### <a name="python"></a>Python

自動スケーリング対応のプールを Python SDK で作成するには:

1. プールを作成してその構成を指定します。
1. サービス クライアントにプールを追加します。
1. 作成した数式でプールの自動スケーリングを有効にします。

次の例に、これらの手順を示します。

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> その他の Python SDK の使用例については、GitHub の [Batch Python クイックスタート リポジトリ](https://github.com/Azure-Samples/batch-python-quickstart)を参照してください。

## <a name="enable-autoscaling-on-an-existing-pool"></a>既存のプールでの自動スケールの有効化

各 Batch SDK には自動スケーリングを有効にする方法が用意されています。 次に例を示します。

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [プールで自動スケールを有効にする](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

既存のプールで自動スケーリングを有効にする際には、次の点に注意してください。

- プールで自動スケーリングが現在無効になっている場合、要求の発行時に有効な自動スケーリングの数式を指定する必要があります。 必要に応じて、自動スケーリングの間隔を指定できます。 間隔を指定しない場合、既定値の 15 分が使用されます。
- プールで自動スケーリングが現在有効になっている場合、新しい数式、新しい間隔、またはその両方を指定できます。 これらのプロパティの 1 つ以上を指定する必要があります。
  - 新しい自動スケーリングの間隔を指定すると、既存のスケジュールが停止し、新しいスケジュールが開始します。 新しいスケジュールの開始時刻は、自動スケールを有効にする要求を発行した時間です。
  - 自動スケーリングの数式と間隔のいずれかを省略すると、引き続き Batch サービスではその設定の現在の値が使用されます。

> [!NOTE]
> .NET でプールを作成したときに **CreatePool** メソッドの *targetDedicatedNodes* パラメーターと *targetLowPriorityNodes* パラメーターの値を指定した、または別の言語で同等のパラメーターを指定した場合、自動スケーリングの数式が評価されるときにそれらの値は無視されます。

この C# の例では、[Batch .NET](/dotnet/api/microsoft.azure.batch) ライブラリを使用し、既存のプールで自動スケーリングを有効にします。

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>自動スケールの数式の更新

既存の自動スケール対応のプールの数式を更新するには、新しい数式で自動スケールを再度有効にする操作を呼び出します。 たとえば、次の .NET コードの実行時に `myexistingpool` で自動スケールが既に有効になっている場合、自動スケールの数式は、`myNewFormula` の内容に置き換わります。

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>自動スケールの間隔の更新

既存の自動スケール対応のプールの自動スケールの評価間隔を更新するには、新しい間隔で自動スケールを再度有効にする操作を呼び出します。 たとえば、既に自動スケールが有効なプールについて、自動スケール評価の間隔を 60 分に設定する場合、次のようになります。

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>自動スケールの数式の評価

プールに適用する前に数式を評価できます。 これにより、数式の結果を運用環境に導入する前にテストできます。

自動スケーリングの数式を評価する前に、まず 1 行の数式 `$TargetDedicatedNodes = 0` などの有効な数式を使用して、プールの自動スケーリングを有効にする必要があります。 その後、次のいずれかを使用してテスト対象の数式を評価します。

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) または [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    これらの Batch .NET メソッドでは、既存のプールの ID のほか、評価する自動スケールの数式が含まれた文字列が必要になります。

- [自動スケールの数式の評価](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    この REST API 要求では、URI にプール ID、要求本文の *autoScaleFormula* 要素に自動スケールの数式を指定します。 操作の応答には、数式に関連する可能性があるすべてのエラー情報が含まれています。

この [Batch .NET](/dotnet/api/microsoft.azure.batch) の例では、自動スケーリングの数式を評価します。 プールで自動スケーリングがまだ使用されていない場合は、まずそれを有効にします。

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

数式が正しく評価されると、このコード スニペットには次のような結果が表示されます。

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>自動スケールの実行に関する情報の取得

数式が正常に実行されるように、Batch によってプールで行われる自動スケールの実行の結果を、定期的に確認することをお勧めします。 これを行うには、プールへの参照を取得 (または更新) してから、最後に行われた自動スケーリングの実行のプロパティを確認します。

Batch .NET の場合、プールで行われた最近の自動スケールの実行に関する情報は、[CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) プロパティ内のいくつかのプロパティに含まれています。

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

REST API の場合、[プールに関する情報を取得する](/rest/api/batchservice/get-information-about-a-pool)要求によって、プールに関する情報が返されます。この情報の [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool) プロパティに、最新の自動スケールの実行に関する情報が含まれています。

次の C# の例では、Batch .NET ライブラリを使用して、プール _myPool_ の最新の自動スケーリング実行に関する情報を出力します。

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

前の例からのサンプル出力:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>プールの自動スケーリング イベントを使用して自動スケールの実行履歴を取得する
[PoolAutoScaleEvent](batch-pool-autoscale-event.md) に対してクエリを実行して、自動スケーリングの履歴を確認することもできます。 このイベントは、Batch サービスによって生成され、自動スケールの数式の評価と実行が発生するたびに記録します。これは、潜在的な問題のトラブルシューティングに役立ちます。

PoolAutoScaleEvent のサンプル イベント:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>自動スケールの数式の例

さまざまな方法でプールのコンピューティング リソースの量を調整する、いくつかの数式を見ていきます。

### <a name="example-1-time-based-adjustment"></a>例 1:時間ベースの調整

曜日や時間帯でプール サイズを調整する必要があるとします。 この例では、状況に応じてプールのノード数を増減させる方法を示します。

数式は、最初に現在の時刻を取得します。 平日 (1 ～ 5) および稼働時間 (午前 8 時～午後 6 時) 内の場合、目標のプール サイズは 20 のノードに設定されます。 その他の場合、プール サイズは 10 ノードに設定されます。

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` は、`TimeZoneInterval_Hour` と UTC オフセットの積に `time()` を追加することで、ローカル タイム ゾーンを反映するように調整できます。 たとえば、山地夏時間 (MDT) には `$curTime = time() + (-6 * TimeInterval_Hour);` を使用します。 オフセットは、夏時間の開始時と終了時に調整する必要があることに注意してください (該当する場合)。

### <a name="example-2-task-based-adjustment"></a>例 2:タスクベースの調整

この C# の例では、プールのサイズはキューのタスク数に基づいて調整されます。 数式の文字列にはコメントと改行の両方を含めています。

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>例 3: 並列タスクの説明

この C# の例では、タスクの数に基づいてプール サイズを調整します。 この数式では、プールに設定されている [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) 値が考慮されます。 このアプローチは、プールで[並列タスクの実行](batch-parallel-node-tasks.md)が有効になっている場合に便利です。

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>例 4: 初期のプール サイズの設定

この例は、最初の期間におけるプール サイズを特定のノード数に設定する、自動スケーリングの数式を使用した C# の例を示しています。 その後、実行中のアクティブなタスク数に基づいてプール サイズが調整されます。

具体的に、この数式では次のことを行います。

- 最初のプール サイズを 4 ノードに設定しています。
- プールのライフサイクルの最初の 10 分間は、プール サイズを調整しません。
- 10 分経過した後は、過去 60 分間の実行中でアクティブなタスク数の最大値を取得します。
  - 両方の値が 0 の場合は (過去 60 分間に実行中またはアクティブなタスクがないことを示します)、プール サイズは 0 に設定されます。
  - いずれかの値が 0 よりも大きい場合は、変更されません。

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>次のステップ

- [プール内のコンピューティング ノードで複数のタスクを同時に実行する](batch-parallel-node-tasks.md)方法を確認します。 自動スケーリングに加えて、これは一部のワークロードのジョブの実行時間を短縮し、費用の節約に役立つことがありなります。
- 効率性を高めるために [Azure Batch サービスを効率的にクエリする](batch-efficient-list-queries.md)方法を確認します。
