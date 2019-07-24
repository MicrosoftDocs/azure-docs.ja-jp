---
title: モデルに合わせてハイパーパラメーターを調整する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning サービスを使用して、ディープ ラーニング/機械学習モデルのハイパーパラメーターを効率的に調整します。 パラメーター検索空間を定義し、最適化する主要なメトリックを指定し、パフォーマンスの低い実行を早期に終了する方法を学習します。
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 48f714a505bc79f0556a829206821aef986ad5d0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240268"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning-service"></a>Azure Machine Learning service でモデルのハイパーパラメーターを調整する

Azure Machine Learning service を使用してモデルのハイパーパラメーターを効率的に調整します。  ハイパーパラメーターの調整には、次の手順が含まれます。

* パラメーター検索空間を定義する
* 最適化する主要メトリックを指定する  
* パフォーマンスの低い実行の早期終了条件を指定する
* ハイパーパラメーター調整用にリソースを割り当てる
* 上記の構成で実験を開始する
* トレーニングの実行を視覚化する
* モデルのパフォーマンスが最高の構成を選択する

## <a name="what-are-hyperparameters"></a>ハイパーパラメーターとは

ハイパーパラメーターとは、トレーニング プロセス自体を制御する、モデルをトレーニングするために選択する調整可能なパラメーターです。 たとえば、ディープ ニューラル ネットワークをトレーニングするには、モデルをトレーニングする前に、ネットワーク内の非表示レイヤー数と各レイヤー内のノード数を決定します。 通常、トレーニング プロセス中にこれらの値は一定のままです。

ディープ ラーニングや機械学習のシナリオでは、モデルのパフォーマンスは、選択されているハイパーパラメーター値によって大きく変わります。 ハイパーパラメーター探索の目標は、さまざまなハイパーパラメーターの構成全体を検索して、最高のパフォーマンスになる構成を見つけることです。 一般的に、検索空間が広大で、各構成の評価コストが高くなる可能性があることを考えると、ハイパーパラメーター探索プロセスは手間がかかります。

Azure Machine Learning では、ハイパーパラメーター探索を効率的な方法で自動化し、時間とリソースを大幅に節約することができます。 ハイパーパラメーター値の範囲と、トレーニングの最大実行回数を指定します。 異なるパラメーター構成を持つ複数の実行が同時に自動的に開始され、(ユーザーが選択したメトリックで測定された) 最高のパフォーマンスになる構成が見つかります。 パフォーマンスの低いトレーニング実行は自動的に早期終了されるので、コンピューティング リソースの無駄が軽減されます。 このようなリソースは、代わりに他のハイパーパラメーター構成の探索に使用されます。


## <a name="define-search-space"></a>検索空間を定義する

各ハイパーパラメーターに対して定義された値の範囲を探索することで、ハイパーパラメーターを自動的に調整します。

### <a name="types-of-hyperparameters"></a>ハイパーパラメーターの種類

各ハイパーパラメーターは、不連続または連続のいずれかです。

#### <a name="discrete-hyperparameters"></a>不連続ハイパーパラメーター 

不連続ハイパーパラメーターは、不連続値の中の `choice` として指定します。 `choice` には次のものを指定できます。

* 1 つまたは複数のコンマ区切りの値
* `range` オブジェクト
* 任意の `list` オブジェクト


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

この場合、`batch_size` は値 [16, 32, 64, 128] のいずれかになり、`number_of_hidden_layers` は値 [1, 2, 3, 4] のいずれかになります。

高度な不連続ハイパーパラメーターは、分布を使用して指定することもできます。 次の分布がサポートされています。

* `quniform(low, high, q)`: round(uniform(low, high) / q) * q などの値を返します
* `qloguniform(low, high, q)`: round(exp(uniform(low, high)) / q) * q などの値を返します
* `qnormal(mu, sigma, q)`: round(normal(mu, sigma) / q) * q などの値を返します
* `qlognormal(mu, sigma, q)`: round(exp(normal(mu, sigma)) / q) * q などの値を返します

