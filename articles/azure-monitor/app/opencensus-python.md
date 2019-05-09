---
title: Azure Application Insights による OpenCensus Python のトレース | Microsoft Docs
description: OpenCensus Python のトレースをローカル フォワーダーおよび Application Insights と接続する手順について説明します。
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 22e58f31e2f891eb09c3d42a01763c68cdcd11a8
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696185"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Python から分散トレースを収集する (プレビュー)

Application Insights は、[OpenCensus](https://opencensus.io) と Microsoft の新しい[ローカル フォワーダー](./../../azure-monitor/app/opencensus-local-forwarder.md)との統合により、Python アプリケーションの分散トレースをサポートするようになりました。 この記事では、OpenCensus for Python を設定し、トレース データを Application Insights に取得するプロセスについて順を追って説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションが必要です。
- Python がインストールされている必要があります。この記事では [Python 3.7.0](https://www.python.org/downloads/) を使用していますが、以前のバージョンでも軽微な調整で使用できる可能性があります。
- 手順に従って[ローカル フォワーダーを Windows サービスとしてインストールします](./../../azure-monitor/app/opencensus-local-forwarder.md)

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-application-insights-resource"></a>Application Insights リソースを作成する

まず、インストルメンテーション キー (ikey) を生成する Application Insights リソースを作成する必要があります。 ikey を使用して、OpenCensus でインストルメント化されたアプリケーションから Application Insights に分散トレースを送信するようにローカル フォワーダーを構成します。   

1. **[リソースの作成]** > **[開発者ツール]** > **[Application Insights]** の順に選択します。

   ![Application Insights リソースの追加](./media/opencensus-python/0001-create-resource.png)

   構成ボックスが表示されたら、次の表を使用して入力フィールドに入力します。

    | 設定        | 値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **アプリケーションの種類** | 全般 | 監視しているアプリの種類 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **場所** | 米国東部 | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="configure-local-forwarder"></a>ローカル フォワーダーを構成する

1. **[概要]** > **[Essentials]** を選択し、アプリケーションの**インストルメンテーション キー**をコピーします。

   ![インストルメンテーション キーのスクリーンショット](./media/opencensus-python/0003-instrumentation-key.png)

2. `LocalForwarder.config` ファイルを編集し、インストルメンテーション キーを追加します。 [前提条件](./../../azure-monitor/app/opencensus-local-forwarder.md)の手順に従った場合、このファイルは `C:\LF-WindowsServiceHost` にあります。

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. アプリケーションの**ローカル フォワーダー** サービスを再開します。

## <a name="opencensus-python-package"></a>OpenCensus Python パッケージ

1. コマンド ラインから pip または pipenv を使用して Python 用 Open Census パッケージをインストールします。

    ```python
    python -m pip install opencensus
    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` は、Python インストール用に PATH 環境変数が設定されていることを前提としています。 これを構成していない場合は、Python の実行可能ファイルの場所を示す完全なディレクトリ パスを指定する必要があります。その結果次のようなコマンドになります。`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`

2. まずいくつかのトレース データをローカルで生成しましょう。 Python IDLE か任意のエディターで、次のコードを入力します。

    ```python
    from opencensus.trace.tracer import Tracer
    
    def main():
        while True:
            valuePrompt()
    
    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    
    ```

3. このコードを実行すると、値を入力するように繰り返し求められます。 入力ごとに値はシェルに出力され、**SpanData** の対応する部分が OpenCensus Python Module によって生成されます。 OpenCensus プロジェクトでは、[_複数範囲のツリーとしてトレース_](https://opencensus.io/core-concepts/tracing/)が定義されます。
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. これはデモの目的では役立ちますが、最終的には**ローカル フォワーダー サービス**で取得して Application Insights に送信できる方法で SpanData を送信したいと考えています。 前の手順のコードを次のように変更します。

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.trace.exporters.ocagent import trace_exporter
    from opencensus.trace import tracer as tracer_module
    
    import os
    
    def main():        
        while True:
            valuePrompt()
    
    def valuePrompt():
        export_LocalForwarder = trace_exporter.TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))
        
        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    ```

5. 上記のモジュールを保存して実行しようとすると、`grpc` に対して `ModuleNotFoundError` を受け取る可能性があります。 この場合は、以下を実行して [grpcio package](https://pypi.org/project/grpcio/) をインストールします。

    ```
    python -m pip install grpcio
    ```

6. 上の手順から Python スクリプトを実行しても値を入力するように求められますが、今度は値のみがシェルに出力されています。

7. **ローカル フォワーダー**がトレースを取得していることを確認するには、`LocalForwarder.config` ファイルを確認します。 [前提条件](https://docs.microsoft.com/azure/application-insights/local-forwarder)の手順に従った場合は、`C:\LF-WindowsServiceHost` にあります。

    ログ ファイルの以下の画像では、追加した 2 つ目のスクリプトを実行する前に、エクスポーター `OpenCensus input BatchesReceived` は 0 だったことがわかります。 更新されたスクリプトの実行を開始すると、増分された `BatchesReceived` が入力した値の数に等しくなります。
    
    ![新しい App Insights リソースのフォーム](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure portal で Application Insights の **[概要]** ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。 **[Live Metric Stream]\(ライブ メトリック ストリーム\)** を選択します。

   ![ライブ メトリック ストリームが赤い枠線で書き込まれた概要ウィンドウのスクリーンショット](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 2 つ目の Python スクリプトをもう一度実行し、値の入力を開始すると、ローカル フォワーダー サービスから Application Insights に送信されたライブ トレース データが表示されます。

   ![パフォーマンス データが表示されたライブ メトリック ストリームのスクリーンショット](./media/opencensus-python/0006-stream.png)

3. **[概要]** ページに戻り、**[アプリケーション マップ]** を選択して、アプリケーション コンポーネント間の依存関係と呼び出しのタイミングを視覚的に表したレイアウトを表示します。

    ![基本的なアプリケーション マップのスクリーンショット](./media/opencensus-python/0007-application-map.png)

    1 つのメソッド呼び出しのみをトレースしていたため、このアプリケーション マップはあまり興味深い内容ではありません。 ただし、アプリケーション マップをスケーリングして、分散性がはるかに高いアプリケーションを視覚化することができます。

   ![アプリケーション マップ](media/opencensus-python/application-map.png)

4. **[パフォーマンスの調査]** を選択して詳細なパフォーマンス分析を実行し、パフォーマンス低下の根本原因を判断します。

    ![パフォーマンス ウィンドウのスクリーンショット](./media/opencensus-python/0008-performance.png)

5. **[サンプル]** を選択し、右側のウィンドウに表示されるいずれかのサンプルをクリックすると、エンドツーエンド トランザクションの詳細エクスペリエンスが起動します。 このサンプル アプリには単一のイベントのみが表示されますが、より複雑なアプリケーションでは、個々のイベントのコール スタックのレベルまでエンドツーエンドのトランザクションを探索できます。

     ![エンドツーエンドのトランザクション インターフェイスのスクリーンショット](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>OpenCensus for Python のトレース

OpenCensus for Python をローカル フォワーダーおよび Application Insights と接続する手順の基本についてのみ説明しました。 公式の使用ガイダンスでは、次のようなより高度なトピックを扱っています。

* [サンプラー](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Flask の統合](https://opencensus.io/api/python/trace/usage.html#flask)
* [Django の統合](https://opencensus.io/api/python/trace/usage.html#django)
* [MySQL の統合](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>次の手順

* [OpenCensus Python の使用ガイド](https://opencensus.io/api/python/trace/usage.html)
* [アプリケーション マップ](./../../azure-monitor/app/app-map.md)
* [エンドツーエンドのパフォーマンスの監視](./../../azure-monitor/learn/tutorial-performance.md)
