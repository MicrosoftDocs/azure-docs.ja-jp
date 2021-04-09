---
title: Azure Functions で Python アプリのスループット パフォーマンスを向上させる
description: Python を使用して、負荷時に適切にスケーリングされる高パフォーマンスの Azure Functions アプリを開発する方法について説明します。
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786108"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Azure Functions で Python アプリのスループット パフォーマンスを向上させる

Python を使用して Azure Functions 向けに開発する場合、関数がどのように実行され、そのパフォーマンスが関数アプリのスケーリング方法にどのように影響するかを理解する必要があります。 高パフォーマンスのアプリを設計するときには、この必要性がより重要になります。 関数アプリを設計、作成、構成するときに考慮すべき主な要素は、水平スケーリングとスループット パフォーマンスの構成です。

## <a name="horizontal-scaling"></a>水平スケーリング
既定では、Azure Functions は、アプリケーションの負荷を自動的に監視し、必要に応じて Python 用に追加のホスト インスタンスを作成します。 Azure Functions では、さまざまなトリガーの種類の組み込みしきい値 (メッセージの経過時間や QueueTrigger のキュー サイズなど) を使用して、インスタンスを追加するタイミングを決定します。 これらのしきい値は、ユーザーが構成することはできません。 詳細については、「[Azure Functions でのイベント ドリブン スケーリング](event-driven-scaling.md)」を参照してください。

## <a name="improving-throughput-performance"></a>スループットのパフォーマンスの向上

ほとんどの Azure Functions アプリケーションの場合、既定の構成が適しています。 ただし、ワークロード プロファイルに基づく構成を採用することで、アプリケーションのスループットのパフォーマンスを向上させることができます。 最初の手順は、実行しているワークロードの種類を理解することです。

| ワークロードの種類 | 関数アプリの特性       | 例                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **I/O バウンド**     | • アプリで多くの同時呼び出しを処理する必要がある。<br>• アプリは、ネットワーク呼び出しやディスクの読み取り/書き込みなど、大量の I/O イベントを処理します。 | • Web API                                          |
| **CPU バウンド**     | • アプリでは、イメージのサイズ変更など、長時間実行される計算が行われます。<br>• アプリでは、データの変換が行われます。                                                | • データ処理<br>• 機械学習推論<br> |

 
実際の関数ワークロードは、通常、I/O バインドと CPU バインドが混在しているため、運用環境の現実的な負荷の下でアプリをプロファイリングする必要があります。


### <a name="performance-specific-configurations"></a>パフォーマンス固有の構成

関数アプリのワークロード プロファイルを理解したら、次は関数のスループット パフォーマンスを向上させるために使用できる構成を行います。