#### <a name="continuous-hyperparameters"></a>連続ハイパーパラメーター 

連続ハイパーパラメーターは、連続した範囲の値にわたる分布として指定します。 サポートされている分布は次のとおりです。

* `uniform(low, high)`: 低い値と高い値の間の均等に分布される値を返します
* `loguniform(low, high)`: 戻り値の対数が均等に分布されるように exp(uniform(low, high)) に従って収束された値を返します
* `normal(mu, sigma)`: 平均ミューと標準偏差シグマを使用して正規分布された実際の値を返します
* `lognormal(mu, sigma)`: 戻り値の対数が正規分布されるように exp(normal(mu, sigma)) に従って収束された値を返します

パラメーター空間定義の例を次に示します。

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

このコードでは、2 つのパラメーター `learning_rate` と `keep_probability` で検索空間を定義します。 `learning_rate` は、平均値 10 と標準偏差 3 の正規分布になります。 `keep_probability` は、最小値 0.05 と最大値 0.1 の一様分布になります。

### <a name="sampling-the-hyperparameter-space"></a>ハイパーパラメーター空間のサンプリング

ユーザーは、ハイパーパラメーター空間定義に対して使用するパラメーター サンプリング方法も指定できます。 Azure Machine Learning サービスは、ランダム サンプリング、グリッド サンプリング、ベイジアン サンプリングをサポートしています。

#### <a name="random-sampling"></a>ランダム サンプリング

ランダム サンプリングでは、定義済みの探索空間からハイパーパラメーター値がランダムに選択されます。 ランダム サンプリングを使用すると、検索空間に不連続ハイパーパラメーターと連続ハイパーパラメーターの両方を含めることができます。

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>グリッド サンプリング

グリッド サンプリングは、定義済みの検索空間内のすべての実現可能な値に対して単純なグリッド検索を実行します。 `choice` を使用して指定されたハイパーパラメーターにのみ使用できます。 たとえば、次の空間には合計で 6 個のサンプルがあります。

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>ベイジアン サンプリング

ベイジアン サンプリングはベイジアン最適化アルゴリズムに基づいており、次にサンプリングするハイパーパラメーター値を合理的に選択します。 このサンプルは、以前のサンプルがどのように実行されたかに基づいて選択されるので、報告される主要メトリックが新しいサンプルで改善されます。

ベイジアン サンプリングを使用する場合、同時実行数は調整プロセスの有効性に影響を与えます。 通常、同時実行数が少ないほど、サンプリングの収束が向上します。これは、並列処理の次数が小さいほど、以前に完了した実行の恩恵を受ける実行数が増えるためです。

