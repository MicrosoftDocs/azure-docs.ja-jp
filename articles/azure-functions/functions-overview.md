---
title: Azure Functions の概要
description: 堅牢なサーバーレス アプリの作成に Azure Functions を活用する方法について説明します。
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 41b627259d84539c868f95eb3cf33db5dbdab52c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97862406"
---
# <a name="introduction-to-azure-functions"></a>Azure Functions の概要

Azure Functions は、記述するコードと管理するインフラストラクチャを減らし、コストを節約できるサーバーレス ソリューションです。 クラウド インフラストラクチャによって、アプリケーションの実行を維持するために必要な最新のリソースがすべて提供されるので、サーバーのデプロイや管理について心配する必要はありません。

ユーザーは最も重要なコードに集中でき、残りの部分は Azure Functions が処理します。<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

通常、システムは、一連の重大なイベントに対処できるように構築されます。 Web API の構築、データベースの変更への対応、IoT データ ストリームの処理、メッセージ キューの管理など、その目的が何であれ、どのアプリケーションにも、そうしたイベントが発生したときに、なんらかのコードを実行する手段が必要です。

このニーズを満たすために、Azure Functions は、2 つの有効な方法で "コンピューティングをオンデマンドで" 提供します。

まず、Azure Functions では、利用しやすいコード ブロックの中にシステムのロジックを実装することができます。 これらのコード ブロックを "関数" といいます。 重大なイベントに対応しなければならないときは、各種の関数をいつでも実行できます。

2 つ目は、要求の増大に伴って、Azure Functions は、必要な分のリソースと関数インスタンスを、必要な間だけ使用して需要を満たします。 要求が少なくなれば、余分なリソースとアプリケーション インスタンスは自動的に解放されます。

これらのコンピューティング リソースは一体どこから来るのでしょうか。 Azure Functions は、アプリケーションの需要を満たすうえで[必要な分だけのコンピューティング リソース](./functions-scale.md)を提供します。

オンデマンドでコンピューティング リソースを提供することは、Azure Functions の[サーバーレス コンピューティング](https://azure.microsoft.com/solutions/serverless/)の本質と言えます。

## <a name="scenarios"></a>シナリオ

多くの場合、関数は、[さまざまなクラウド サービスと連携](./functions-triggers-bindings.md)して多機能な実装を実現します。

以下に示したのは、Azure Functions の代表的なシナリオの例です。"_すべてのシナリオを網羅したものではありません_"。

| 目的 | 手段 |
| --- | --- |
| **Web API をビルドする** | [HTTP トリガー](./functions-bindings-http-webhook.md)を使用して Web アプリケーションのエンドポイントを実装します |
| **ファイルのアップロードを処理する** | [Blob Storage](./functions-bindings-storage-blob.md) のファイルがアップロードされたり変更されたりしたときにコードを実行します |
| **サーバーレス ワークフローを作成する** | [Durable Functions](./durable/durable-functions-overview.md) を使用して一連の関数のチェーンを作成します。 |
| **データベースの変更に対処する** | [Cosmos DB](./functions-bindings-cosmosdb-v2.md) でドキュメントが作成または更新されたときにカスタム ロジックを実行します |
| **スケジュールされたタスクを実行する** | [設定した時刻](./functions-bindings-timer.md)にコードを実行します |
| **信頼性の高いメッセージ キュー システムを作成する** | [Queue Storage](./functions-bindings-storage-queue.md)、[Service Bus](./functions-bindings-service-bus.md)、[Event Hubs](./functions-bindings-event-hubs.md) のいずれかを使用してメッセージ キューを処理します |
| **IoT データ ストリームを分析する** | [IoT デバイスからデータ](./functions-bindings-event-iot.md)を収集して処理します |
| **データをリアルタイムで処理する** | [Functions と SignalR](./functions-bindings-signalr-service.md) を使用して、その時点のデータに対応します |

実際に関数を作成するときは、次のオプションとリソースを利用できます。

- **優先する言語の使用**: [C#、Java、JavaScript、PowerShell、Python](./supported-languages.md) で関数を作成します。それ以外の言語については、[カスタム ハンドラー](./functions-custom-handlers.md)を使用します (実質上、言語の制限はありません)。

- **デプロイの自動化**: ツールベースのアプローチから外部パイプラインの使用まで、[無数のデプロイ オプション](./functions-deployment-technologies.md)が利用できます。

- **関数のトラブルシューティング**: [監視ツール](./functions-monitoring.md)と [テスト戦略](./functions-test-a-function.md)を使用して、アプリの分析情報を入手します。

- **柔軟な価格オプション**: [従量課金](./pricing.md)プランでは、関数が実行されている間だけ支払いが発生します。これに対し、[Premium](./pricing.md) プランと [App Service](./pricing.md) プランでは、特別なニーズに対応するための機能が提供されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [レッスン、サンプル、対話型チュートリアルで始める](./functions-get-started.md)
