---
title: "Flow、Logic Apps、Functions、WebJobs の比較 | Microsoft Docs"
description: "Microsoft のクラウド統合サービスを比較対照し、使用するサービスの判断に役立てます。"
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: "Microsoft Flow, Flow, Logic Apps, Azure Functions, Functions, Azure WebJobs, WebJobs, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/07/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: cec9660ee068b33a114748813f0c7ffa3821d973
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Flow、Logic Apps、Functions、WebJobs の比較
この記事では、Microsoft Cloud で提供されている以下のサービスを比較対照しながら説明します。いずれも統合に関する問題を解決し、ビジネス プロセスの自動化を実現できるサービスです。

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

ここに挙げたサービスはいずれも、異なるシステムを "つなげる" 際に有用です。 どのサービスでも入力、アクション、条件、出力を定義できます。 また、それぞれのサービスはスケジュールまたはトリガーを使って実行できます。 ただし、各サービスにはそれぞれ違った利点が存在します。 このため、サービスの比較にあたっては、"どのサービスが最も良いか" ではなく、"このソリューションにはどのサービスが最もよく合うか" が問題となります。 十分な機能を備え、かつスケーラブルな統合ソリューションをすばやく構築するには、多くの場合、サービスを複数組み合わせることが最も効果的です。

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow とLogic Apps
Microsoft Flow と Azure Logic Apps はどちらも、"*構成第一*" の統合サービスであるため、ここで一緒に説明します。 これらのサービスを使用すると、プロセスやワークフローの構築から、各種の SaaS やエンタープライズ アプリケーションとの統合までを容易に行うことができます。 

* Flow は Logic Apps の上に構築されている
* 同じワークフロー デザイナーがある
* [コネクタ](../connectors/apis-list.md) は、もう一方でも動作する

Flow を使えば、オフィスの従業員がだれでも、開発者や IT 部門の力を借りることなくシンプルな統合 (重要なメールに関する SMS を受信するなど) を実現できます。 これに対して、Logic Apps では、エンタープライズレベルの DevOps とセキュリティを必要とする高度な統合やミッション クリティカルな統合 (B2B プロセスなど) が可能になります。 ビジネスで使用するワークフローは、時間の経過と共に複雑さを増してくるものです。 このため、最初はフローを使用し、後から必要に応じてロジック アプリに変換することもできます。

以下の表は、統合に Flow と Logic Apps のどちらが適しているかを判断するうえで役立ちます。

