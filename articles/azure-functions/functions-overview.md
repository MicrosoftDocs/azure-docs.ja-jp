---
title: Azure Functions の概要 | Microsoft Docs
description: Azure Functions を使用して非同期ワークロードを最適化する方法を簡単に説明します。
services: functions
documentationcenter: na
author: mattchenderson
manager: jeconnoc
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 4fbe3a5d308559a4ac9e1a781f3a4f385c767f90
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348196"
---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions の概要  
Azure Functions は、小規模なコード ("関数") をクラウドで手軽に実行できるソリューションです。 目の前の問題に必要なコードだけを記述すればよく、アプリケーション全体や、コードを実行するインフラストラクチャのことを考える必要がありません。 Functions は開発の生産性をさらに向上させることができ、またユーザーは C#、Java、JavaScript、Python、PHP などの任意の開発言語を使用できます。 コードが実行された時間に対してだけ料金を支払えばよく、必要に応じてスケールできます。 Azure Functions を使用すると、Microsoft Azure で[サーバー不要](https://azure.microsoft.com/solutions/serverless/)のアプリケーションを作成できます。

このトピックでは、Azure Functions の概要について説明します。 Functions の利用をすぐに開始する場合は、「 [初めての Azure 関数の作成](functions-create-first-azure-function.md)」を参照してください。 Azure Functions の詳細な技術情報をお探しの場合は、 [開発者向けリファレンス](functions-reference.md)を参照してください。

## <a name="features"></a>機能
Functions の主要な機能を次に示します。

