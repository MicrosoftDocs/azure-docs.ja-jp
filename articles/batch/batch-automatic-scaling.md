---
title: "Azure Batch プール内の計算ノードの自動スケール | Microsoft Docs"
description: "クラウド プールで自動スケールを有効にして、プール内のコンピューティング ノードの数を動的に調整します。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 9dbfa813ea64666779f1f85b3ccda2b4fa1a755b
ms.lasthandoff: 03/15/2017


---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Batch プール内のコンピューティング ノードを拡張するための自動スケールの数式の作成

自動スケールを使用すると、Azure Batch サービスによって、定義したパラメーターに基づいてプール内の計算ノードが動的に追加または削除されます。 アプリケーションによって使用される計算処理能力を自動的に調整 (ジョブのタスクの需要が大きくなったときにはノードを追加し、小さくなったときは除外) することで、時間とコストを削減できる可能性があります。

自動スケールをコンピューティング ノードのプールに対して有効にするには、定義した*自動スケールの数式* ([Batch .NET](batch-dotnet-get-started.md) ライブラリ内の [PoolOperations.EnableAutoScale][net_enableautoscale] メソッドの数式など) をプールに関連付けます。 Batch サービスでは、この数式を使用して、ワークロードを実行するために必要なコンピューティング ノードの数を決定します。 Batch は定期的に収集されるサービスのメトリック データのサンプルに従い、プール内のコンピューティング ノードの数が、関連付けられている数式に基づいて構成可能な間隔で調整されます。

自動スケールは、プールの作成時のほか、既存のプールに対して有効化することができます。 また、"自動スケール" が有効なプールに対し、既存の数式を変更することもできます。 Batch では、数式をプールに割り当てる前に数式の評価を行うことができるほか、自動スケールの実行状態を監視することができます。

## <a name="automatic-scaling-formulas"></a>自動スケールの数式
自動スケールの数式は、1 つまたは複数のステートメントを含む、ユーザーが定義する文字列値であり、この文字列値は、プールの [autoScaleFormula][rest_autoscaleformula] 要素 (Batch REST) または [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] プロパティ (Batch .NET) に割り当てられます。 Batch サービスをプールに割り当てると、Batch サービスによって、次の処理期間 (およびそれ以降の処理期間) にプールで使用可能なコンピューティング ノードの数の決定に数式が使用されます。 この数式はサイズが 8 KB 以下の文字列で、最大 100 個のステートメントをセミコロンで区切って指定できます。また、改行やコメントを使用することもできます。

自動スケールの数式は、Batch 自動スケール "言語" を使用することと考えることができます。 数式は自由形式のステートメントになっていて、サービス定義の変数 (Batch サービスによって定義された変数) とユーザー定義の変数 (ユーザーによって定義された変数) の両方を含めることができます。 組み込みの型、演算子、関数を使用して、これらの値に対する各種の操作を実行できます。 たとえば、ステートメントは次の形式を使用する場合があります。

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

一般に、数式には複数のステートメントが存在し、先行するステートメントで取得された値に対してさまざまな操作が実行されます。 たとえば `variable1` の値を取得しておき、それを関数に渡して `variable2` に代入します。

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

