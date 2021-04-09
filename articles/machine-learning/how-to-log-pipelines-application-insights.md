---
title: パイプラインのログ ファイルを監視して収集する
titleSuffix: Azure Machine Learning
description: トレーニングおよびバッチ スコアリングのパイプラインにログを追加し、Application Insights に記録された結果を表示します。
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7ed27eba66b3d18bed8017934fce85928b961392
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520049"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Application Insights でアラートとデバッグ用に機械学習パイプラインのログ ファイルを収集する


[OpenCensus](https://opencensus.io/quickstart/python/) の Python ライブラリを使用して、スクリプトから Application Insights にログをルーティングできます。 パイプラインの実行のログを 1 か所に集約することで、クエリを作成し、イシューを診断することができます。 Application Insights を使用すると、ログを経時的に追跡し、実行全体のパイプライン ログを比較することができます。

ログを 1 か所で保持すれば、例外とエラー メッセージの履歴がわかります。 Application Insights は Azure アラートと統合されるため、Application Insights のクエリに基づいてアラートを作成することもできます。

## <a name="prerequisites"></a>前提条件

* 手順に従って [Azure Machine Learning](./how-to-manage-workspace.md) ワークスペースを作成し、[最初のパイプラインを作成](./how-to-create-machine-learning-pipelines.md)します
* Azure Machine Learning SDK をインストールするための[開発環境を構成](./how-to-configure-environment.md)します。
* [OpenCensus の Azure Monitor エクスポーター](https://pypi.org/project/opencensus-ext-azure/)のパッケージをローカルにインストールします。
  ```python
  pip install opencensus-ext-azure
  ```
* [Application Insights インスタンス](../azure-monitor/app/opencensus-python.md)を作成します (このドキュメントには、リソースの接続文字列の取得に関する情報も含まれています)

## <a name="getting-started"></a>作業の開始

このセクションでは、Azure Machine Learning パイプラインから OpenCensus を使用する方法のみを説明します。 詳細なチュートリアルについては、「[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)」(OpenCensus の Azure Monitor エクスポーター) を参照してください

PythonScriptStep を Azure ML パイプラインに追加します。 opencensus-ext-azure での依存関係を使用して [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) を構成します。 `APPLICATIONINSIGHTS_CONNECTION_STRING` 環境変数を構成します。

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

`sample_step.py`という名前でファイルを作成します。 Application Insights にログをルーティングするために、AzureLogHandler クラスをインポートします。 また、Python ログ ライブラリもインポートする必要があります。

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

次に、AzureLogHandler を Python ロガーに追加します。

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>カスタム ディメンションを使用したログ
 
既定では Application Insights に転送されるログには、実行または実験を追跡するのに十分なコンテキストがありません。 イシューの診断のためにログを利用できるようにするには、追加のフィールドが必要です。 

これらのフィールドを追加するには、カスタム ディメンションを追加してログ メッセージにコンテキストを提供します。 1 つの例が、同じパイプライン実行の複数のステップにわたるログを表示したいユーザーがいる場合です。

カスタム ディメンションでは、キー値 (文字列、文字列として格納) のペアのディクショナリが作成されます。 その後、ディクショナリが Application Insights に送信され、クエリ結果に列として表示されます。 個々のディメンションは、[クエリ パラメーター](#additional-helpful-queries)として使用できます。

### <a name="helpful-context-to-include"></a>含めると役に立つコンテキスト

| フィールド                          | 理由/例                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 同じ parent_run_id を持つログのクエリを実行して、個々のステップを確認することなく、すべてのステップのログを経時的に確認できます                                        |
| step_id                        | 同じ step_id を持つログのクエリを実行して、狭い範囲のどこでイシューが発生したかを個々のステップまで確認できます                                                        |
| step_name                      | ログのクエリを実行して、経時的なステップのパフォーマンスを確認できます。 また、ポータル UI を確認することなく最近の実行の step_id を見つけることもできます                                          |
| experiment_name                | 複数のログのクエリを実行して、経時的な実験のパフォーマンスを確認できます。 また、ポータル UI を確認することなく最近の実行の parent_run_id または step_id を見つけることもできます                   |
| run_url                 | 調査のために直接実行に戻るリンクを指定できます。 |

**その他の便利なフィールド**

これらのフィールドには追加のコード インストルメンテーションが必要な場合があり、実行コンテキストでは提供されません。

| フィールド                   | 理由/例                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | CI/CD を使用してデプロイする場合、このフィールドでは、ステップおよびパイプライン ロジックを提供したコード バージョンにログを関連付けることができます。 このリンクは、イシューを診断したり、特定の特性 (ログ/メトリック値) を持つモデルを識別したりするのに役立ちます |
| run_type                       | 異なるモデルの種類、またはトレーニングとスコアリングの実行を区別できます |

### <a name="creating-a-custom-dimensions-dictionary"></a>カスタム ディメンション ディクショナリの作成

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus の Python ログに関する考慮事項

OpenCensus の AzureLogHandler は、Python ログを Application Insights にルーティングするために使用されます。 結果として、Python ログの微妙な差異を考慮する必要があります。 作成されたロガーには既定のログ レベルが設定され、そのレベル以上のログが表示されます。 Python のログ機能を使用するための参考資料には、[Logging Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) をお勧めします。

OpenCensus ライブラリには、`APPLICATIONINSIGHTS_CONNECTION_STRING` 環境変数が必要です。 プレーンテキストの接続文字列が渡されないように、環境変数をパイプライン パラメーターとして渡すのではなく、この環境変数を設定することをお勧めします。

## <a name="querying-logs-in-application-insights"></a>Application Insights のログのクエリ

Application Insights にルーティングされたログは、'トレース' または '例外' の下に表示されます。 パイプラインの実行が含まれるように時間枠を調整してください。

![Application Insights クエリ結果](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights の結果には、ログ メッセージとレベル、ファイル パス、およびコード行番号が表示されます。 また、含まれているカスタム ディメンションもすべて表示されます。 この画像では、customDimensions ディクショナリが、前の[コード サンプル](#creating-a-custom-dimensions-dictionary)のキーと値のペアを示しています。

### <a name="additional-helpful-queries"></a>その他の便利なクエリ

以下のクエリの中には 'customDimensions.Level' が使用されているものがあります。 これらの重大度レベルは、Python ログが最初に送信されたレベルに対応します。 その他のクエリ情報については、「[Azure Monitor ログクエリ](/azure/data-explorer/kusto/query/)」を参照してください。

| 使用事例                                                               | クエリ                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 特定のカスタム ディメンションのログ結果 (例 'parent_run_id') | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 過去 7 日間のすべてのトレーニング実行のログ結果                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 過去 7 日間の severityLevel エラーが発生したログ結果              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 過去 7 日間の severityLevel エラーが発生したログ結果の数     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>次の手順

Application Insights インスタンスに作成されたログは、クエリ結果に基づいて [Azure Monitor アラート](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on)の設定で使用できます。

また、クエリの結果を [Azure ダッシュボード](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query)に追加して、詳細な分析情報を得ることもできます。