ベイジアン サンプリングは、検索空間に対して `choice` および `uniform` 分布のみをサポートしています。 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> ベイジアン サンプリングは早期終了ポリシーをサポートしていません (「[早期終了ポリシーを指定する](#specify-early-termination-policy)」を参照)。 ベイジアン パラメーター サンプリングを使用する場合は、`early_termination_policy = None` を設定するか、`early_termination_policy` パラメーターをオフのままにします。

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>主要メトリックを指定する

ハイパーパラメーターの調整実験で最適化する主要メトリックを指定します。 トレーニングの各実行は、この主要メトリックに対して評価されます。 パフォーマンスの低い実行 (主要メトリックが早期終了ポリシーに設定されている基準を満たさない) は終了されます。 主要メトリック名だけでなく、主要メトリックを最大化または最小化するという最適化の目標も指定します。

* `primary_metric_name`:最適化する主要メトリックの名前。 主要メトリックの名前は、トレーニング スクリプトによってログに記録されているメトリック名と完全に一致する必要があります。 「[ハイパーパラメーターの調整のためのログ メトリック](#log-metrics-for-hyperparameter-tuning)」を参照してください。
* `primary_metric_goal`:`PrimaryMetricGoal.MAXIMIZE` または `PrimaryMetricGoal.MINIMIZE` のいずれかを指定できます。実行を評価する際に主要メトリックを最大化するか最小化するかを決定します。 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

"精度" が最大になるように実行が最適化されます。  必ずこの値をトレーニング スクリプトに記録してください。

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>ハイパーパラメーターの調整のためのログ メトリック

モデルのトレーニング スクリプトでは、モデルのトレーニング中に関連するメトリックをログに記録する必要があります。 ハイパーパラメーターのチューニングを構成するときは、実行のパフォーマンスの評価に使用する主要メトリックを指定します。 (「[最適化する主要メトリックを指定する](#specify-primary-metric-to-optimize)」を参照。)トレーニング スクリプトでは、ハイパーパラメーターの調整処理に使用できるように、このメトリックを記録する必要があります。

次のサンプル スニペットで、このメトリックをトレーニング スクリプトに記録します。

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

トレーニング スクリプトは `val_accuracy` を計算し、"精度" としてログに記録します。これは主要メトリックとして使用されます。 メトリックをログに記録するたびに、ハイパーパラメーター調整サービスによって受信されます。 このメトリックを報告する頻度は、モデルの開発者が任意に決定できます。

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>早期終了ポリシーを指定する

早期終了ポリシーを使用して、パフォーマンスの低い実行を自動的に終了します。 終了により、リソースの無駄がなくなり、このようなリソースは代わりに他のパラメーター構成の探索に使用されます。

早期終了ポリシーを使用すると、ユーザーはポリシーの適用時期を制御する次のパラメーターを構成できます。

* `evaluation_interval`: ポリシーを適用する頻度。 トレーニング スクリプトによってログに記録されるたびに、主要メトリックは 1 間隔としてカウントされます。 そのため、`evaluation_interval` に 1 を指定すると、トレーニング スクリプトが主要メトリックを報告するたびにポリシーが適用されます。 `evaluation_interval` に 2 を指定すると、トレーニング スクリプトが主要メトリックを報告する 2 回に 1 回、ポリシーが適用されます。 指定しないと、`evaluation_interval` は既定で 1 に設定されます。
* `delay_evaluation`: 指定した間隔数の期間、最初のポリシー評価を遅延させます。 これは省略可能なパラメーターです。すべての構成を初期の最小間隔で実行できるため、トレーニング実行の早期終了を回避できます。 指定すると、このポリシーは delay_evaluation 以上の evaluation_interval の倍数ごとに適用されます。

Azure Machine Learning サービスは、以下の早期終了ポリシーをサポートしています。

### <a name="bandit-policy"></a>バンディット ポリシー

バンディットは、Slack 要素/Slack 量と評価間隔に基づく終了ポリシーです。 このポリシーは、パフォーマンスが最高のトレーニング実行に関して、指定された Slack 要素/Slack 量内に主要メトリックが収まらないすべての実行を早期に終了します。 次の構成パラメーターを指定できます。

* `slack_factor` または `slack_amount`: パフォーマンスが最高のトレーニング実行に関して許可される Slack。 `slack_factor` は、許可される Slack を比率として指定します。 `slack_amount` は、許可される Slack を比率ではなく絶対量として指定します。

    たとえば、間隔 10 でバンディット ポリシーが適用されているとします。 主要メトリックを最大化するという目標があり、間隔 10 で最高のパフォーマンスになる実行で主要メトリック 0.8 が報告されたとします。 0.2 の `slack_factor` を使用してポリシーが指定された場合、間隔 10 の最高のメトリックを持つトレーニング実行のうち、0.66 (0.8/(1+`slack_factor`)) 未満のトレーニングが終了されます。 代わりに 0.2 の `slack_amount` を使用してポリシーが指定された場合、間隔 10 の最高のメトリックを持つトレーニング実行のうち、0.6 (0.8 - `slack_amount`) 未満のトレーニングが終了されます。
* `evaluation_interval`: ポリシーを適用する頻度 (省略可能なパラメーター)。
* `delay_evaluation`: 指定した間隔数の期間、最初のポリシー評価を遅延させます (省略可能なパラメーター)。


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

この例では、評価間隔 5 から始めて、メトリックが報告される間隔ごとに早期終了ポリシーが適用されます。 最高のメトリックが (1/(1 + 0.1) 未満、または最高のパフォーマンスの実行のうち 91% 未満の実行はすべて終了されます。

### <a name="median-stopping-policy"></a>中央値の停止ポリシー

中央値の停止は、実行から報告される主要メトリックの移動平均に基づく早期終了ポリシーです。 このポリシーは、すべてのトレーニング実行全体の移動平均を計算し、移動平均の中央値よりもパフォーマンスが低い実行を終了します。 このポリシーでは、次の構成パラメーターを指定できます。
* `evaluation_interval`: ポリシーを適用する頻度 (省略可能なパラメーター)。
* `delay_evaluation`: 指定した間隔数の期間、最初のポリシー評価を遅延させます (省略可能なパラメーター)。


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

この例では、評価間隔 5 から始めて、間隔ごとに早期終了ポリシーが適用されます。 トレーニング実行全体で、最高の主要メトリックが間隔 1:5 に対して移動平均の中間値未満の場合、実行は間隔 5 で終了されます。

### <a name="truncation-selection-policy"></a>切り捨て選択ポリシー

切り捨て選択では、各評価間隔で、最も低いパフォーマンスの実行から指定した割合の取り消しが実行されます。 実行は主要メトリックに関するパフォーマンスに基づいて比較され、最も低い X% が終了されます。 次の構成パラメーターを指定できます。

* `truncation_percentage`: 各評価間隔で終了する最も低いパフォーマンスの割合 (%)。 1 ～ 99 の整数値を指定します。
* `evaluation_interval`: ポリシーを適用する頻度 (省略可能なパラメーター)。
* `delay_evaluation`: 指定した間隔数の期間、最初のポリシー評価を遅延させます (省略可能なパラメーター)。


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

この例では、評価間隔 5 から始めて、間隔ごとに早期終了ポリシーが適用されます。 間隔 5 のパフォーマンスが、間隔 5 のすべての実行の下位 20% に含まれる場合、その実行は間隔 5 で終了されます。

### <a name="no-termination-policy"></a>終了なしポリシー

すべてのトレーニング実行を完了まで実行するには、ポリシーを [なし] に設定します。 これには、早期終了ポリシーを一切適用しない効果があります。

```Python
policy=None
```

### <a name="default-policy"></a>既定のポリシー

ポリシーを指定していない場合、ハイパーパラメーター調整サービスは、すべてのトレーニング実行を完了まで実行します。

>[!NOTE] 
>先のジョブまで終了せずに節約する保守的なポリシーが望ましい場合は、`evaluation_interval` 1 および `delay_evaluation` 5 を指定して中央値の停止ポリシーを使用できます。 これらは保守的な設定であり、(評価データに基づいて) 主要メトリックに関する損失なしで約 25% から 35% の節約を実現できます。

## <a name="allocate-resources"></a>リソースを割り当てる

トレーニング実行の最大合計回数を指定することで、ハイパーパラメーター調整実験のリソース予算を制御します。  必要に応じて、ハイパーパラメーター調整実験の最大期間をしています。

* `max_total_runs`:作成されるトレーニング実行の最大合計回数。 上限 - たとえば、ハイパーパラメーター空間が有限でサンプル数があまりない場合には、実行数が少なくなる可能性があります。 1 から 100 の数値を指定する必要があります。
* `max_duration_minutes`:ハイパーパラメーター調整実験の最大期間 (分単位)。 このパラメーターは省略可能であり、指定すると、この期間の後に実行されていたすべての実行は、自動的に取り消されます。

>[!NOTE] 
>`max_total_runs` と `max_duration_minutes` の両方を指定した場合は、これら 2 つのしきい値のうちの最初のしきい値に達するとハイパーパラメーター調整実験は終了します。

さらに、ハイパーパラメーター調整の検索時に同時に実行できるトレーニング実行の最大回数を指定します。

* `max_concurrent_runs`:任意の時点で同時に実行できる実行の最大数です。 指定しない場合、すべての `max_total_runs` が並列で起動されます。 指定する場合、1 から 100 の数値を指定する必要があります。

>[!NOTE] 
>並列実行の数は、指定された計算ターゲットで使用できるリソースに基づいて制御されます。 そのため、目的の同時実行可能性のために、計算ターゲットに必要なリソースを確保する必要があります。

ハイパーパラメーター調整用にリソースを割り当てます。

```Python
max_total_runs=20,
max_concurrent_runs=4
```

このコードでは、ハイパーパラメーター調整実験は、一度に 4 つの構成を実行して合計で最大 20 回の実行を使用するように構成されます。

## <a name="configure-experiment"></a>実験を構成する

以上のセクションで定義されているハイパーパラメーター検索空間、早期終了ポリシー、主要メトリック、およびリソース割り当てを使用して、ハイパーパラメーター調整実験を構成します。 さらに、サンプリングされたハイパーパラメーターを使用して呼び出される `estimator` を指定します。 `estimator` は、実行するトレーニング スクリプト、ジョブあたりのリソース (シングルまたはマルチ gpu)、および使用する計算ターゲットを示します。 ハイパーパラメーター調整実験の同時実行性は、使用できるリソースによって制御されるため、`estimator` に指定した計算ターゲットに、必要な同時実行性に対応できる十分なリソースがあることを確認します (見積もりツールの詳細については、[モデルのトレーニング方法](how-to-train-ml-models.md)に関するページを参照してください)。

ハイパーパラメーター調整実験を構成します。

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>実験を送信する

ハイパーパラメーター調整構成を定義したら、実験を送信します。

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` はハイパーパラメーター調整実験に割り当てる名前、`workspace` は実験を作成するワークスペースです (実験について詳しくは、「[Azure Machine Learning サービスのしくみ](concept-azure-machine-learning-architecture.md)」をご覧ください)。

## <a name="visualize-experiment"></a>実験を視覚化する

Azure Machine Learning SDK には、トレーニング実行の進行状況を視覚化する Notebook ウィジェットが用意されています。 次のスニペットを使用すると、すべてのハイパーパラメーター調整実行が Jupyter Notebook の 1 か所で視覚化されます。

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

このコードにより、各ハイパーパラメーター構成のトレーニング実行に関する詳細情報が表に示されます。

![ハイパーパラメーター調整表](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

トレーニングの進行に合わせて、各実行のパフォーマンスを視覚化することもできます。 

![ハイパーパラメーター調整プロット](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

さらに、並行座標プロットを使用して、個々のハイパーパラメーターのパフォーマンスと値の間の相関関係を視覚的に特定することができます。 

![ハイパーパラメーター調整の並行座標](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Azure Web ポータルですべてのハイパーパラメーター調整実行を視覚化できます。 Web ポータルで実験を表示する方法について詳しくは、[実験の追跡方法](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)に関するページをご覧ください。

![ハイパーパラメーター調整ポータル](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-best-model"></a>最高のモデルを見つける

すべてのハイパーパラメーター調整実行が完了したら、最高のパフォーマンスの構成と対応するハイパーパラメーター値を特定します。

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>サンプル ノートブック
以下のノートブックをご覧ください。
* [how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) 
* [how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順
* [実験を追跡する](how-to-track-experiments.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
