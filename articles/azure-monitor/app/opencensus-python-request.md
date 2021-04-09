---
title: OpenCensus Python を使用した Azure Application Insights における受信要求の追跡 | Microsoft Docs
description: Python アプリの要求呼び出しを OpenCensus Python で監視します。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585677"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>OpenCensus Python を使用した受信要求の追跡

受信要求データは、OpenCensus Python とそのさまざまな統合を使用して収集されます。 一般的な Web フレームワーク `django`、`flask` および `pyramid` 上に構築された Web アプリケーションに送信された受信要求データを追跡します。 その後、データは Azure Monitor の Application Insights に `requests` テレメトリとして送信されます。

まず、最新の [OpenCensus Python SDK](./opencensus-python.md) を使用して Python アプリケーションをインストルメント化します。

## <a name="tracking-django-applications"></a>Django アプリケーションの追跡

1. [PyPI](https://pypi.org/project/opencensus-ext-django/) から `opencensus-ext-django` をダウンロードしてインストールし、`django` ミドルウェアでアプリケーションをインストルメント化します。 `django` アプリケーションに送信された受信要求が追跡されます。

2. `settings.py` ファイルの `MIDDLEWARE` に `opencensus.ext.django.middleware.OpencensusMiddleware` を含めます。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. `settings.py` の `OPENCENSUS` に AzureExporter が正しく構成されていることを確認します。 追跡したくない URL からの要求については、その URL を `EXCLUDELIST_PATHS` に追加します。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Flask アプリケーションの追跡

1. [PyPI](https://pypi.org/project/opencensus-ext-flask/) から `opencensus-ext-flask` をダウンロードしてインストールし、`flask` ミドルウェアでアプリケーションをインストルメント化します。 `flask` アプリケーションに送信された受信要求が追跡されます。

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. `app.config` を使用して `flask` アプリケーションを構成することもできます。 追跡したくない URL からの要求については、その URL を `EXCLUDELIST_PATHS` に追加します。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Pyramid アプリケーションの追跡

1. [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) から `opencensus-ext-django` をダウンロードしてインストールし、`pyramid` tween でアプリケーションをインストルメント化します。 `pyramid` アプリケーションに送信された受信要求が追跡されます。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. コードで直接 `pyramid` tween を構成できます。 追跡したくない URL からの要求については、その URL を `EXCLUDELIST_PATHS` に追加します。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>FastAPI アプリケーションの追跡

OpenCensus は FastAPI の拡張機能を備えていません。 独自の FastAPI ミドルウェアを記述するには、次の手順を実行します。

1. 次の依存関係が必要です。 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. [FastAPI ミドルウェア](https://fastapi.tiangolo.com/tutorial/middleware/)を追加します。 span kind サーバーを必ず設定するようにしてください (`span.span_kind = SpanKind.SERVER`)。

3. アプリケーションを実行します。 FastAPI アプリケーションに対する呼び出しは、自動的に追跡され、テレメトリが Azure Monitor に直接記録される必要があります。

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>次のステップ

* [アプリケーション マップ](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [Search](./diagnostic-search.md)
* [Log (Analytics) のクエリ](../logs/log-query-overview.md)
* [トランザクションの診断](./transaction-diagnostics.md)

