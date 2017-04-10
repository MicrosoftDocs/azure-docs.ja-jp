---
title: "Azure Functions の概要 | Microsoft Docs"
description: "Azure Functions を使用して非同期ワークロードを最適化する方法を簡単に説明します。"
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: cfowler;mahender;glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2fd12dd32ed3c8479c7460cbc0a1cac3330ff4f4
ms.openlocfilehash: 045ec40811468eeffb25ed28ec5bfc84f074c19d
ms.lasthandoff: 03/01/2017


---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions の概要  
Azure Functions は、小規模なコード ("関数") をクラウドで手軽に実行できるソリューションです。 目の前の問題に必要なコードだけを記述すればよく、アプリケーション全体や、コードを実行するインフラストラクチャのことを考える必要がありません。 Functions により開発の生産性が大幅に向上するだけでなく、C#、F#、Node.js、Python、PHP などの開発言語も使用できるようになります。 コードが実行された時間に対してだけ料金を支払えばよく、必要に応じてスケールできます。 Azure Functions を使用すると、Microsoft Azure でサーバー不要のアプリケーションを作成できます。

このトピックでは、Azure Functions の概要について説明します。 Azure Functions の利用をすぐに開始する場合は、「 [初めての Azure 関数の作成](functions-create-first-azure-function.md)」を参照してください。 Azure Functions の詳細な技術情報をお探しの場合は、 [開発者向けリファレンス](functions-reference.md)を参照してください。

## <a name="features"></a>Features (機能)
Azure Functions の主要な機能を次に示します。

* **さまざまな言語** - C#、F#、Node.js、Python、PHP、Batch、Bash、その他実行可能な言語を使って関数を記述できます。
* **従量課金制の価格モデル** - コードの実行に要した時間に対してのみ課金されます。 [価格に関するセクション](#pricing)にある従量課金ホスティング プラン オプションを確認してください。  
* **独自の依存関係を使用** - Azure Functions では NuGet と NPM をサポートしているので、お気に入りのライブラリを使用できます。  
* **セキュリティの統合** - Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントなどの OAuth プロバイダーにより、HTTP によってトリガーされる関数を保護できます。  
* **簡単な手順で統合** - Azure サービスと、サービスとしてのソフトウェア (SaaS) のプランを簡単に活用できます。 例については、「[統合](#integrations)」セクションをご覧ください。  
* **柔軟な開発** - 関数をポータル内で直接作成することも、継続的な統合を設定し、GitHub や Visual Studio Team Services などの [サポート対象開発ツール](../app-service-web/web-sites-deploy.md#deploy-using-an-ide)を使ってコードをデプロイすることもできます。  
* **オープン ソース** - Azure Functions ランタイムはオープン ソースであり、 [GitHub](https://github.com/azure/azure-webjobs-sdk-script)で提供されています。  

## <a name="what-can-i-do-with-functions"></a>Functions でできること
Azure Functions は、データの処理、システムの統合、モノのインターネット (IoT) の操作、単純な API とマイクロサービスの構築に適した優れたソリューションです。 画像処理や注文処理、ファイルの保守、スケジュールに従って実行するタスクに Functions を利用することを検討してください。 

Functions には、次のような主要なシナリオを開始するためのテンプレートが用意されています。

* **BlobTrigger** - Azure Storage BLOB がコンテナーに追加されたときに、それを処理します。 この関数は、画像のサイズ変更に利用できます。
* **EventHubTrigger** - Azure イベント ハブに送信されたイベントに応答します。 アプリケーションのインストルメンテーション、ユーザー エクスペリエンスやワークフローの処理、モノのインターネット (IoT) のシナリオで、特に役立ちます。
* **汎用 webhook** - webhook をサポートする任意のサービスからの webhook HTTP 要求を処理します。
* **GitHub webhook** - GitHub リポジトリで発生するイベントに応答します。 例については、「 [webhook または API Azure Function を作成する](functions-create-a-web-hook-or-api-function.md)」をご覧ください。
* **HTTPTrigger** - HTTP 要求を使用して、コードの実行をトリガーします。
* **QueueTrigger** - Azure Storage キューにメッセージが配信されたときに応答します。 例については、「[Azure サービスにバインドする Azure Function を作成する](functions-create-an-azure-connected-function.md)」をご覧ください。
* **ServiceBusQueueTrigger** - メッセージ キューをリッスンし、コードを他の Azure サービスまたはオンプレミスのサービスに接続します。 
* **ServiceBusTopicTrigger** - トピックをサブスクライブすることで、コードを他の Azure サービスまたはオンプレミスのサービスに接続します。 
* **TimerTrigger** - 事前定義されたスケジュールに従ってクリーンアップなどのバッチ タスクを実行します。 例については、「 [イベント処理用の Azure の機能の作成](functions-create-an-event-processing-function.md)」をご覧ください。

Azure Functions は、コードの実行を開始する機能である "*トリガー*" と、データの入出力用コードを単純化する機能である "*バインド*" をサポートしています。 Azure Functions のトリガー機能とバインド機能について詳しくは、「 [Azure Functions のトリガーとバインドの開発者用リファレンス](functions-triggers-bindings.md)」をご覧ください。

## <a name="a-nameintegrationsaintegrations"></a><a name="integrations"></a>統合
Azure Functions は、さまざまな Azure サービスや サード パーティのサービスと統合されます。 これらのサービスは、関数をトリガーとして実行を開始したり、コードの入出力として利用したりできます。 次のサービス統合が Azure Functions でサポートされます。 

* Azure DocumentDB
* Azure Event Hubs 
* Azure Mobile Apps (テーブル)
* Azure 通知ハブ
* Azure Service Bus (キューとトピック)
* Azure Storage (BLOB、キュー、テーブル) 
* GitHub (webhook)
* オンプレミス (Service Bus を使用)
* Twilio (SMS メッセージ)

## <a name="a-namepricingahow-much-does-functions-cost"></a><a name="pricing"></a>Azure Functions の価格
Azure Functions には&2; 種類の料金プランがあり、ニーズに合わせて選択できます。 

* **従量課金プラン** - 関数が実行されるときに、必要なすべてのコンピューティング リソースが Azure から適用されます。 リソースの管理について考慮する必要がなく、コードを実行した時間に応じた料金のみを支払います。 
* **App Service プラン** - 関数を Web アプリ、モバイル アプリ、API アプリと同様に実行できます。 既に App Service を他のアプリケーションに使用している場合、追加コストなしで、同じプランで関数を実行できます。 

価格の詳細については、 [Functions の価格のページ](https://azure.microsoft.com/pricing/details/functions/)をご覧ください。 関数のスケーリングの詳細については、「 [Azure Functions のスケーリング方法](functions-scale.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* [初めての Azure 関数の作成](functions-create-first-azure-function.md)  
  Azure Functions のクイック スタートですぐに最初の関数を作成します。 
* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  Azure Functions ランタイムに関する詳細な技術情報と、関数のコーディングやトリガーおよびバインドの定義に関するリファレンスを提供します。
* [Azure Functions のテスト](functions-test-a-function.md)  
  関数をテストするための各種ツールと手法について説明します。
* [Azure Functions のスケーリング方法](functions-scale.md)  
  Azure Functions で利用できるサービス プラン (従量課金ホスティング プランを含む) と、適切なプランを選択する方法について説明します。 
* [Azure App Service とは](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions では、デプロイ、環境変数、診断などの主要な機能に Azure App Service プラットフォームを活用しています。 


