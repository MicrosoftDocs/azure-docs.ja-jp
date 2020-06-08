---
title: Azure Functions の概要
description: Azure Functions を使用して非同期ワークロードを最適化する方法を簡単に説明します。
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "77621000"
---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions の概要

Azure Functions を使用すると、アプリケーションのインフラストラクチャについて気にすることなく、小さなコード ("Functions") を実行することができます。 Azure Functions では、アプリケーションを大規模に実行し続けるために必要な最新のサーバーがすべて、クラウド インフラストラクチャによって提供されます。

Functions は特殊なイベントによって "トリガー" されます。 [サポートされるトリガー](./functions-triggers-bindings.md)としては、データの変更に対する応答や、メッセージへの応答、スケジュールに基づく実行、HTTP 要求の結果などがあります。

数あるサービスを対象としたコードを直接記述することもできますが、バインドを使用すれば、他のサービスとの統合を効率化することができます。 バインディングにより、[Azure やサードパーティのさまざまなサービスに対して宣言型の手法でアクセス](./functions-triggers-bindings.md)することができます。

## <a name="features"></a>特徴

Azure Functions のいくつかの重要な機能を次に示します。

- **サーバーレス アプリケーション**: Functions を使用すると、Microsoft Azure 上で[サーバーレス](https://azure.microsoft.com/solutions/serverless/)のアプリケーションを開発できます。

- **言語の選択**:　[C#、Java、JavaScript、Python、PowerShell](supported-languages.md) のいずれかの言語を自由に選んで Functions を記述できます。

- **従量課金制の価格モデル**: コードの実行に要した時間に対してのみ課金されます。 [価格に関するセクション](#pricing)にある従量課金ホスティング プラン オプションを確認してください。  

- **独自の依存関係を使用**: Functions では NuGet と NPM がサポートされるので、お気に入りのライブラリを使用できます。

- **セキュリティの統合**: Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントなどの OAuth プロバイダーにより、HTTP によってトリガーされる Functions を保護できます。

- **簡単な手順で統合**: Azure サービスや SaaS (サービスとしてのソフトウェア) プランと簡単に統合できます。

- **柔軟な開発**: 継続的インテグレーションを設定し、[GitHub](../app-service/scripts/cli-continuous-deployment-github.md) や [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) などの[サポート対象開発ツール](../app-service/deploy-local-git.md)を使ってコードをデプロイすることができます。

- **ステートフルなサーバーレス アーキテクチャ**: [Durable Functions](durable/durable-functions-overview.md) を使用してサーバーレス アプリケーションをオーケストレーションできます。

- **オープンソース**: Functions ランタイムはオープン ソースであり、[GitHub で入手](https://github.com/azure/azure-webjobs-sdk-script)できます。

## <a name="what-can-i-do-with-functions"></a>Functions でできること

Functions は、一括データの処理、システムの統合、モノのインターネット (IoT) の操作、単純な API とマイクロサービスの構築に適した優れたソリューションです。

主なシナリオに対応した一連のテンプレートが用意されています。その例を次に示します。

- **HTTP**: [HTTP 要求](functions-create-first-azure-function.md)に基づいてコードを実行します。

- **Timer**: [あらかじめ定義した時刻に実行](./functions-create-scheduled-function.md)するようコードをスケジュールします。

- **Azure Cosmos DB**: [Azure Cosmos DB の新規ドキュメントと変更ドキュメント](./functions-create-cosmos-db-triggered-function.md)を処理します。

- **Blob Storage**: [Azure Storage Blob の新規ドキュメントと変更ドキュメント](./functions-create-storage-blob-triggered-function.md)を処理します。

- **Queue storage**: [Azure Storage キュー メッセージ](./functions-create-storage-queue-triggered-function.md)に応答します。

- **Event Grid**: [サブスクリプションとフィルターを介して Azure Event Grid イベント](../event-grid/resize-images-on-storage-blob-upload-event.md)に応答します。

- **イベント ハブ**: [大量の Azure Event Hubs イベント](./functions-bindings-event-hubs.md)に応答します。

- **Service Bus キュー**: [Service Bus キュー メッセージに応答](./functions-bindings-service-bus.md)することで、別の Azure サービスやオンプレミス サービスに接続します。

- **Service Bus トピック**: [Service Bus トピック メッセージ](./functions-bindings-service-bus.md)に応答することで、別の Azure サービスやオンプレミス サービスに接続します。

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Azure Functions の価格

Azure Functions には、3 種類の価格プランがあります。 ニーズに最適なものを以下から選択します。

- **従量課金プラン**: Azure には、必要なコンピューティング リソースがすべて用意されています。 リソースの管理について考慮する必要がなく、コードを実行した時間に応じた料金のみを支払います。

- **Premium プラン**: 常にオンライン状態で即時に応答する準備が整った、事前ウォーミングされたインスタンスをいくつか自分で指定します。 Functions が実行されると、追加で必要となるコンピューティング リソースが Azure から提供されます。 常時実行される事前ウォーミングされたインスタンスに加え、Azure によってアプリがスケールインまたはスケールアウトされたときに追加で使用されるインスタンスが支払いの対象となります。

- **App Service プラン**: Functions を Web アプリと同様に実行できます。 App Service を他のアプリケーションに使用している場合、追加コストを負担することなく、同じプランで Functions を実行できます。

ホスティング プランの詳細については、[Azure Functions のホスティング プランの比較](functions-scale.md)に関するページをご覧ください。 価格の詳細については、 [Functions の価格のページ](https://azure.microsoft.com/pricing/details/functions/)をご覧ください。

## <a name="next-steps"></a>次の手順

- [初めての Azure Functions の作成](functions-create-first-function-vs-code.md)  
  [Visual Studio Code](functions-create-first-function-vs-code.md)、[コマンドライン](functions-create-first-azure-function-azure-cli.md)、または [Azure portal](functions-create-first-azure-function.md) で始めましょう。

- [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  Azure Functions ランタイムに関する詳細な技術情報と、Functions のコーディングやトリガーおよびバインドの定義に関するリファレンスを提供します。

- [Azure Functions のスケーリング方法](functions-scale.md)  
  Azure Functions で利用できるサービス プラン (従量課金ホスティング プランを含む) と、適切なプランを選択する方法について説明します。

- [Azure App Service とは](../app-service/overview.md)  
  Azure Functions では、デプロイ、環境変数、診断などの主要な機能に Azure App Service を活用しています。
