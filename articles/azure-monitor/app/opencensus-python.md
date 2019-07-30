---
title: Azure Application Insights を使用して Python アプリを監視する | Microsoft Docs
description: OpenCensus Python を Application Insights に接続する手順について説明します
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541433"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Python から分散トレースを収集する (プレビュー)

Application Insights は、[OpenCensus](https://opencensus.io) との統合により、Python アプリケーションの分散トレースをサポートするようになりました。 この記事では、OpenCensus for Python を設定し、監視データを Application Insights に取得するプロセスについて順を追って説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションが必要です。
- Python がインストールされている必要があります。この記事では [Python 3.7.0](https://www.python.org/downloads/) を使用していますが、以前のバージョンでも軽微な調整で使用できる可能性があります。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-application-insights-resource"></a>Application Insights リソースを作成する

まず、インストルメンテーション キー (ikey) を生成する Application Insights リソースを作成する必要があります。 次に、この ikey を使用して、Application Insights にテレメトリ データを送信する OpenCensus SDK を構成します。

1. **[リソースの作成]**  >  **[開発者ツール]**  >  **[Application Insights]** の順に選択します。

   ![Application Insights リソースの追加](./media/opencensus-python/0001-create-resource.png)

   構成ボックスが表示されたら、次の表を使用して入力フィールドに入力します。

    | 設定        | 値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **Location** | East US | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="install-opencensus-azure-monitor-exporters"></a>OpenCensus Azure Monitor エクスポーターをインストールする

1. OpenCensus Azure Monitor エクスポーターをインストールします。

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` は、Python インストール用に PATH 環境変数が設定されていることを前提としています。 これを構成していない場合は、Python の実行可能ファイルの場所を示す完全なディレクトリ パスを指定する必要があります。その結果次のようなコマンドになります。`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`

2. まずいくつかのトレース データをローカルで生成しましょう。 Python IDLE か任意のエディターで、次のコードを入力します。

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

3. このコードを実行すると、値を入力するように繰り返し求められます。 入力ごとに値はシェルに出力され、**SpanData** の対応する部分が OpenCensus Python Module によって生成されます。 OpenCensus プロジェクトでは、[_複数範囲のツリーとしてトレース_](https://opencensus.io/core-concepts/tracing/)が定義されます。
    
    ```
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

4. これはデモとしても有用ですが、最終的には SpanData を Application Insights に送信するようにします。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
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
5. Python スクリプトを実行すると引き続き値を入力するように求められますが、今度は値のみがシェルに出力されます。

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure portal で Application Insights の **[概要]** ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。 **[Live Metric Stream]\(ライブ メトリック ストリーム\)** を選択します。

   ![ライブ メトリック ストリームが赤い枠線で書き込まれた概要ウィンドウのスクリーンショット](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. **[概要]** ページに戻り、 **[アプリケーション マップ]** を選択して、アプリケーション コンポーネント間の依存関係と呼び出しのタイミングを視覚的に表したレイアウトを表示します。

    ![基本的なアプリケーション マップのスクリーンショット](./media/opencensus-python/0007-application-map.png)

    1 つのメソッド呼び出しのみをトレースしていたため、このアプリケーション マップはあまり興味深い内容ではありません。 ただし、アプリケーション マップをスケーリングして、分散性がはるかに高いアプリケーションを視覚化することができます。

   ![アプリケーション マップ](media/opencensus-python/application-map.png)

3. **[パフォーマンスの調査]** を選択して詳細なパフォーマンス分析を実行し、パフォーマンス低下の根本原因を判断します。

    ![パフォーマンス ウィンドウのスクリーンショット](./media/opencensus-python/0008-performance.png)

4. **[サンプル]** を選択し、右側のウィンドウに表示されるいずれかのサンプルをクリックすると、エンドツーエンド トランザクションの詳細エクスペリエンスが起動します。 このサンプル アプリには単一のイベントのみが表示されますが、より複雑なアプリケーションでは、個々のイベントのコール スタックのレベルまでエンドツーエンドのトランザクションを探索できます。

     ![エンドツーエンドのトランザクション インターフェイスのスクリーンショット](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>OpenCensus for Python

* [カスタマイズ](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>次の手順

* [GitHub の OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [アプリケーション マップ](./../../azure-monitor/app/app-map.md)
* [エンドツーエンドのパフォーマンスの監視](./../../azure-monitor/learn/tutorial-performance.md)