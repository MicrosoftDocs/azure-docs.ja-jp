---
title: 機械学習パイプライン YAML
titleSuffix: Azure Machine Learning
description: YAML ファイルを使用して機械学習パイプラインを定義する方法について説明します。 YAML パイプラインの定義は、Azure CLI 用の機械学習拡張機能で使用されます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2a92fa8fd242482585ab3785e99f8239548ce369
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868343"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>YAML で機械学習パイプラインを定義する

[YAML](https://yaml.org/) で機械学習パイプラインを定義する方法について説明します。 Azure CLI 用の機械学習拡張機能を使用している場合、パイプライン関連のコマンドの多くは、パイプラインを定義した YAML ファイルを必要とします。

次の表に、YAML でパイプラインを定義する際に現在サポートされているものと、サポートされていないものを示します。

| ステップの種類 | サポート対象かどうか |
| ----- | :-----: |
| PythonScriptStep | はい |
| ParallelRunStep | はい |
| AdlaStep | はい |
| AzureBatchStep | はい |
| DatabricksStep | はい |
| DataTransferStep | はい |
| AutoMLStep | いいえ |
| HyperDriveStep | いいえ |
| ModuleStep | はい |
| MPIStep | いいえ |
| EstimatorStep | いいえ |

## <a name="pipeline-definition"></a>パイプラインの定義

パイプラインの定義では次のキーを使用します。これらのキーは、[Pipelines](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline) クラスに対応します。

| YAML キー | 説明 |
| ----- | ----- |
| `name` | パイプラインの説明。 |
| `parameters` | パイプラインへのパラメーター。 |
| `data_reference` | 実行中にデータを使用できるようにする方法と場所を定義します。 |
| `default_compute` | パイプラインのすべてのステップが実行される既定のコンピューティング先。 |
| `steps` | パイプラインで使用されるステップ。 |

## <a name="parameters"></a>パラメーター

`parameters` セクションでは次のキーを使用します。これらのキーは、[PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter) クラスに対応します。

| YAML キー | 説明 |
| ---- | ---- |
| `type` | パラメーターの値の型。 有効な型は、`string`、`int`、`float`、`bool`、または `datapath` です。 |
| `default` | 既定値。 |

各パラメーターには名前が付けられます。 たとえば、次の YAML スニペットでは、`NumIterationsParameter`、`DataPathParameter`、`NodeCountParameter` という名前の 3 つのパラメーターが定義されています。

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>データ参照

`data_references` セクションでは次のキーを使用します。これらのキーは、[DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference) に対応します。

| YAML キー | 説明 |
| ----- | ----- |
| `datastore` | 参照するデータストア。 |
| `path_on_datastore` | データ参照用バッキング ストレージの相対パス。 |

各データ参照は 1 つのキーに格納されます。 たとえば、次の YAML スニペットでは、`employee_data` という名前のキーに格納されているデータ参照が定義されています。

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>手順

ステップは、計算環境とその環境で実行すべきファイルを定義します。 ステップの種類を定義するには、`type` キーを使用します。

| ステップの種類 | 説明 |
| ----- | ----- |
| `AdlaStep` | Azure Data Lake Analytics を使用して U-SQL スクリプトを実行します。 [AdlaStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) クラスに対応します。 |
| `AzureBatchStep` | Azure Batch を使用してジョブを実行します。 [AzureBatchStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) クラスに対応します。 |
| `DatabricsStep` | Databricks ノートブック、Python スクリプト、または JAR を追加します。 [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) クラスに対応します。 |
| `DataTransferStep` | ストレージ オプション間でデータを転送します。 [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) クラスに対応します。 |
| `PythonScriptStep` | Python スクリプトを実行します。 [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) クラスに対応します。 |
| `ParallelRunStep` | Python スクリプトを実行して、大量のデータを非同期的かつ並列的に処理します。 [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) クラスに対応します。 |

### <a name="adla-step"></a>ADLA ステップ

| YAML キー | 説明 |
| ----- | ----- |
| `script_name` | U-SQL スクリプトの名前 (`source_directory` からの相対名)。 |
| `compute` | このステップで使用する Azure Data Lake コンピューティング先。 |
| `parameters` | パイプラインへの[パラメーター](#parameters)。 |
| `inputs` | 入力は、[InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding)、[DataReference](#data-reference)、[PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference)、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata)、[Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29)、[DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)、または [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) のいずれかです。 |
| `outputs` | 出力は、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) か [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) のどちらかです。 |
| `source_directory` | スクリプトやアセンブリなどを格納するディレクトリ。 |
| `priority` | 現在のジョブに使用する優先度の値。 |
| `params` | 名前と値の組のディクショナリ。 |
| `degree_of_parallelism` | このジョブに使用する並列処理の次数。 |
| `runtime_version` | Data Lake Analytics エンジンのランタイム バージョン。 |
| `allow_reuse` | 同じ設定でもう一度実行されたときに、ステップで前の結果を再利用するかどうかを決定します。 |

次に、ADLA ステップの定義のサンプルを示します。

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch ステップ

| YAML キー | 説明 |
| ----- | ----- |
| `compute` | このステップで使用する Azure Batch コンピューティング先。 |
| `inputs` | 入力は、[InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding)、[DataReference](#data-reference)、[PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference)、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata)、[Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29)、[DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)、または [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) のいずれかです。 |
| `outputs` | 出力は、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) か [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) のどちらかです。 |
| `source_directory` | モジュール バイナリ、実行可能ファイル、アセンブリなどを格納するディレクトリ。 |
| `executable` | このジョブの一部として実行されるコマンドまたは実行可能ファイルの名前。 |
| `create_pool` | ジョブを実行する前にプールを作成するかどうかを示すブール値のフラグ。 |
| `delete_batch_job_after_finish` | ジョブが完了した後に Batch アカウントからそのジョブを削除するかどうかを示すブール値のフラグ。 |
| `delete_batch_pool_after_finish` | ジョブが完了した後にプールを削除するかどうかを示すブール値のフラグ。 |
| `is_positive_exit_code_failure` | タスクが正数のコードで終了した場合にジョブが失敗するかどうかを示すブール値のフラグ。 |
| `vm_image_urn` | `create_pool` が `True`の場合、VM では `VirtualMachineConfiguration` が使用されます。 |
| `pool_id` | ジョブが実行されるプールの ID。 |
| `allow_reuse` | 同じ設定でもう一度実行されたときに、ステップで前の結果を再利用するかどうかを決定します。 |

次に、Azure Batch ステップの定義のサンプルを示します。

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks ステップ

| YAML キー | 説明 |
| ----- | ----- |
| `compute` | このステップで使用する Azure Databricks コンピューティング先。 |
| `inputs` | 入力は、[InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding)、[DataReference](#data-reference)、[PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference)、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata)、[Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29)、[DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)、または [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) のいずれかです。 |
| `outputs` | 出力は、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) か [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) のどちらかです。 |
| `run_name` | この実行の Databricks 内の名前。 |
| `source_directory` | スクリプトやその他のファイルを格納するディレクトリ。 |
| `num_workers` | Databricks 実行クラスターのワーカーの静的な数。 |
| `runconfig` | `.runconfig` ファイルへのパス。 このファイルは [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) クラスの YAML 表現です。 このファイルの構造について詳しくは、[runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) を参照してください。 |
| `allow_reuse` | 同じ設定でもう一度実行されたときに、ステップで前の結果を再利用するかどうかを決定します。 |

次に、Databricks ステップのサンプルを示します。

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>データ転送ステップ

| YAML キー | 説明 |
| ----- | ----- |
| `compute` | このステップで使用する Azure Data Factory コンピューティング先。 |
| `source_data_reference` | データ転送操作のソースとして機能する入力接続。 サポートされている値は、[InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding)、[DataReference](#data-reference)、[PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference)、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata)、[Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29)、[DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)、または [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) です。 |
| `destination_data_reference` | データ転送操作の転送先として機能する入力接続。 サポートされている値は、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) と [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) です。 |
| `allow_reuse` | 同じ設定でもう一度実行されたときに、ステップで前の結果を再利用するかどうかを決定します。 |

次に、データ転送ステップのサンプルを示します。

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python スクリプト ステップ

| YAML キー | 説明 |
| ----- | ----- |
| `inputs` | 入力は、[InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding)、[DataReference](#data-reference)、[PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference)、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata)、[Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29)、[DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)、または [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) のいずれかです。 |
| `outputs` | 出力は、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) か [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) のどちらかです。 |
| `script_name` | Python スクリプトの名前 (`source_directory` からの相対名)。 |
| `source_directory` | スクリプトや Conda 環境などを格納するディレクトリ。 |
| `runconfig` | `.runconfig` ファイルへのパス。 このファイルは [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) クラスの YAML 表現です。 このファイルの構造について詳しくは、[runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) を参照してください。 |
| `allow_reuse` | 同じ設定でもう一度実行されたときに、ステップで前の結果を再利用するかどうかを決定します。 |

次に、Python スクリプト ステップのサンプルを示します。

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="parallel-run-step"></a>並列実行ステップ

| YAML キー | 説明 |
| ----- | ----- |
| `inputs` | 入力には、[Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29)、[DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)、または [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) を指定できます。 |
| `outputs` | 出力は、[PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) か [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) のどちらかです。 |
| `script_name` | Python スクリプトの名前 (`source_directory` からの相対名)。 |
| `source_directory` | スクリプトや Conda 環境などを格納するディレクトリ。 |
| `parallel_run_config` | `parallel_run_config.yml` ファイルへのパス。 このファイルは [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) クラスの YAML 表現です。 |
| `allow_reuse` | 同じ設定でもう一度実行されたときに、ステップで前の結果を再利用するかどうかを決定します。 |

次に、並列実行ステップのサンプルを示します。

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>複数のステップを含むパイプライン 

| YAML キー | 説明 |
| ----- | ----- |
| `steps` | 1 つ以上の PipelineStep 定義のシーケンス。 1 つのステップの `outputs` の `destination` キーが次のステップの `inputs` への `source` キーになることに注意してください。| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>スケジュール

パイプラインのスケジュールを定義する場合、そのスケジュールがデータストアによってトリガーされるか、一定間隔で定期的に実行されるかを選択できます。 スケジュールの定義に使用されるキーは次のとおりです。

| YAML キー | 説明 |
| ----- | ----- |
| `description` | スケジュールの説明。 |
| `recurrence` | スケジュールが定期的に実行される場合、繰り返し設定を格納します。 |
| `pipeline_parameters` | パイプラインに必要なすべてのパラメーター。 |
| `wait_for_provisioning` | スケジュールのプロビジョニングが完了するまで待機するかどうかを指定します。 |
| `wait_timeout` | タイムアウトになるまで待機する秒数。 |
| `datastore_name` | 変更または追加された BLOB を監視するデータストア。 |
| `polling_interval` | 変更または追加された BLOB のポーリング間隔 (分)。 既定値:5 分。 データストアのスケジュールでのみサポートされます。 |
| `data_path_parameter_name` | 変更された BLOB パスで設定するデータ パス パイプライン パラメーターの名前。 データストアのスケジュールでのみサポートされます。 |
| `continue_on_step_failure` | 送信された PipelineRun 内の 1 つのステップが失敗した場合に、他のステップの実行を続行するかどうかを指定します。 指定した場合、パイプラインの `continue_on_step_failure` 設定はオーバーライドされます。
| `path_on_datastore` | 省略可能。 変更または追加された BLOB を監視するデータストア上のパス。 パスはそのデータストアのコンテナーの下にあるため、スケジュールで監視される実際のパスは container/`path_on_datastore` です。 存在しない場合は、データストア コンテナーが監視されます。 `path_on_datastore` のサブフォルダーに加えられた追加や変更は監視されません。 データストアのスケジュールでのみサポートされます。 |

次に、データストアによってトリガーされるスケジュールの定義のサンプルを示します。

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

**定期的なスケジュール** を定義する場合は、`recurrence` の下で次のキーを使用します。

| YAML キー | 説明 |
| ----- | ----- |
| `frequency` | スケジュールが繰り返される頻度。 有効な値は、`"Minute"`、`"Hour"`、`"Day"`、`"Week"`、または `"Month"` です。 |
| `interval` | スケジュールが実行される頻度。 整数値は、スケジュールがもう一度実行されるまで待機する時間単位の数です。 |
| `start_time` | スケジュールの開始時刻。 この値の文字列形式は `YYYY-MM-DDThh:mm:ss` です。 開始時刻を指定しない場合、最初のワークロードはすぐに実行され、将来のワークロードはスケジュールに基づいて実行されます。 開始時刻が過去の場合、最初のワークロードは、計算された次の実行時に実行されます。 |
| `time_zone` | 開始時刻のタイム ゾーン。 タイム ゾーンを指定しない場合、UTC が使用されます。 |
| `hours` | `frequency` が `"Day"` または `"Week"` の場合、パイプラインを実行する時刻 (時) として、0 から 23 までの 1 つ以上の整数をコンマ区切りで指定できます。 使用できるのは、`time_of_day` か、`hours` と `minutes` のみです。 |
| `minutes` | `frequency` が `"Day"` または `"Week"` の場合、パイプラインを実行する時刻 (分) として、0 から 59 までの 1 つ以上の整数をコンマ区切りで指定できます。 使用できるのは、`time_of_day` か、`hours` と `minutes` のみです。 |
| `time_of_day` | `frequency` が `"Day"` または `"Week"` の場合、スケジュールを実行する時刻を指定できます。 この値の文字列形式は `hh:mm` です。 使用できるのは、`time_of_day` か、`hours` と `minutes` のみです。 |
| `week_days` | `frequency` が `"Week"` の場合、スケジュールを実行する 1 つ以上の曜日をコンマ区切りで指定できます。 有効な値は、`"Monday"`、`"Tuesday"`、`"Wednesday"`、`"Thursday"`、`"Friday"`、`"Saturday"`、`"Sunday"`です。 |

次に、定期的なスケジュールの定義のサンプルを示します。

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>次のステップ

[Azure Machine Learning 用の CLI 拡張機能を使用する](reference-azure-machine-learning-cli.md)方法について学習する。