|  | Flow | Logic Apps |
| --- | --- | --- |
| オーディエンス |オフィスの従業員、ビジネス ユーザー |IT プロフェッショナル、開発者 |
| シナリオ |セルフ サービス |ミッション クリティカル |
| デザイン ツール |ブラウザー上とモバイル アプリ、UI のみ |ブラウザー上のほか、[Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)、[コード ビュー](../logic-apps/logic-apps-author-definitions.md)が利用可能 |
| DevOps |実稼働環境でのアドホックな開発 |[Azure リソース管理](../logic-apps/logic-apps-arm-provision.md) |
| 管理者向けエクスペリエンス |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| セキュリティ |標準的な実務慣行: [データの主権性の確保](https://wikipedia.org/wiki/Technological_Sovereignty)、[機密性の高い保存データの暗号化](https://wikipedia.org/wiki/Data_at_rest#Encryption)など |Azure によるセキュリティ保証: [Azure セキュリティ](https://www.microsoft.com/trustcenter/Security/AzureSecurity)、[Security Center](https://azure.microsoft.com/services/security-center/)、[監査ログ](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)など |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions とWebJobs
Azure Functions と Azure App Service WebJobs はどちらも開発者向けに設計された " *コード第一* " の統合サービスであるため、ここで一緒に説明します。 この 2 つのサービスでは、[新しいストレージ BLOB](functions-bindings-storage.md) や [webhook 要求](functions-bindings-http-webhook.md)など、さまざまなイベントに対する応答としてスクリプトまたはコードを実行できます。 この 2 つは、以下の点がよく似ています。 

* どちらも [Azure App Service](../app-service/app-service-web-overview.md) の上に構築されたものであり、[ソース管理](../app-service/app-service-continuous-deployment.md)、[認証](../app-service/app-service-authentication-overview.md)、[監視](../app-service/web-sites-monitor.md)などの機能を利用可能
* どちらも開発者向けのサービスである
* どちらも標準的なスクリプト言語とプログラミング言語をサポートしている
* どちらも NuGet および NPM をサポートしている

Functions は WebJobs の最も良い点を引き継ぎ、さらに改善しているという点で、WebJobs を自然な形で進化させたものとなっています。 WebJobs からの改善点は、以下のとおりです。 

* 開発、テスト、コードの実行をブラウザー上で直接行えるよう合理化
* [GitHub webhook](https://developer.github.com/webhooks/creating/)など、さらに多くの Azure サービスとサードパーティ サービスを統合済み
* 従量課金制を採用し、 [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)の購入が不要
* 自動の [動的スケーリング](functions-scale.md)
* App Service を既にお使いのお客様については、App Service プランでの実行も可能 (このため、利用の少ないリソースを有効活用できます)
* Logic Apps との統合

以下の表では、Functions と WebJobs の違いをまとめています。

|  | Functions | WebJobs |
| --- | --- | --- |
| スケーリング |構成不要のスケーリング |App Service プランによるスケーリング |
| 価格 |従量課金または App Service プランの一部 |App Service プランの一部 |
| 実行の種類 |トリガー、(タイマー トリガーによる) スケジュール |トリガー、継続的、スケジュール |
| トリガー イベント |[タイマー](functions-bindings-timer.md)、[Azure Cosmos DB](functions-bindings-documentdb.md)、[Azure Event Hub](functions-bindings-event-hubs.md)、[HTTP/webhook (GitHub、Slack)](functions-bindings-http-webhook.md)、[Azure App Service Mobile Apps](functions-bindings-mobile-apps.md)、[Azure Notification Hubs](functions-bindings-notification-hubs.md)、[Azure Service Bus](functions-bindings-service-bus.md)、[Azure Storage](functions-bindings-storage-blob.md) |[Azure Storage](functions-bindings-storage-blob.md)、[Azure Service Bus](functions-bindings-service-bus.md) |
| ブラウザーでの開発 |サポート対象 | サポート対象外 |
| Windows スクリプト (.cmd、.bat) |試験段階 |サポート対象 |
| PowerShell |試験段階 |サポート対象 |
| C# |サポート対象 |サポート対象 |
| F# |サポート対象 |サポート対象外 |
| Bash |試験段階 |サポート対象 |
| PHP |試験段階 |サポート対象 |
| Python |試験段階 |サポート対象 |
| JavaScript |サポート対象 |サポート対象 |

最終的には、現時点で App Service を使って何をしているかに応じて、Functions と WebJobs のどちらを使用するかが変わります。 ある App Service アプリのコード スニペットを実行する必要があり、そのコード スニペットをアプリと同じ DevOps 環境で管理したい場合には、WebJobs を使用します。 次のシナリオでは、Functions を使用します。

* 他の Azure サービスまたはサード パーティ製アプリ用のコード スニペットを実行する必要がある場合。
* App Service アプリから統合コードを個別に管理する必要がある場合。
* ロジック アプリからコード スニペットを呼び出す必要がある場合。 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow、Logic Apps、および Functions
前に述べたとおり、どのサービスが最適であるかは、状況によって異なります。 

* 単純なビジネスの最適化では、Flow を使用します。
* 統合のシナリオが高度で Flow では対応できない場合、または DevOps 機能やセキュリティ コンプライアンス機能が必要な場合には、Logic Apps を使用します。
* 統合シナリオのどこかのステップで大幅なカスタマイズまたは特殊なコードの使用が必要になる場合には、関数を作成し、ロジック アプリ内のアクションとして関数をトリガーします。

フローでは、ロジック アプリを呼び出すことができます。 また、ロジック アプリで関数を呼び出したり、関数でロジック アプリを呼び出したりすることもできます。 Flow、Logic Apps、Functions の統合は、今後ますます強まっていきます。 あるサービスで作成したものは、別のサービスで使用できます。 このため、これら 3 つのテクノロジに対する投資が無駄になることはありません。

## <a name="next-steps"></a>次のステップ
最初のフロー、ロジック アプリ、Function App、または Web ジョブを作成して、それぞれのサービスを使ってみましょう。 以下のリンクのなかから、お好きなものをクリックしてください。

* [Get started with Microsoft Flow (Microsoft Flow の概要)](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)
* [初めての Azure 関数の作成](functions-create-first-azure-function.md)
* [Visual Studio を使用した Web ジョブの展開](../app-service/websites-dotnet-deploy-webjobs.md)

ここで紹介した統合サービスの詳しい情報については、以下のリンクを参照してください。

* [Christopher Anderson によるプレゼンテーション「Leveraging Azure Functions & Azure App Service for integration scenarios (統合シナリオで Azure Functions と Azure App Service を使用する)」](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Charles Lamanna によるプレゼンテーション「Integrations Made Simple (統合をもっとシンプルに)」](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps のライブ Web キャスト](http://aka.ms/logicappslive)
* [Microsoft Flow のよくある質問](https://flow.microsoft.com/documentation/frequently-asked-questions/)


