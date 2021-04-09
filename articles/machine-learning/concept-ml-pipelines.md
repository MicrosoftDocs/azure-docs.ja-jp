---
title: 機械学習パイプラインとは
titleSuffix: Azure Machine Learning
description: 機械学習パイプラインによって機械学習ワークフローを構築、最適化、管理する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 584e421b6beac0e4ecfab5b3e3cb735b8465e1b4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503523"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Azure Machine Learning パイプラインとは

この記事では、機械学習パイプラインを使用して、機械学習ワークフローの構築、最適化、管理を行う方法について説明します。 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>どの Azure パイプライン テクノロジを使用すべきか

Azure クラウドには、それぞれ目的が異なる複数の種類のパイプラインが用意されています。 次の表に、それらのさまざまなパイプラインとその用途を示します。

| シナリオ | プライマリ ペルソナ | Azure オファリング | OSS オファリング | Canonical パイプ | 長所 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| モデル オーケストレーション (機械学習) | データ サイエンティスト | Azure Machine Learning パイプライン | Kubeflow パイプライン | データ -> モデル | 分散、キャッシュ、コード優先、再利用 | 
| データのオーケストレーション (データ準備) | データ エンジニア | [Azure Data Factory のパイプライン](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | データ -> データ | 厳密に型指定された移動、データ中心のアクティビティ |
| コードとアプリのオーケストレーション (CI/CD) | アプリ開発者/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | コード + モデル -> アプリとサービス | ほとんどのオープンで柔軟なアクティビティのサポート、承認キュー、ゲートを使用したフェーズ | 

## <a name="what-can-machine-learning-pipelines-do"></a>機械学習パイプラインでできること

Azure Machine Learning パイプラインは、独立して実行できる完全な機械学習タスクのワークフローです。 サブタスクは、パイプライン内の一連のステップとしてカプセル化されます。 Azure Machine Learning パイプラインは、Python スクリプトを呼び出すのと同じくらいシンプルなものにすることができるため、ほとんどのことを実行 "_できます_"。 パイプラインは、次のような機械学習タスクに重点を置く "_必要があります_"。

+ インポート、検証、クリーンアップ、マンジングと変換、正規化、ステージングなどのデータ準備
+ 引数のパラメーター化、ファイルパス、ログ/レポートの構成などのトレーニング構成
+ 効率的かつ反復的なトレーニングと検証。 特定のデータ サブセット、異なるハードウェア コンピューティング リソース、分散処理、および進行状況の監視を指定することで効率が向上する場合があります。
+ バージョン管理、スケーリング、プロビジョニング、アクセス制御などのデプロイ

個別のステップにより、複数のデータ サイエンティストが、コンピューティング リソースを過剰に使用することなく、同じパイプラインで同時に作業を行うことができます。 また、個別のステップにより、ステップごとに異なるコンピューティングの種類/サイズを使用することが容易になります。

パイプラインが設計された後には、多くの場合、パイプラインのトレーニング ループを中心にさらに多くの微調整が行われます。 パイプラインを再実行すると、更新されたトレーニング スクリプトなど、再実行する必要があるステップにジャンプして実行されます。 再実行する必要がないステップはスキップされます。 

パイプラインでは、異なるタスクには異なるハードウェアを使用することを選択できます。 Azure により、使用するさまざまな[コンピューティング ターゲット](concept-azure-machine-learning-architecture.md)の調整が行われるため、中間データはダウンストリームのコンピューティング ターゲットへとシームレスに流れます。

Azure portal または[ワークスペースのランディング ページ (プレビュー)](https://ml.azure.com) で直接、[パイプライン実験のメトリックを追跡](./how-to-track-experiments.md)できます。 パイプラインを発行した後は、任意のプラットフォームまたはスタックからパイプラインを再実行できる REST エンドポイントを構成できます。

つまり、パイプラインを使用することで、機械学習のライフサイクルの複雑なタスクをすべて支援できます。 その他の Azure パイプライン テクノロジには、それぞれ固有の長所があります。 [Azure Data Factory パイプライン](../data-factory/concepts-pipelines-activities.md)はデータの操作に優れ、[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) は、継続的な統合とデプロイに適切なツールです。 ただし、機械学習に重点を置いている場合は、Azure Machine Learning パイプラインがワークフローのニーズに最適な選択肢となる可能性があります。 

### <a name="analyzing-dependencies"></a>依存関係の分析

多くのプログラミング エコシステムは、リソース、ライブラリ、またはコンパイルの依存関係を調整するツールを備えています。 一般に、これらのツールでは、ファイルのタイムスタンプを使用して依存関係を計算します。 ファイルが変更されると、ファイルとその依存のみが更新 (ダウンロード、再コンパイル、またはパッケージ化) されます。 Azure Machine Learning パイプラインでは、この概念が拡張されます。 パイプラインでは、従来のビルド ツールと同様に、ステップ間の依存関係が計算され、必要な再計算のみが実行されます。 

ただし、Azure Machine Learning パイプラインでの依存関係の分析は、単純なタイムスタンプよりも洗練されています。 すべてのステップを別のハードウェアやソフトウェア環境で実行できます。 データの準備は時間のかかるプロセスですが、強力な GPU を搭載したハードウェア上で実行する必要はありません。特定のステップでは、OS 固有のソフトウェアが必要な場合があります。分散トレーニングを使用する必要がある場合があります。 

Azure Machine Learning により、パイプラインのステップ間のすべての依存関係が自動的に調整されます。 このオーケストレーションには、Docker イメージのスピンアップとスピンダウン、コンピューティング リソースのアタッチとデタッチ、一貫した自動的な方法でステップ間のデータ移動などが含まれます。

### <a name="coordinating-the-steps-involved"></a>必要なステップの調整

`Pipeline` オブジェクトを作成して実行すると、次のような高レベルのステップが実行されます。

+ 各ステップでは、次に対する要件がサービスによって計算されます。
    + ハードウェアのコンピューティング リソース
    + OS リソース (Docker イメージ)
    + ソフトウェア リソース (Conda/virtualenv の依存関係)
    + データ入力 
+ サービスによりステップ間の依存関係が決定され、その結果、動的な実行グラフになります
+ 実行グラフの各ノードが実行されると、次のことが起こります。
    + サービスによって、必要なハードウェアおよびソフトウェア環境が構成されます (既存のリソースが再利用されることがあります)
    + ステップが実行され、含まれている `Experiment` オブジェクトにログと監視情報が提供されます
    + ステップが完了すると、その出力は次のステップへの入力として準備されるか、ストレージに書き込まれます
    + 不要になったリソースは、完了してデタッチされます

![パイプラインのステップ](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Python SDK を使用したパイプラインの構築

[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) では、パイプラインは `azureml.pipeline.core` モジュールで定義されている Python オブジェクトです。 [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) オブジェクトには、1 つ以上の [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) オブジェクトの順序付けられたシーケンスが含まれています。 `PipelineStep` クラスは抽象であり、実際のステップは [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)、[PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)、[DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) などのサブクラスになります。 [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) クラスでは、パイプライン間で共有できる、再利用可能な一連のステップが保持されます。 `Pipeline` は `Experiment` の一部として実行されます。

Azure Machine Learning パイプラインは、Azure Machine Learning ワークスペースに関連付けられています。パイプライン ステップは、そのワークスペース内で使用可能なコンピューティング先に関連付けられています。 詳細については、「[Azure portal 内で Azure Machine Learning ワークスペースを作成および管理する](./how-to-manage-workspace.md)」または「[Azure Machine Learning でのコンピューティング先とは](./concept-compute-target.md)」を参照してください。

### <a name="a-simple-python-pipeline"></a>単純な Python パイプライン

このスニペットは、`Pipeline` を作成して実行するために必要なオブジェクトと呼び出しを示しています。

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

スニペットは、共通の Azure Machine Learning オブジェクト、`Workspace`、`Datastore`、[ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)、および `Experiment` で開始されます。 次に、コードによって `input_data` と `prepped_data_path` を保持するオブジェクトが作成されます。 `input_data` は [FileDataset](/python/api/azureml-core/azureml.data.filedataset) のインスタンスであり、`prepped_data_path` は [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) のインスタンスです。 `OutputFileDatasetConfig` の場合、既定の動作はパス `/dataset/{run-id}/{output-name}` の下で `workspaceblobstore` データストアに出力をコピーすることです。`run-id` は実行の ID であり、`output-name` は、開発者が指定していない場合に自動生成される値です。

データ準備コード (表示されていません) によって、区切りファイルが `prepped_data_path` に書き込まれます。 データ準備ステップからのこれらの出力は、`prepped_data` としてトレーニング ステップに渡されます。 

配列 `steps` には、2 つの `PythonScriptStep` (`dataprep_step` と `train_step`) が保持されています。 Azure Machine Learning によって、`prepped_data` のデータ依存関係が分析され、`train_step` の前に `dataprep_step` が実行されます。 

次に、このコードによって `Pipeline` オブジェクト自体がインスタンス化され、ワークスペースとステップの配列が渡されます。 `experiment.submit(pipeline)` を呼び出すと、Azure ML パイプラインの実行が開始されます。 `wait_for_completion()` を呼び出すと、パイプラインが終了するまでブロックされます。 

パイプラインをデータに接続する方法の詳細については、[Azure Machine Learning でのデータ アクセス](concept-data.md)と [ML パイプライン ステップでのデータの移動 (Python)](how-to-move-data-in-out-of-pipelines.md)に関するページを参照してください。 

## <a name="building-pipelines-with-the-designer"></a>デザイナーを使用したパイプラインの構築

ビジュアル デザイン サーフェスを好む開発者は、Azure Machine Learning デザイナーを使用してパイプラインを作成できます。 このツールにアクセスするには、ワークスペースのホームページで **[デザイナー]** を選択します。  デザイナーでは、デザイン サーフェスにステップをドラッグ アンド ドロップできます。 

パイプラインを視覚的にデザインすると、ステップの入力と出力が視覚的に表示されます。 データ接続をドラッグ アンド ドロップして、パイプラインのデータフローをすばやく理解し、変更することができます。

![Azure Machine Learning デザイナーの例](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>主な利点

機械学習ワークフローのパイプラインを使用する主な利点を次に示します。

|主な利点|説明|
|:-------:|-----------|
|**無人&nbsp;実行**|複数のステップを無人で確実に並列実行または順次実行するようにスケジュールします。 データの準備とモデリングには数日または数週間かかる可能性があります。パイプラインを使用すると、このプロセスの実行中に他のタスクに専念できます。 |
|**異種コンピューティング**|スケーラブルな異種コンピューティング リソースとストレージの場所全体で確実に調整された複数のパイプラインを使用します。 パイプラインの個々のステップをさまざまなコンピューティング ターゲット上 (HDInsight、GPU Data Science VM、Databricks など) で実行することによって、利用可能なコンピューティング リソースを効率的に使用できます。|
|**再利用**|再トレーニングやバッチ スコアリングなどの特定のシナリオ向けにパイプライン テンプレートを作成します。 単純な REST 呼び出しを使用して外部システムから発行されたパイプラインをトリガーします。|
|**追跡とバージョン管理**|反復処理しながらデータや結果パスを手動で追跡するのではなく、パイプライン SDK を使用して、データ ソース、入力、および出力に明示的に名前を付けてバージョン管理します。 また、生産性を向上させるために、スクリプトとデータを個別に管理することもできます。|
| **モジュール性** | 懸念事項の範囲を分離し、変更を分離することで、ソフトウェアをより高い品質でより迅速に進化させることができます。 | 
|**コラボレーション**|パイプラインを使用すると、データ科学者は、機械学習の設計プロセスのすべてのリージョンで共同作業を行うことができます。さらに、パイプラインの複数のステップに同時に取り組むこともできます。|

## <a name="next-steps"></a>次のステップ

Azure Machine Learning パイプラインは、初期の開発段階で価値の提供を開始する強力な機能です。 チームとプロジェクトの規模が大きくなるにつれてその価値は増加します。 この記事では、Azure Machine Learning Python SDK でパイプラインを指定し、Azure で調整する方法について説明しました。 いくつかの単純なソース コードについて説明し、使用可能な `PipelineStep` クラスをいくつか紹介しました。 Azure Machine Learning パイプラインを使用するタイミングと、Azure でそれらが実行される方法について理解しておく必要があります。 

+ [最初のパイプラインを作成する](./how-to-create-machine-learning-pipelines.md)方法を学習します。

+ [大量のデータに対してバッチ予測を実行する](tutorial-pipeline-batch-scoring-classification.md )方法を学習します。

+ [パイプライン コア](/python/api/azureml-pipeline-core/)と[パイプライン ステップ](/python/api/azureml-pipeline-steps/)については、SDK のリファレンス ドキュメントを参照してください。

+ [Azure Machine Learning パイプライン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)を紹介する Jupyter ノートブックの例を試します。 [ノートブックを実行してこのサービスを調べる](samples-notebooks.md)方法を学習します。