数式の中でそれらのステートメントを使用することによって、プールのコンピューティング ノード数をどこまでスケールするか、つまり**専用ノード**の**目標**数を得ることが必要となります。 この数は、現在のプール内のノード数より多くなることも、少なくなることも、同じになることもあります。 プールの自動スケールの数式は、Batch によって一定間隔で評価されます ([自動スケールの間隔](#automatic-scaling-interval) については以降で説明します)。 その後、プール内の目標ノード数が、評価時に自動スケールの数式によって割り出されたノード数と一致するように調整されます。

以下にごく単純な例を紹介します。この 2 つの行から成る自動スケールの数式は、コンピューティング ノードの最大数を 10 とし、アクティブなタスクの数に基づいてノードの数を調整する必要があることを示しています。

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

以降いくつかのセクションで、変数、演算子、操作、関数など、自動スケールの数式を構成するさまざまな要素について説明します。 Batch に存在する各種コンピューティング リソースとタスクについて、そのメトリックを取得する方法についても取り上げます。 これらのメトリックを使用すると、リソースの使用状況やタスクの状態に応じて、プールのノード数をインテリジェントに調整することができます。 その後、数式を作成する方法や、Batch の REST API と .NET API を使用してプールの自動スケールを有効にする方法について説明した後、 最後に数式の例をいくつか紹介します。

> [!IMPORTANT]
> 各 Azure Batch アカウントは、処理に使用できる最大コア (コンピューティング ノード) 数に制限されています。 そのコア数が、Batch サービスで作成される最大ノード数になります。 そのため、数式から求められるコンピューティング ノードの目標数には届かない場合もあります。 アカウントのクォータを表示する方法および増やす方法については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md) 」を参照してください。
> 
> 

## <a name="variables"></a>変数
自動スケールの数式には、**サービス定義**の変数と**ユーザー定義**の変数の両方を使用できます。 サービス定義の変数は Batch サービスに組み込まれており、読み取り/書き込み可能な変数と読み取り専用の変数があります。 ユーザー定義の変数は、ユーザーが *独自* に定義できる変数です。 上記の&2; 行の数式の例で、`$TargetDedicated` はサービス定義の変数であり、`$averageActiveTaskCount` はユーザー定義の変数です。

次の表に、Batch サービスで定義されている読み取り/書き込み変数と読み取り専用変数の両方を示します。

これらのサービス定義の変数の値を**取得**および**設定**することで、プール内のコンピューティング ノードの数を管理できます。

| 読み取り/書き込み可能なサービス定義変数 | Description |
| --- | --- |
| $TargetDedicated |プールの**専用コンピューティング ノード**の**目標**数。 これはスケールによって目指すプールのコンピューティング ノード数です。 プールはこのノード数に到達しない可能性があるため、この数は "目標" 数となります。 たとえば、最初に設定された目標ノード数にプールが達する前に、後続の自動スケール評価によって目標ノード数が再度変更される場合があります。 また、目標ノード数に到達する前に、Batch アカウントのノード数やコア クォータに達することも考えられます。 |
| $NodeDeallocationOption |コンピューティング ノードがプールから削除されるときに発生するアクション。 次のいずれかの値になります。<ul><li>**requeue** – すぐにタスクを終了し、再スケジュールされるようにジョブ キューに戻します。<li>**terminate** – すぐにタスクを終了し、ジョブ キューから削除します。<li>**taskcompletion** – 現在実行中のタスクの完了を待ち、プールからノードを削除します。<li>**retaineddata** - ノードでローカル タスクによって保持されているすべてのデータがクリーンアップされるまで待機してから、ノードをプールから削除します。</ul> |

これらのサービス定義変数の値を **取得** して、Batch サービスのメトリックに基づいて調整できます。

| 読み取り専用のサービス定義変数 | Description |
| --- | --- |
| $CPUPercent |平均 CPU 使用率。 |
| $WallClockSeconds |使用された秒数。 |
| $MemoryBytes |使用された平均メガバイト数。 |
| $DiskBytes |ローカル ディスクで使用された平均ギガバイト数。 |
| $DiskReadBytes |読み取られたバイト数。 |
| $DiskWriteBytes |書き込まれたバイト数。 |
| $DiskReadOps |実行された読み取りディスク操作の数。 |
| $DiskWriteOps |実行された書き込みディスク操作の数。 |
| $NetworkInBytes |受信バイト数。 |
| $NetworkOutBytes |送信バイト数。 |
| $SampleNodeCount |計算ノードの数。 |
| $ActiveTasks |アクティブ状態のタスクの数。 |
| $RunningTasks |実行状態のタスクの数。 |
| $PendingTasks |$ActiveTasks と $RunningTasks の合計。 |
| $SucceededTasks |正常に完了したタスクの数。 |
| $FailedTasks |失敗したタスクの数。 |
| $CurrentDedicated |専用コンピューティング ノードの現在の数。 |

> [!TIP]
> 上記の読み取り専用のサービス定義変数は、それぞれに関連付けられたデータにアクセスするさまざまなメソッドを指定する " *オブジェクト* " です。 詳細については、「 [サンプル データの取得](#getsampledata) 」をご覧ください。
> 
> 

## <a name="types"></a>型
次の **型** が数式でサポートされています。

* double
* doubleVec
* doubleVecList
* string
* timestamp -- timestamp は次のメンバーを含む複合構造になっています。
  
  * year
  * month (1 ～&12;)
  * day (1 ～&31;)
  * weekday (数値の形式で表記します。月曜は 1 など)
  * hour (24 時間の数字で表記します。午後 1 時は 13 など)
  * minute (00 ～&59;)
  * second (00 ～&59;)
* timeinterval
  
  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>操作
上記の型に対して、次の **演算** を実行できます。

| 操作 | サポートされている演算子 | 結果の種類 |
| --- | --- | --- |
| double *&lt;演算子&gt;* double |+, -, *, / |double |
| double *&lt;演算子&gt;* timeinterval |* |timeinterval |
| doubleVec *&lt;演算子&gt;* double |+, -, *, / |doubleVec |
| doubleVec *&lt;演算子&gt;* doubleVec |+, -, *, / |doubleVec |
| timeinterval *&lt;演算子&gt;* double |*, / |timeinterval |
| timeinterval *&lt;演算子&gt;* timeinterval |+, - |timeinterval |
| timeinterval *&lt;演算子&gt;* timestamp |+ |timestamp |
| timestamp *&lt;演算子&gt;* timeinterval |+ |timestamp |
| timestamp *<演算子>* timestamp |- |timeinterval |
| *&lt;演算子&gt;*double |-, ! |double |
| *&lt;演算子&gt;*timeinterval |- |timeinterval |
| double *&lt;演算子&gt;* double |<, <=, ==, >=, >, != |double |
| string *<演算子>* string |<, <=, ==, >=, >, != |double |
| timestamp *<演算子>* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *&lt;演算子&gt;* timeinterval |<, <=, ==, >=, >, != |double |
| double *&lt;演算子&gt;* double |&&, &#124;&#124; |double |

3 項演算子で double 型の値をテストするときに (`double ? statement1 : statement2`)、0 以外の値は **true**、0 は **false** になります。

## <a name="functions"></a>関数
次の定義済みの **関数** は、自動スケールの数式の定義に使用できます。

| 関数 | 戻り値の型 | Description |
| --- | --- | --- |
| avg(doubleVecList) |double |doubleVecList のすべての値の平均値を返します。 |
| len(doubleVecList) |double |doubleVecList から作成されたベクター長を返します。 |
| lg (double) |double |2 を底とする double の対数を返します。 |
| lg(doubleVecList) |doubleVec |2 を底とする doubleVecList の成分ごとの対数を返します。 このパラメーターには明示的に vec(double) を渡す必要があります。 そのようにしないと、double lg(double) として解釈されます。 |
| ln(double) |double |double の自然対数を返します。 |
| ln(doubleVecList) |doubleVec |2 を底とする doubleVecList の成分ごとの対数を返します。 このパラメーターには明示的に vec(double) を渡す必要があります。 そのようにしないと、double lg(double) として解釈されます。 |
| log(double) |double |10 を底とする double の対数を返します。 |
| log(doubleVecList) |doubleVec |10 を底とする doubleVecList の成分ごとの対数を返します。 この単一の double パラメーターには明示的に vec(double) を渡す必要があります。 そのようにしないと、double log(double) として解釈されます。 |
| max(doubleVecList) |double |doubleVecList の最大値を返します。 |
| min(doubleVecList) |double |doubleVecList の最小値を返します。 |
| norm(doubleVecList) |double |doubleVecList から作成されたベクトルの&2; ノルムを返します。 |
| percentile(doubleVec v, double p) |double |ベクトル v の百分位要素を返します。 |
| rand() |double |0.0 ～ 1.0 のランダムな値を返します。 |
| range(doubleVecList) |double |doubleVecList の最小値と最大値の差を返します。 |
| std(doubleVecList) |double |doubleVecList の値のサンプルの標準偏差を返します。 |
| stop() | |自動スケール式の評価を停止します。 |
| sum(doubleVecList) |double |doubleVecList のすべての成分の合計を返します。 |
| time(string dateTime="") |timestamp |パラメーターが渡されない場合は現在の時刻のタイムスタンプ、渡された場合は dateTime 文字列のタイムスタンプを返します。 サポートされている dateTime 形式は、W3C-DTF と RFC 1123 です。 |
| val(doubleVec v, double i) |double |開始インデックス&0; のベクター v の位置 i にある要素の値を返します。 |

上記の表に示した一部の関数は、リストを引数として受け入れることができます。 コンマ区切りのリストは、*double* と *doubleVec* の任意の組み合わせです。 For example:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*doubleVecList* 値は、評価の前に&1; つの *doubleVec* に変換されます。 たとえば、`v = [1,2,3]` の場合、`avg(v)` の呼び出しは、`avg(1,2,3)` の呼び出しに相当します。 `avg(v, 7)` の呼び出しは、`avg(1,2,3,7)` の呼び出しに相当します。

## <a name="getsampledata"></a>サンプル データの取得
自動スケールの数式は、Batch サービスから提供されるメトリック データ (サンプル) に基づいて作用します。 プール サイズは、サービスから数式に与えられる値に基づいて拡大されたり縮小されたりします。 上記のサービス定義の変数は、関連付けられたデータにアクセスするメソッドを提供するオブジェクトです。 たとえば、次の式は、最後の&5; 分間の CPU 使用率を取得する要求を示しています。

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| メソッド | Description |
| --- | --- |
| GetSample() |`GetSample()` メソッドは、データ サンプルのベクターを返します。<br/><br/>サンプルは、30 秒相当のメトリック データです。 つまり、30 秒ごとにサンプルが取得されます。 ただし、この後も説明しますが、サンプルが収集されてから、それが数式に使用できるようになるまでには時間差があります。 そのため、特定の期間に取得されたすべてのサンプルを数式の評価に使用できない可能性があります。<ul><li>`doubleVec GetSample(double count)`<br/>最新の収集済みサンプルから取得するサンプル数を指定します。<br/><br/>`GetSample(1)` は、使用できる最新のサンプルを返します。 ただし、`$CPUPercent` などのメトリックの場合、サンプルが収集された "*時間*" がわからないので、GetSample を使用できません。 最新の場合もありますが、システム上の問題が原因でかなり古い可能性があります。 このような場合は、次のように期間を使用することをお勧めします。<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>サンプル データを収集する期間を指定します。 指定した期間内に必要となるサンプルの割合をオプションで指定できます。<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` は、過去 10 分間のサンプルがすべて CPUPercent 履歴に存在する場合、20 個のサンプルを返します。 ただし、過去 1 分間の履歴を使用できない場合は、18 個のサンプルのみが返されます。 この場合、次のようになります。<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` は、サンプルの 90% しか使用できないため、失敗します。<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` は成功します。<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>開始時刻と終了時刻の両方を使用して、データを収集する期間を指定します。<br/><br/>前述のように、サンプルが収集される時間と、数式に使用できるようになる時間には遅延があります。 `GetSample` メソッドを使用するときは、この点を考慮する必要があります。 後述の `GetSamplePercent` をご覧ください。 |
| GetSamplePeriod() |履歴のサンプル データ セットで受け取ったサンプルの期間を返します。 |
| Count() |メトリック履歴のサンプルの合計数を返します。 |
| HistoryBeginTime() |使用可能な最も古いメトリックのデータ サンプルのタイムスタンプを返します。 |
| GetSamplePercent() |特定の時間間隔で利用できるサンプルの割合を返します。 For example:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>返されたサンプルの割合が指定した `samplePercent` 未満の場合、`GetSample` メソッドは失敗するので、まず `GetSamplePercent` メソッドを使用して確認します。 その後、十分なサンプルが存在しない場合は、自動スケール評価を停止せずに代替の操作を実行します。 |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>サンプル、サンプルの割合、 *GetSample()* メソッド
タスクおよびリソースのメトリック データを取得し、そのデータに基づいてプールのサイズを調整することが、自動スケールの数式の主要な動作となります。 そのため、自動スケールの数式とメトリック データ ("サンプル") とがどのように関係し合うのかをしっかりと把握しておくことが重要です。

**サンプル**

Batch サービスでは、タスクおよびリソースのメトリックの " *サンプル* " を定期的に取得し、自動スケールの数式でこのサンプルを使用できるようにします。 これらのサンプルは 30 秒に 1 回、Batch サービスによって記録されます。 しかし、サンプルが記録されてから、それが自動スケールの数式で使用できるよう (読み取り可能) になるまでには通常、ある程度の時間差が生じます。 さらに、ネットワークやその他のインフラストラクチャの問題など、さまざまな要因により、特定の間隔でサンプルが記録されない場合があります。 その結果としてサンプル不足になります。

**サンプルの割合**

`samplePercent` を `GetSample()` メソッドに渡したり、`GetSamplePercent()` メソッドを呼び出したりするときの "割合" とは、Batch サービスによって記録されるサンプルの "*最大*" 合計数と、自動スケールの数式で実際に "*使用できる*" サンプル数との比を示します。

例として、10 分間の時間枠で説明します。 サンプルは 30 秒ごとに記録されるため、10 分間の期間内に Batch によって記録されるサンプルの最大合計数は 20 サンプルとなります (1 分あたり 2 サンプル)。 一方、レポートの機構上必然的に伴う時間差や Azure インフラストラクチャ内の問題により、自動スケールの数式で読み取り可能なサンプル数が 15 にしか到達しない可能性もあります。 つまり、この 10 分間に数式で実際に使用可能なサンプル数は、記録されたサンプルの合計数の **75%** に限られるということです。

**GetSample() とサンプルの範囲**

プールの拡大と縮小は、自動スケールの数式に基づいてノードを追加したり削除したりすることによって行われます。 ノードには金銭的コストが伴うため、数式には、十分なデータに基づいたインテリジェントな分析方法を用いる必要があります。 このため、数式に傾向分析を取り入れ、 収集したサンプルの "*範囲*" に基づいて、プールの拡大と縮小を行うことをお勧めします。

そのためには、 `GetSample(interval look-back start, interval look-back end)` を使用して、サンプルの **ベクター** を返します。

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

上記の行は Batch によって評価されると、値のベクターとしてサンプルの範囲を返します。 For example:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

サンプルのベクターを収集したら、`min()`、`max()`、`avg()` などの関数を使用して、収集した範囲から有意な値を導き出すことができます。

セキュリティを強化するために、特定の期間に使用できるサンプルの割合が特定の値を下回る場合に、数式による評価を強制的に "*失敗*" させることができます。 指定したサンプルの割合を使用できない場合は、数式の評価を強制的に失敗させることで、数式のそれ以上の評価を中止するように Batch に指示します。プール サイズの変更は行われません。 評価を成功させるために必要なサンプルの割合を指定するには、その割合を&3; 番目のパラメーターとして `GetSample()` に指定します。 ここで、サンプルの 75% という要件は次のように指定します。

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

また、前述したようにサンプルの可用性には遅延があるため、時間範囲を指定する際には、常に、開始時間を&1; 分より長く遡って指定することが重要です。 サンプルがシステムを介して伝達されるまで約 1 分かかるため、 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` の範囲内のサンプルは使用できないことがよくあります。 ここでも、 `GetSample()` の割合パラメーターを使用することで、サンプルの割合に関する特定の要件を適用できます。

> [!IMPORTANT]
> **自動スケールの数式で、`GetSample(1)` に "*のみ*" 依存するのは避ける**ようにすることを**強くお勧めします**。 これは、 `GetSample(1)` では基本的に、"どれほど前に取得したのかに関係なく、最後に取得したサンプルを渡す" よう Batch サービスに指示するためです。 それは単一のサンプルであり、また以前のサンプルであるため、最近のタスクまたはリソースの状態を表す情報として十分でない可能性があります。 `GetSample(1)`を使用する場合は、より大きなステートメントの一部であり、数式が依存する唯一のデータ ポイントになっていないことを確認してください。
> 
> 

## <a name="metrics"></a>メトリック
数式を定義するときは、**リソース**と**タスク**の両方のメトリックを使用できます。 プール内の専用ノードの目標数は、収集して評価したメトリック データに基づいて調整します。 各メトリックの詳細については、前述の「 [変数](#variables) 」をご覧ください。

<table>
  <tr>
    <th>メトリック</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><b>リソース</b></td>
    <td><p><b>リソース メトリック</b>は、コンピューティング ノードの CPU、帯域幅、およびメモリの使用状況とノード数に基づくメトリックです。</p>
        <p> 次のサービス定義の変数は、ノード数に基づいて調整を行う場合に有用です。</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
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
    <td><p><b>タスク メトリック</b>は、タスクの状態 (アクティブ、保留中、完了) に基づくメトリックです。 次のサービス定義の変数は、タスク メトリックに基づいてプールのサイズを調整する場合に有用です。</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>自動スケールの数式の記述
自動スケールの数式は、これまでに挙げたさまざまな要素を使ってステートメントを記述し、それらを&1; つの数式として組み合わせることで作成します。 このセクションでは、いくつかの実際のスケーリング決定を行える、自動スケールの数式のサンプルを作成します。

まず、新しい自動スケールの数式の要件を定義します。 数式の要件は次のようになります。

1. CPU 使用率が高い場合、プール内のコンピューティング ノードの目標数を**増やす**。
2. CPU 使用率が低い場合、プール内のコンピューティング ノードの目標数を**減らす**。
3. 常に**最大**ノード数を 400 までに制限する。

CPU 使用率が高いときにノード数を "*増やす*" には、過去 10 分間の平均 CPU 使用率の最小値が 70% を超えた場合にのみ、ユーザー定義変数 (`$totalNodes`) をノードの現在の目標数の 110% の値に設定するステートメントを定義します。 70% を超えない場合は、現在の専用の値を使用します。

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

CPU 使用率が低いときにノード数を "*減らす*" には、数式の次のステートメントで、過去 60 分間の平均 CPU 使用率が 20% を下回る場合に、同じ `$totalNodes` 変数をノードの現在の目標数の 90% に設定します。 20% を下回らない場合は、上記のステートメントに入力した `$totalNodes` の現在の値を使用します。

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

ここでは、専用コンピューティング ノードの目標数を **最大** 400 に制限しています。

```
$TargetDedicated = min(400, $totalNodes)
```

完全な数式を次に示します。

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>自動スケール対応のプールの作成
自動スケール対応のプールを新しく作成するには、次の手法のいずれかを使用できます。

**Batch .NET**

1. [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) でプールを作成します。
2. [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) プロパティを `true` に設定します。
3. [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) プロパティを自動スケールの数式で設定します。
4. (省略可能) [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) プロパティを設定します (既定では 15 分)。
5. [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) または [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx) で、プールをコミットします。

**Batch REST API**

* [プールをアカウントに追加します](https://msdn.microsoft.com/library/azure/dn820174.aspx)。REST API 要求の `enableAutoScale` および `autoScaleFormula` 要素を指定し、作成時にプールの自動スケールが構成されるようにします。

次のコード スニペットを使うと、[Batch .NET][net_api] ライブラリを使用して自動スケール対応のプールを作成できます。 このプールでは、自動スケールの数式によって、目標ノード数を月曜日は 5 に、それ以外の曜日は 1 に設定しています。 [自動スケールの間隔](#automatic-scaling-interval)は、30 分に設定されます。 次に示す C# スニペットまたはこの記事で示すその他の C# スニペットでは、"myBatchClient" は適切に初期化された [BatchClient][net_batchclient] のインスタンスです。

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Batch REST API と .NET SDK のほか、その他の [Batch SDK](batch-apis-tools.md#batch-development-apis)、[Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)、[Batch CLI](batch-cli-get-started.md) のいずれかを使用して自動スケールの操作を行うこともできます。

> [!IMPORTANT]
> 自動スケール対応のプールを作成する場合は、`targetDedicated` パラメーターを指定**しない**ようにしてください。 また、自動スケール対応のプールのサイズを手動で変更する場合 ([BatchClient.PoolOperations.ResizePool][net_poolops_resizepool] など)、最初にプールで自動スケールを**無効**にしてから、プールのサイズを変更する必要があります。
> 
> 

### <a name="automatic-scaling-interval"></a>自動スケールの間隔
既定では、Batch サービスは自動スケールの数式に従って **15 分**ごとにプールのサイズを調整します。 ただし、この間隔は、次のプール プロパティを使用して構成することができます。

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

間隔の最小値は 5 分、最大値は 168 時間です。 この範囲外の間隔が指定されると、Batch サービスは「正しくない要求 (400)」エラーを返します。

> [!NOTE]
> 現行の自動スケール機能は、1 分以内に起こった変化に対応するというよりは、ワークロードを実行する過程でプールのサイズを少しずつ調整することを意図しています。
> 
> 

## <a name="enable-autoscaling-on-an-existing-pool"></a>既存のプールでの自動スケールの有効化
*targetDedicated* パラメーターを使用して所定のコンピューティング ノード数でプールを既に作成してある場合、引き続きプールの自動スケールを有効にできます。 各 Batch SDK には、"自動スケールを有効にする" 操作が用意されています。たとえば、次のものがあります。

* [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] (Batch .NET)
* [プールの自動スケーリングを有効にする][rest_enableautoscale] (REST API)

既存のプールで自動スケールを有効にする際には、次のことが当てはまります。

* "自動スケールを有効にする" 要求を発行する時点でプールの自動スケールが**無効**になっている場合、要求の発行時に有効な自動スケールの数式を指定する "*必要*" があります。 "*必要に応じて*"、自動スケール評価の間隔を指定できます。 指定しない場合、間隔は既定の値である 15 分になります。
* プールの自動スケールが現在**有効**になっている場合、自動スケールの数式または評価の間隔を指定できます。両方とも指定することも可能です。 これらのプロパティを両方とも省略することはできません。
  
  * 自動スケール評価の間隔を新しく指定すると、既存の評価スケジュールが停止し、新しいスケジュールが開始します。 新しいスケジュールの開始時刻は、"自動スケールを有効にする" 要求を発行した時間です。
  * 自動スケールの数式と評価の間隔のいずれかを省略すると、引き続き Batch サービスではその設定の現在の値が使用されます。

> [!NOTE]
> プールの作成時に値を *targetDedicated* パラメーターに指定した場合、自動スケールの数式の評価時に無視されます。
> 
> 

次の C# コード スニペットでは、[Batch .NET][net_api] ライブラリを使用して、既存のプールの自動スケールを有効にします。

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>自動スケールの数式の更新
既存の自動スケール対応のプールで数式を "*更新*" する場合、同じ "自動スケールを有効にする" 要求を使用します (Batch .NET の [EnableAutoScale][net_enableautoscale] を使うなど)。 "自動スケールを更新する" 特別な操作はありません。 たとえば、次のコードの実行時に "myexistingpool" で自動スケールが既に有効になっている場合、自動スケールの数式は、`myNewFormula` の内容に置き換わります。

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>自動スケールの間隔の更新
既存の自動スケール対応のプールに適用される自動スケール評価の間隔を変更するには、自動スケールの数式の更新と同様に、同じ [EnableAutoScale][net_enableautoscale] メソッドを使用します。 たとえば、既に自動スケールが有効なプールについて、自動スケール評価の間隔を 60 分に設定する場合、次のようになります。

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>自動スケールの数式の評価
プールに適用する前に数式を評価できます。 これにより、数式の "テスト実行" を行ってステートメントの評価を確認してから、数式を運用環境で使用することができます。

自動スケールの数式を評価するにはまず、**有効な数式**を使用して、プールの**自動スケールを有効**にする必要があります。 自動スケールをまだ有効にしていないプールで数式をテストしたい場合は、初めて自動スケールを有効にするときに、1 行の数式 `$TargetDedicated = 0` を使用できます。 その後、次のいずれかを使用してテスト対象の数式を評価します。

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) または [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)
  
    これらの Batch .NET メソッドでは、既存のプールの ID のほか、評価する自動スケールの数式が含まれた文字列が必要になります。 評価の結果は、返される [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) インスタンスに含まれます。
* [自動スケールの数式の評価](https://msdn.microsoft.com/library/azure/dn820183.aspx)
  
    この REST API 要求では、URI にプール ID、要求本文の *autoScaleFormula* 要素に自動スケールの数式を指定します。 操作の応答には、数式に関連する可能性があるすべてのエラー情報が含まれています。

この [Batch .NET][net_api] コード スニペットでは、[CloudPool][net_cloudpool] に適用する前に数式を評価します。 プールで自動スケールが有効になっていない場合、先に有効にします。

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
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
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

このスニペットで数式の評価に成功すると、次のような出力になります。

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>自動スケールの実行に関する情報の取得
数式が正常に実行されるように、Batch によってプールで行われる自動スケールの "実行" の結果を、定期的に確認することをお勧めします。 これを行うには、プールへの参照を取得 (または更新) し、最後に行われた自動スケールの実行のプロパティを確認します。

Batch .NET の場合、Batch サービスによってプールで行われた最近の自動スケールの実行に関する情報は、[CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) プロパティ内のいくつかのプロパティに含まれています。

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

REST API の場合、[プールに関する情報を取得する](https://msdn.microsoft.com/library/dn820165.aspx)要求によって、プールに関する情報が返されます。この情報の [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun) に、最新の自動スケールの実行に関する情報が含まれています。

次の C# コード スニペットでは、Batch .NET ライブラリを使用して、プール "myPool" の最新の自動スケール実行に関する情報を出力します。

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

上記のスニペットのサンプル出力は、次のようになります。

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>自動スケールの数式の例
さまざまな方法でプールのコンピューティング リソースの量を調整する、いくつかの数式を見ていきます。

### <a name="example-1-time-based-adjustment"></a>例 1: 時間ベースの調整
曜日と時刻に基づいてプールのサイズを調整し、プール内のノードの数を増減するとします。

この数式は、最初に現在の時刻を取得します。 平日 (1 ～ 5) および稼働時間 (午前 8 時～午後 6 時) 内の場合、目標のプール サイズは 20 のノードに設定されます。 その他の場合、プール サイズは 10 ノードに設定されます。

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>例 2: 時間ベースの調整
この例では、プールのサイズはキューのタスク数に基づいて調整されます。 コメントと改行の両方が数式の文字列に使用できることに注意してください。

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>例 3: 並列タスクの説明
別の例では、タスクの数に基づいてプールのサイズを調整します。 また、この数式では、プールに設定されている [MaxTasksPerComputeNode][net_maxtasks] 値が考慮されます。 これは、プールで [並列タスクの実行](batch-parallel-node-tasks.md) が有効になっている場合に特に便利です。

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>例 4: 初期のプール サイズの設定
次に自動スケールの数式を使用した C# コード スニペットの例を示します。 この例では、最初の期間はプール サイズを特定のノード数に設定し、最初の期間が経過した後は、実行中のアクティブなタスク数に基づいてプール サイズを調整します。

次のコード スニペットの数式では、

* 最初のプール サイズを&4; ノードに設定しています。
* プールのライフサイクルの最初の 10 分間は、プール サイズを調整しません。
* 10 分経過した後は、過去 60 分間の実行中でアクティブなタスク数の最大値を取得します。
  * 両方の値が 0 の場合は (過去 60 分間に実行中またはアクティブなタスクがないことを示します)、プール サイズは 0 に設定されます。
  * いずれかの値が&0; よりも大きい場合は、変更されません。

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>次のステップ
* [同時実行ノード タスクで Azure Batch コンピューティング リソースの使用率を最大にする](batch-parallel-node-tasks.md) 」では、プール内のコンピューティング ノードで複数のタスクを同時に実行する方法の詳細を説明しています。 自動スケール以外にも、この機能は一部のワークロードのジョブの実行時間短縮に役立つことがあり、費用の節約になります。
* 他にも、効率を上げるために、Batch アプリケーションによって、最適な方法で Batch サービスが照会されていることを確認してください。 「 [効率的な Azure Batch サービスのクエリ](batch-efficient-list-queries.md)」では、数千に上る可能性のあるコンピューティング ノードやタスクの状態を照会するときに、送信されるデータの量を制限する方法について説明しています。

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