* **言語の選択** - C#、Java、Javascript、Python などから選んだ言語を使用して関数を記述できます。 完全な一覧が必要な場合、[サポートされている言語](supported-languages.md)に関するページを参照してください。
* **従量課金制の価格モデル** - コードの実行に要した時間に対してのみ課金されます。 [価格に関するセクション](#pricing)にある従量課金ホスティング プラン オプションを確認してください。  
* **独自の依存関係を使用** - Azure Functions では NuGet と NPM をサポートしているので、お気に入りのライブラリを使用できます。  
* **セキュリティの統合** - Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントなどの OAuth プロバイダーにより、HTTP によってトリガーされる関数を保護できます。  
* **簡単な手順で統合** - Azure サービスと、サービスとしてのソフトウェア (SaaS) のプランを簡単に活用できます。 例については、「[統合](#integrations)」セクションをご覧ください。  
* **柔軟な開発** - 関数をポータル内で直接作成することも、継続的インテグレーションを設定し、[GitHub](../app-service/scripts/cli-continuous-deployment-github.md) や [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) などの [サポート対象開発ツール](../app-service/deploy-local-git.md)を使ってコードをデプロイすることもできます。  
* **オープン ソース** - Azure Functions ランタイムはオープン ソースであり、 [GitHub](https://github.com/azure/azure-webjobs-sdk-script)で提供されています。  

## <a name="what-can-i-do-with-functions"></a>Functions でできること
Functions は、データの処理、システムの統合、モノのインターネット (IoT) の操作、単純な API とマイクロサービスの構築に適した優れたソリューションです。 画像処理や注文処理、ファイルの保守、スケジュールに従って実行するタスクに Functions を利用することを検討してください。 

Functions には、次のような主要なシナリオを開始するためのテンプレートが用意されています。

* **HTTPTrigger** - HTTP 要求を使用して、コードの実行をトリガーします。 例については、[最初の関数の作成](functions-create-first-azure-function.md)に関するページをご覧ください。
* **TimerTrigger** - 事前定義されたスケジュールに従ってクリーンアップなどのバッチ タスクを実行します。 例については、[タイマーによってトリガーされる関数の作成](functions-create-scheduled-function.md)に関するページをご覧ください。
* **CosmosDBTrigger** - Azure Cosmos DB ドキュメントが NoSQL データベースのコレクションに追加されたか更新されたときに、それらを処理します。 詳細については、[Azure Cosmos DB のバインド](functions-bindings-cosmosdb-v2.md)に関するページを参照してください。
* **BlobTrigger** - Azure Storage BLOB がコンテナーに追加されたときに、それを処理します。 この関数は、画像のサイズ変更に利用できます。 詳細については、[Blob Storage のバインディング](functions-bindings-storage-blob.md)に関するページをご覧ください。
* **QueueTrigger** - Azure Storage キューにメッセージが配信されたときに応答します。 詳細については、[Azure Queue Storage のバインド](functions-bindings-storage-queue.md)に関するページを参照してください。
* **EventGridTrigger** - Azure Event Grid のサブスクリプションに配信されたイベントに応答します。 イベント受信のためにサブスクリプション ベースのモデルをサポートしています。これにはフィルター処理が含まれます。 イベント ベースのアーキテクチャを構築する場合に適したソリューションです。 例については、「[Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../event-grid/resize-images-on-storage-blob-upload-event.md)」を参照してください。
* **EventHubTrigger** - Azure イベント ハブに送信されたイベントに応答します。 アプリケーションのインストルメンテーション、ユーザー エクスペリエンスやワークフローの処理、モノのインターネット (IoT) のシナリオで、特に役立ちます。 詳細については、[Event Hubs のバインディング](functions-bindings-event-hubs.md)に関するページをご覧ください。
* **ServiceBusQueueTrigger** - メッセージ キューをリッスンし、コードを他の Azure サービスまたはオンプレミスのサービスに接続します。 詳細については、[Service Bus のバインディング](functions-bindings-service-bus.md)に関するページをご覧ください。
* **ServiceBusTopicTrigger** - トピックをサブスクライブすることで、コードを他の Azure サービスまたはオンプレミスのサービスに接続します。 詳細については、[Service Bus のバインディング](functions-bindings-service-bus.md)に関するページをご覧ください。

Azure Functions は、コードの実行を開始する機能である "*トリガー*" と、データの入出力用コードを単純化する機能である "*バインド*" をサポートしています。 Azure Functions のトリガー機能とバインド機能について詳しくは、「 [Azure Functions のトリガーとバインドの開発者用リファレンス](functions-triggers-bindings.md)」をご覧ください。

## <a name="integrations"></a>統合
Azure Functions は、さまざまな Azure サービスや サード パーティのサービスと統合されます。 これらのサービスは、関数をトリガーとして実行を開始したり、コードの入出力として利用したりできます。 次のサービス統合が Azure Functions でサポートされます。

* Azure Cosmos DB
* Azure Event Hubs
* Azure Event Grid
* Azure 通知ハブ
* Azure Service Bus (キューとトピック)
* Azure Storage (BLOB、キュー、テーブル)
* オンプレミス (Service Bus を使用)
* Twilio (SMS メッセージ)

## <a name="pricing"></a>Azure Functions の価格
Azure Functions には、2 種類の価格プランがあります。 ニーズに最適なものを以下から選択します。 

* **従量課金プラン** - 関数が実行されるときに、必要なすべてのコンピューティング リソースが Azure から適用されます。 リソースの管理について考慮する必要がなく、コードを実行した時間に応じた料金のみを支払います。 
* **App Service プラン** - 関数を Web アプリと同様に実行できます。 既に App Service を他のアプリケーションに使用している場合、追加コストなしで、同じプランで関数を実行できます。 

ホスティング プランの詳細については、[Azure Functions のホスティング プランの比較](functions-scale.md)に関するページをご覧ください。 価格の詳細については、 [Functions の価格のページ](https://azure.microsoft.com/pricing/details/functions/)をご覧ください。

## <a name="next-steps"></a>次の手順
* [初めての Azure 関数の作成](functions-create-first-azure-function.md)  
  Azure Functions のクイック スタートですぐに最初の関数を作成します。 
* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  Azure Functions ランタイムに関する詳細な技術情報と、関数のコーディングやトリガーおよびバインドの定義に関するリファレンスを提供します。
* [Azure Functions のテスト](functions-test-a-function.md)  
  関数をテストするための各種ツールと手法について説明します。
* [Azure Functions のスケーリング方法](functions-scale.md)  
  Azure Functions で利用できるサービス プラン (従量課金ホスティング プランを含む) と、適切なプランを選択する方法について説明します。 
* [Azure App Service とは](../app-service/overview.md)  
  Azure Functions では、デプロイ、環境変数、診断などの主要な機能に Azure App Service を活用しています。 

