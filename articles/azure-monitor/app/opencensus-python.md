---
title: Azure Monitor を使用して Python アプリを監視する | Microsoft Docs
description: OpenCensus Python を Azure Monitor に接続する手順について説明します
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: d22174b269ba9cea3b2c9cb9de2b5521df2786fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704414"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Python アプリケーション用に Azure Monitor をセットアップします

Azure Monitor は、[OpenCensus](https://opencensus.io) との統合により、Python アプリケーションの分散トレース、メトリック収集、およびログ記録をサポートします。 この記事では、OpenCensus for Python を設定し、監視データを Azure Monitor に送信するプロセスについて説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- Python のインストール。 この記事では [Python 3.7.0](https://www.python.org/downloads/release/python-370/) を使用しますが、他のバージョンでも軽微な変更で使用できる可能性があります。 SDK でサポートされているのは、Python v2.7 および v3.4 から v3.7 のみです。
- Application Insights の[リソース](./create-new-resource.md)を作成します。 リソースの独自のインストルメンテーション キー (ikey) が割り当てられます。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Monitor 用の OpenCensus Python SDK を使用したインストルメント化

OpenCensus Azure Monitor エクスポーターをインストールします。

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure` コマンドは、Python インストール用に `PATH` 環境変数が設定されていることを前提としています。 この変数を構成していない場合は、Python の実行可能ファイルの場所を示す完全なディレクトリ パスを指定する必要があります。 その結果次のようなコマンドになります。`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`

SDK では 3 つの Azure Monitor エクスポーターを使用して、さまざまな種類のテレメトリを Azure Monitor に送信します。 それらはトレース、メトリック、ログです。 これらのテレメトリの種類の詳細については、[データ プラットフォームの概要](../data-platform.md)に関するページを参照してください。 次の手順に従い、3 つのエクスポーターを使用してこれらのテレメトリの種類を送信します。

## <a name="telemetry-type-mappings"></a>テレメトリの種類のマッピング

Azure Monitor に表示されるテレメトリの種類にマップされる、OpenCensus のエクスポーターを以下に示します。

| 可観測性の重要な要素 | Azure Monitor のテレメトリの種類    | 説明                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| ログ                    | traces、exceptions、customEvents   | ログ テレメトリ、例外テレメトリ、イベント テレメトリ |
| メトリック                 | customMetrics、performanceCounters | カスタム メトリック、パフォーマンス カウンター                |
| トレース                 | requests、dependencies             | 受信要求、送信要求                |

### <a name="logs"></a>ログ

1. まず、いくつかのローカル ログ データを生成しましょう。

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. コードにより、値の入力を継続的に求められます。 入力したすべての値に対してログ エントリが生成されます。

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. 値の入力はデモとしても有用ですが、最終的にはログ データを Azure Monitor に送信するようにします。 接続文字列をエクスポーターに直接渡します。 または、`APPLICATIONINSIGHTS_CONNECTION_STRING` 環境変数で指定することができます。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. エクスポーターによって、ログ データが Azure Monitor に送信されます。 データは `traces` で確認できます。 

    > [!NOTE]
    > このコンテキストでは、`traces` は `tracing` と同じではありません。 ここでは、`traces` は、`AzureLogHandler` を利用するときに Azure Monitor に表示されるテレメトリの種類を表します。 しかし、`tracing` は OpenCensus の概念を表し、[分散トレース](./distributed-tracing.md)に関連します。

    > [!NOTE]
    > ルート ロガーは警告レベルで構成されます。 つまり、重要度が低いログを送信しても無視され、Azure Monitor に送信されません。 詳細については、[こちらのドキュメント](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)を参照してください。

1. また、*extra* キーワード引数内のご自分のログ メッセージに、custom_dimensions フィールドを使用してカスタム プロパティを追加することもできます。 これらのプロパティは、Azure Monitor に `customDimensions` のキーと値のペアとして表示されます。
    > [!NOTE]
    > この機能が動作するためには、custom_dimensions フィールドにディクショナリを渡す必要があります。 他の型の引数を渡すと、それらはロガーによって無視されます。

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Django アプリケーション用にログ記録を構成する

Django アプリケーション用に、上記のようなアプリケーション コードでログ記録を明示的に構成することも、Django のログ記録構成で指定することもできます。 このコードは、Django 設定の構成に使用する任意のファイルに入れることができます。 Django 設定を構成する方法については、「[Django settings](https://docs.djangoproject.com/en/3.0/topics/settings/)」 (Django 設定) を参照してください。 ログ記録構成の詳細については、[Django のログ記録](https://docs.djangoproject.com/en/3.0/topics/logging/)に関するページを参照してください。

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

必ず、構成で指定したものと同じ名前のロガーを使用してください。

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>例外を送信する

OpenCensus Python では、`exception` テレメトリの追跡と送信が自動的には行われません。 これらは、Python ログ ライブラリ経由の例外を使用し、`AzureLogHandler` を通じて送信されます。 通常のログと同様、カスタム プロパティを追加することができます。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
例外は明示的にログに記録する必要があるため、ハンドルされない例外をどのようにログに記録するかはユーザーしだいです。 OpenCensus では、例外のテレメトリが明示的にログに記録されている限り、これをユーザーがどのように行うかについては制限はありません。

#### <a name="send-events"></a>送信イベント

代わりに `AzureEventHandler` を使用することを除き、`trace` テレメトリを送信するのとまったく同じ方法で `customEvent` テレメトリを送信できます。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>サンプリング

OpenCensus のサンプリングの詳細については、[OpenCensus でのサンプリング](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)に関するページを参照してください。

#### <a name="log-correlation"></a>ログの関連付け

トレース コンテキスト データを使用してログを強化する方法の詳細については、OpenCensus Python [ログの統合](./correlation.md#log-correlation)に関するページを参照してください。

#### <a name="modify-telemetry"></a>テレメトリの変更

追跡されたテレメトリを Azure Monitor に送信する前に変更する方法の詳細については、OpenCensus Python の[テレメトリ プロセッサ](./api-filtering-sampling.md#opencensus-python-telemetry-processors)に関するセクションを参照してください。


### <a name="metrics"></a>メトリック

OpenCensus.stats では 4 つの集計メソッドがサポートされますが、Azure Monitor に対するサポートは部分的に提供されます。

- **Count:** 測定ポイントのカウント数。 値は累積的であり、増加のみ可能です。再起動時に 0 にリセットできます。 
- **Sum:** 測定ポイントの合計。 値は累積的であり、増加のみ可能です。再起動時に 0 にリセットできます。 
- **LastValue:** 最後に記録された値を保持し、他のすべてを削除します。
- **Distribution:** 測定ポイントのヒストグラム分布です。 このメソッドは、**Azure エクスポーターではサポートされていません**。

### <a name="count-aggregation-example"></a>カウント集計の例

1. まず、いくつかのローカル メトリック データを生成しましょう。 ユーザーが **Enter** キーを選択した回数を追跡する単純なメトリックを作成します。

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. コードを実行すると、**Enter** キーを選択するように繰り返し求められます。 **Enter** キーが選択された回数を追跡するメトリックが作成されます。 各エントリに対して、値がインクリメントされ、メトリック情報がコンソールに表示されます。 この情報には、メトリックが更新されたときの現在の値と現在のタイムスタンプが含まれます。

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. 値の入力はデモとしても有用ですが、最終的にはメトリック データを Azure Monitor に送信するようにします。 接続文字列をエクスポーターに直接渡します。 または、`APPLICATIONINSIGHTS_CONNECTION_STRING` 環境変数で指定することができます。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. エクスポーターによって、一定の間隔でメトリック データが Azure Monitor に送信されます。 既定値は 15 秒ごとです。 1 つのメトリックを追跡しているので、このメトリック データは、それに含まれる値およびタイムスタンプに関係なく、間隔ごとに送信されます。 値は累積的であり、増加のみ可能です。再起動時に 0 にリセットできます。 `customMetrics` でデータを見つけることができますが、`customMetrics` プロパティの valueCount、valueSum、valueMin、valueMax、および valueStdDev は有効に使用されていません。

#### <a name="performance-counters"></a>パフォーマンス カウンター

既定では、メトリック エクスポーターによって、Azure Monitor に一連のパフォーマンス カウンターが送信されます。 これを無効にするには、メトリックス エクスポーターのコンストラクターで `enable_standard_metrics` フラグを `False` に設定します。

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

現在、これらのパフォーマンス カウンターが送信されます。

- Available Memory (bytes) (使用可能なメモリ (バイト))
- CPU Processor Time (percentage) (CPU プロセッサ時間 (%))
- Incoming Request Rate (per second) (受信要求率 (1 秒あたり))
- Incoming Request Average Execution Time (milliseconds) (受信要求の平均実行時間 (ミリ秒))
- Process CPU Usage (percentage) (プロセスの CPU 使用率 (%))
- Process Private Bytes (bytes) (プロセスのプライベート バイト (バイト))

これらのメトリックは `performanceCounters` で確認できます。 これらのパフォーマンス カウンターの詳細については、[パフォーマンス カウンター](./performance-counters.md)に関するページを参照してください。

#### <a name="modify-telemetry"></a>テレメトリの変更

追跡されたテレメトリを Azure Monitor に送信する前に変更する方法については、OpenCensus Python の[テレメトリ プロセッサ](./api-filtering-sampling.md#opencensus-python-telemetry-processors)に関するセクションを参照してください。

### <a name="tracing"></a>トレース

> [!NOTE]
> OpenCensus では、`tracing` は[分散トレース](./distributed-tracing.md)を表します。 `AzureExporter` によって `requests` および `dependency` テレメトリが Azure Monitor に送信されます。

1. まずいくつかのトレース データをローカルで生成しましょう。 Python IDLE か任意のエディターで、次のコードを入力します。

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. このコードを実行すると、値を入力するように繰り返し求められます。 各エントリに対して、値がシェルに出力されます。 OpenCensus Python モジュールによって、`SpanData` の対応する部分が生成されます。 OpenCensus プロジェクトでは、[複数範囲のツリーとしてトレース](https://opencensus.io/core-concepts/tracing/)が定義されます。
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. 値の入力はデモンストレーションのために役立ちますが、最終的には `SpanData` を Azure Monitor に出力するようにします。 接続文字列をエクスポーターに直接渡します。 または、`APPLICATIONINSIGHTS_CONNECTION_STRING` 環境変数で指定することができます。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Python スクリプトを実行すると引き続き値を入力するように求められますが、値のみがシェルに出力されます。 作成された `SpanData` は Azure Monitor に送信されます。 送信されたスパン データは `dependencies` で確認できます。 送信要求の詳細については、OpenCensus Python の「[依存関係](./opencensus-python-dependency.md)」を参照してください。
受信要求の詳細については、OpenCensus Python の「[要求](./opencensus-python-request.md)」を参照してください。

#### <a name="sampling"></a>サンプリング

OpenCensus のサンプリングの詳細については、[OpenCensus でのサンプリング](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)に関するページを参照してください。

#### <a name="trace-correlation"></a>トレースの相関付け

トレース データにおけるテレメトリの相関付けの詳細については、OpenCensus Python の「[テレメトリの相関付け](./correlation.md#telemetry-correlation-in-opencensus-python)」を参照してください。

#### <a name="modify-telemetry"></a>テレメトリの変更

追跡されたテレメトリを Azure Monitor に送信する前に変更する方法の詳細については、OpenCensus Python の[テレメトリ プロセッサ](./api-filtering-sampling.md#opencensus-python-telemetry-processors)に関するセクションを参照してください。

## <a name="configure-azure-monitor-exporters"></a>Azure Monitor エクスポーターを構成する

次に示すように、OpenCensus をサポートする Azure Monitor エクスポーターには 3 つの異なる種類があります。 それぞれにより、Azure Monitor に異なる種類のテレメトリが送信されます。 各エクスポーターによって送信されるテレメトリの種類を確認する場合は、以下の一覧を参照してください。

各エクスポーターによって、コンストラクターを介して渡される構成に対する同じ引数が受け入れられます。 それぞれの詳細については、以下を参照してください。

- `connection_string`:Azure Monitor リソースへの接続に使用される接続文字列。 `instrumentation_key` よりも優先されます。
- `enable_standard_metrics`:`AzureMetricsExporter` に使用されます。 [パフォーマンス カウンター](../essentials/app-insights-metrics.md#performance-counters) メトリックを Azure Monitor に自動的に送信するように、エクスポーターに通知します。 既定値は `True` です。
- `export_interval`:エクスポートの頻度を秒単位で指定するために使用されます。
- `instrumentation_key`:Azure Monitor リソースへの接続に使用されるインストルメンテーション キー。
- `logging_sampling_rate`:`AzureLogHandler` に使用されます。 ログをエクスポートするためのサンプリング レート [0,1.0] を指定します。 既定値は 1.0 です。
- `max_batch_size`:一度にエクスポートされるテレメトリの最大サイズを指定します。
- `proxies`:Azure Monitor にデータを送信するために使用する一連のプロキシを指定します。 詳細については、「[proxies](https://requests.readthedocs.io/en/master/user/advanced/#proxies)」 (プロキシ) を参照してください。
- `storage_path`:ローカル ストレージ フォルダーが存在する場所へのパス (未送信のテレメトリ)。 `opencensus-ext-azure` v 1.0.3 以降の既定のパスは、OS の一時ディレクトリ + `opencensus-python` + `your-ikey` です。 v1.0.3 より前の既定のパスは、$USER + `.opencensus` + `.azure` + `python-file-name` です。

## <a name="view-your-data-with-queries"></a>クエリを使用してデータを表示する

アプリケーションから送信されたテレメトリ データは、 **[Logs(Analytics)]** (ログ (分析)) タブを使用して表示できます。

![[Logs(Analytics)] (ログ (分析)) が赤い枠線で囲まれた概要ウィンドウのスクリーンショット](./media/opencensus-python/0010-logs-query.png)

**[アクティブ]** の一覧で次のようにします。

- Azure Monitor のトレース エクスポーターで送信されたテレメトリについては、`requests` の下に受信した要求が表示されます。 送信または処理中の要求は、`dependencies` の下に表示されます。
- Azure Monitor メトリック エクスポーターを使用して送信されたテレメトリの場合、送信されたメトリックは `customMetrics` の下に表示されます。
- Azure Monitor ログ エクスポーターを使用して送信されたテレメトリの場合、ログは `traces` の下に表示されます。 例外は `exceptions` の下に表示されます。

クエリとログの使用方法の詳細については、「[Azure Monitor のログ](../logs/data-platform-logs.md)」を参照してください。

## <a name="learn-more-about-opencensus-for-python"></a>OpenCensus for Python に関する詳細情報

* [GitHub の OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [カスタマイズ](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub の Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 統合](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor サンプル アプリケーション](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>次のステップ

* [受信要求の追跡](./opencensus-python-dependency.md)
* [送信要求の追跡](./opencensus-python-request.md)
* [アプリケーション マップ](./app-map.md)
* [エンドツーエンドのパフォーマンスの監視](../app/tutorial-performance.md)

### <a name="alerts"></a>警告

* [可用性テスト](./monitor-web-app-availability.md): サイトが Web で表示できることを確認するためのテストを作成します。
* [スマート診断](./proactive-diagnostics.md): これらのテストは自動的に実行されます。セットアップするために何かをする必要はありません。 アプリの要求が失敗する割合が異常な場合に通知します。
* [メトリック アラート](../alerts/alerts-log.md): メトリックがしきい値を超えた場合に警告するようにアラートを設定 します。 メトリック アラートはカスタム メトリックで設定し、コード化してアプリに組み込むことができます。