* [非同期](#async)
* [複数の言語ワーカー](#use-multiple-language-worker-processes)
* [言語ワーカー プロセス内のワーカーの最大数](#set-up-max-workers-within-a-language-worker-process)
* [イベント ループ](#managing-event-loop)
* [垂直スケーリング](#vertical-scaling)



#### <a name="async"></a>Async

[Python はシングルスレッド ランタイムである](https://wiki.python.org/moin/GlobalInterpreterLock)ため、Python のホスト インスタンスが処理できる関数呼び出しは、既定で一度に 1 つに限られます。 大量の I/O イベントを処理するアプリケーションや、I/O バインドされているアプリケーションでは、関数を非同期に実行することによってパフォーマンスを著しく向上させることができます。

関数を非同期に実行するには、`async def` ステートメントを使用します。これにより、[asyncio](https://docs.python.org/3/library/asyncio.html) を使用して関数が直接実行されます。

```python
async def main():
    await some_nonblocking_socket_io_op()
```
[aiohttp](https://pypi.org/project/aiohttp/) http クライアントを使用する HTTP トリガーを使用した関数の例を次に示します。

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


`async` キーワードのない関数は、自動的に ThreadPoolExecutor スレッド プールで実行されます。

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

関数を非同期的に実行する利点を最大限に活用するには、コード内で使用されている I/O 操作/ライブラリにも async を実装する必要があります。 非同期として定義されている関数で同期 I/O 操作を使用すると、全体的なパフォーマンスが **低下する可能性が あります**。 使用しているライブラリに非同期バージョンが実装されていない場合でも、アプリで[イベント ループを管理](#managing-event-loop)して、コードを非同期で実行することでメリットが得られる可能性があります。 

非同期パターンを実装したクライアント ライブラリのいくつかの例を次に示します。
- [aiohttp](https://pypi.org/project/aiohttp/) - asyncio の Http クライアント/サーバー 
- [Streams API](https://docs.python.org/3/library/asyncio-stream.html) -ネットワーク接続を操作するための高レベルの async/await 対応プリミティブ
- [Janus Queue](https://pypi.org/project/janus/) - Python 用のスレッドセーフな asyncio 対応キュー
- [pyzmq](https://pypi.org/project/pyzmq/) -ZeroMQ 用の Python バインド
 
##### <a name="understanding-async-in-python-worker"></a>Python Worker の async について

関数シグネチャの前に `async` を定義すると、Python はその関数をコルーチンとしてマークします。 コルーチンを呼び出すときに、イベント ループにタスクとしてスケジュールできます。 非同期関数で `await` を呼び出すと、イベント ループに継続が登録され、イベント ループは待機時間中に次のタスクを処理できるようになります。

Python Worker では、ワーカーは顧客の `async` 関数とイベント ループを共有し、複数の要求を同時に処理できます。 asyncio 互換ライブラリ ([aiohttp](https://pypi.org/project/aiohttp/)、[pyzmq](https://pypi.org/project/pyzmq/) など) を使用することを強くお勧めします。 これらの推奨事項を採用すると、同期的に実装されたライブラリと比較して、関数のスループットが大幅に向上します。

> [!NOTE]
>  関数が実装内で `await` なしで `async` として宣言されている場合、イベント ループがブロックされ、Python Worker が同時要求を処理できなくなるため、関数のパフォーマンスに深刻な影響を及ぼします。

#### <a name="use-multiple-language-worker-processes"></a>複数の言語ワーカー プロセスを使用する

既定では、すべての Functions ホスト インスタンスに 1 つの言語ワーカー プロセスがあります。 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) アプリケーション設定を使用して、ホストごとのワーカー プロセスの数を増やすことができます (最大 10)。 次に、Azure Functions は、これらのワーカー間で同時関数呼び出しを均等に分散しようとします。

CPU バインド アプリの場合、言語ワーカーの数は、関数アプリごとに使用できるコアの数と同じかそれ以上である必要があります。 詳細については、「[利用可能インスタンス SKU](functions-premium-plan.md#available-instance-skus)」を参照してください。 

I/O バインド アプリでも、使用可能なコア数を超えてワーカー プロセスの数を増やすことでメリットが得られる場合もあります。 ワーカー数を大きく設定し過ぎると、必要なコンテキスト切り替えの数が増えるため、全体的なパフォーマンスに影響する可能性があることに注意してください。 

FUNCTIONS_WORKER_PROCESS_COUNT は、需要に応じてアプリケーションをスケールアウトするときに Functions が作成する各ホストに適用されます。

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>言語ワーカー プロセス内のワーカーの最大数を設定する

async に関する[セクション](#understanding-async-in-python-worker)で説明したように、Python 言語ワーカーは関数と[コルーチン](https://docs.python.org/3/library/asyncio-task.html#coroutines)を別々に扱います。 コルーチンは、言語ワーカーが実行されている同じイベント ループ内で実行されます。 一方、関数呼び出しは、言語ワーカーによって管理されている [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor) 内でスレッドとして実行されます。

[PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) アプリケーション設定を使用して、同期関数を実行する際に許可される最大ワーカー数の値を設定できます。 この値により、ThreadPoolExecutor オブジェクトの `max_worker` 引数が設定されます。これにより、Python は最大 `max_worker` スレッドのプールを使用して、非同期で呼び出しを実行できます。 `PYTHON_THREADPOOL_THREAD_COUNT` は、Functions ホストが作成する各ワーカーに適用されます。Python は、新しいスレッドを作成するか、アイドル状態の既存のスレッドを再利用するかを決定します。 Python の古いバージョン (`3.8`、`3.7`、`3.6`) では、`max_worker` 値は 1 に設定されています。 Python バージョン `3.9` では、`max_worker` は `None` に設定されています。

CPU にバインドされたアプリの場合、この設定を小さい数値にしておきます。1 から始めて、ワークロードを試しながら増やしていきます。 この提案は、コンテキスト切り替えに費やす時間を減らし、CPU にバインドされたタスクを終了できるようにすることを目的としています。

I/O にバインドされたアプリの場合、各呼び出しで動作するスレッドの数を増やすことで、スループットが大幅に向上します。 Python の既定値 (コア数 + 4) から始め、実際のスループット値に基づいて調整することをお勧めします。

混合ワークロード アプリの場合、スループットを最大化するには、`FUNCTIONS_WORKER_PROCESS_COUNT` と `PYTHON_THREADPOOL_THREAD_COUNT` の両方の構成のバランスを取る必要があります。 関数アプリが何に最も多くの時間を費やしているかを理解するために、アプリをプロファイリングし、その動作に応じた値を設定することをお勧めします。 FUNCTIONS_WORKER_PROCESS_COUNT アプリケーション設定の詳細については、こちらの[セクション](#use-multiple-language-worker-processes)も参照してください。

> [!NOTE]
>  これらの推奨事項は、HTTP によってトリガーされる関数とトリガーされない関数の両方に適用されますが、関数アプリから期待されるパフォーマンスを得るために、HTTP によってトリガーされない関数では、トリガー固有の他の構成を調整することが必要な場合があります。 この詳細については、こちらの[記事](functions-best-practices.md)をご覧ください。


#### <a name="managing-event-loop"></a>イベント ループを管理する

asyncio 互換のサードパーティ製ライブラリを使用します。 ニーズを満たすサードパーティ製ライブラリがない場合は、Azure Functions でイベント ループを管理することもできます。 イベント ループを管理すると、コンピューティング リソース管理の柔軟性が向上します。また、同期 I/O ライブラリをコルーチンにラップすることも可能になります。

組み込みの **asyncio** ライブラリを使用した [コルーチンとタスク](https://docs.python.org/3/library/asyncio-task.html)および [イベント ループ](https://docs.python.org/3.8/library/asyncio-eventloop.html)について説明している有用な Python 公式ドキュメントが多数あります。

例として、次の [requests](https://github.com/psf/requests) ライブラリを見てみましょう。このコード スニペットでは、**asyncio** ライブラリを使用して `requests.get()` メソッドをコルーチンにラップし、SAMPLE_URL への複数の Web 要求を同時に実行します。


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>垂直方向のスケーリング
特に CPU にバインドされた操作で処理ユニットを増やす場合、仕様が高い Premium プランにアップグレードすることで、これを実現できる可能性があります。 より高度な処理ユニットを使用すると、使用可能なコアの数に応じてワーカー プロセス数を調整し、並列処理の次数を増やすことができます。 

## <a name="next-steps"></a>次のステップ

Python による Azure Functions 開発の詳細については、次のリソースを参照してください。

* [Azure Functions の Python 開発者向けガイド](functions-reference-python.md)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)

