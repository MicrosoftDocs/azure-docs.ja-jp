---
title: OpenCensus Python を使用した Azure Application Insights における依存関係の追跡 | Microsoft Docs
description: Python アプリの依存関係呼び出しを OpenCensus Python で監視します。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: a8f673295236d60ec6681bbfaee1201a4659674b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585708"
---
# <a name="track-dependencies-with-opencensus-python"></a>OpenCensus Python を使用した依存関係の追跡

依存関係は、アプリケーションによって呼び出される外部コンポーネントです。 依存関係データは、OpenCensus Python とそのさまざまな統合を使用して収集されます。 その後、データは Azure Monitor の Application Insights に `dependencies` テレメトリとして送信されます。

まず、最新の [OpenCensus Python SDK](./opencensus-python.md) を使用して Python アプリケーションをインストルメント化します。

## <a name="in-process-dependencies"></a>プロセス内の依存関係

OpenCensus Python SDK for Azure Monitor を使用すると、"プロセス内" の依存関係テレメトリ (アプリケーション内で発生する情報とロジック) を送信できます。 プロセス内の依存関係には、分析で `INPROC` として `type` フィールドがあります。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>"requests" 統合を使用した依存関係

OpenCensus の `requests` 統合を使用して、送信要求を追跡します。

[PyPI](https://pypi.org/project/opencensus-ext-requests/) から `opencensus-ext-requests` をダウンロードしてインストールし、トレース統合に追加します。 Python の [requests](https://pypi.org/project/requests/) ライブラリを使用して送信された要求が追跡されます。

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>"httplib" 統合を使用した依存関係

OpenCensus の `httplib` 統合を使用して送信要求を追跡します。

[PyPI](https://pypi.org/project/opencensus-ext-httplib/) から `opencensus-ext-httplib` をダウンロードしてインストールし、トレース統合に追加します。 [http.client](https://docs.python.org/3.7/library/http.client.html) を使用して送信された要求は、Python3 または [httplib](https://docs.python.org/2/library/httplib.html) for Python2 を使用して追跡されます。

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>"django" 統合を使用した依存関係

OpenCensus の `django` 統合を使用して、送信 Diango 要求を追跡します。

> [!NOTE]
> 追跡される送信 Django 要求は、データベースに対して行われた呼び出しのみです。 Django アプリケーションに対して行われた要求については、[受信要求](./opencensus-python-request.md#tracking-django-applications)に関する記事を参照してください。

[PyPI](https://pypi.org/project/opencensus-ext-django/) から `opencensus-ext-django` をダウンロードしてインストールし、次の行を Django `settings.py` ファイルの `MIDDLEWARE` セクションに追加します。

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

追加の構成を指定することもできます。詳細については、[カスタマイズ](https://github.com/census-instrumentation/opencensus-python#customization)に関するページを参照してください。

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>"mysql" 統合を使用した依存関係

OpenCensus の `mysql` 統合を使用して、MYSQL 依存関係を追跡します。 この統合は、[mysql コネクタ ](https://pypi.org/project/mysql-connector-python/) ライブラリをサポートしています。

[PyPI](https://pypi.org/project/opencensus-ext-mysql/) から `opencensus-ext-mysql` をダウンロードしてインストールし、次の行をコードに追加します。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>"pymysql" 統合を使用した依存関係

OpenCensus の `pymysql` 統合を使用して、PyMySQL 依存関係を追跡します。

[PyPI](https://pypi.org/project/opencensus-ext-pymysql/) から `opencensus-ext-pymysql` をダウンロードしてインストールし、次の行をコードに追加します。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>"postgresql" 統合を使用した依存関係

OpenCensus の `postgresql` 統合を使用して、PostgreSQL 依存関係を追跡します。 この統合は、[psycopg2](https://pypi.org/project/psycopg2/) ライブラリをサポートしています。

[PyPI](https://pypi.org/project/opencensus-ext-postgresql/) から `opencensus-ext-postgresql` をダウンロードしてインストールし、次の行をコードに追加します。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>"pymongo" 統合を使用した依存関係

OpenCensus の `pymongo` 統合を使用して、MongoDB 依存関係を追跡します。 この統合は、[pymongo](https://pypi.org/project/pymongo/) ライブラリをサポートしています。

[PyPI](https://pypi.org/project/opencensus-ext-pymongo/) から `opencensus-ext-pymongo` をダウンロードしてインストールし、次の行をコードに追加します。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>"sqlalchemy" 統合を使用した依存関係

OpenCensus の `sqlalchemy` 統合を使用して、SQLAlchemy を使用した依存関係を追跡します。 この統合により、基になるデータベースに関係なく、[sqlalchemy ](https://pypi.org/project/SQLAlchemy/) パッケージの使用状況が追跡されます。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>次のステップ

* [アプリケーション マップ](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [Search](./diagnostic-search.md)
* [Log (Analytics) のクエリ](../logs/log-query-overview.md)
* [トランザクションの診断](./transaction-diagnostics.md)

