---
title: Microsoft Flow、Logic Apps、Functions、および WebJobs の概要 - Azure
description: 統合タスクに最適化された Microsoft クラウド サービス (Microsoft Flow、Logic Apps、Functions、および WebJobs) を比較します。
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Microsoft Flow, Flow, Logic Apps, Azure Functions, Functions, Azure WebJobs, WebJobs, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: d6abf266d4af5c79333819f5077009124fa28247
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348227"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>Microsoft Flow、Logic Apps、Functions、および WebJobs の概要

この記事では、次の Microsoft クラウド サービスを比較します。

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

いずれも統合に関する問題を解決し、ビジネス プロセスの自動化を実現できるサービスです。 どのサービスでも入力、アクション、条件、出力を定義できます。 また、それぞれのサービスはスケジュールまたはトリガーを使って実行できます。 各サービスにはそれぞれ違った利点が存在します。この記事では、これらの違いについて説明します。

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Microsoft Flow と Azure Logic Apps の比較

Microsoft Flow と Logic Apps はどちらも、ワークフローを作成できる "*デザイナー第一*" の統合サービスです。 どちらのサービスも、各種の SaaS アプリケーションやエンタープライズ アプリケーションと統合されます。 

Microsoft Flow は Logic Apps の上に構築されています。 どちらも同じワークフロー デザイナーと同じ[コネクタ](../connectors/apis-list.md)を共有します。 

Microsoft Flow を使用すると、オフィスの従業員がだれでも、開発者や IT 部門の力を借りることなくシンプルな統合 (SharePoint ドキュメント ライブラリでの承認プロセスなど) を実現できます。 Logic Apps でも、エンタープライズレベルの Azure DevOps とセキュリティ対策を必要とする高度な統合 (B2B プロセスなど) が可能になります。 ビジネスで使用するワークフローは、時間の経過と共に複雑さを増してくるものです。 このため、最初はフローを使用し、後から必要に応じてロジック アプリに変換することもできます。

以下の表は、特定の統合に Microsoft Flow と Logic Apps のどちらが適しているかを判断するうえで役立ちます。

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| ユーザー |オフィスの従業員、ビジネス ユーザー、SharePoint 管理者 |インテグレーター、開発者、IT プロフェッショナル |
| シナリオ |セルフ サービス |高度な統合 |
| デザイン ツール |ブラウザー上とモバイル アプリ、UI のみ |ブラウザー上のほか、[Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)、[コード ビュー](../logic-apps/logic-apps-author-definitions.md)が利用可能 |
| アプリケーション ライフサイクル管理 (ALM) |非運用環境で設計とテストを行い、準備ができたら運用環境に昇格します |Azure DevOps: [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) におけるソース管理、テスト、サポート、自動化、管理 |
| 管理者エクスペリエンス |Microsoft Flow 環境とデータ損失防止 (DLP) ポリシーの管理、ライセンスの追跡: [Microsoft Flow 管理センター](https://admin.flow.microsoft.com) |リソース グループ、接続、アクセス管理、およびログ記録の管理: [Azure Portal](https://portal.azure.com) |
| セキュリティ |Office 365 セキュリティ/コンプライアンスの監査ログ、DLP、機密データの[保存時の暗号化](https://wikipedia.org/wiki/Data_at_rest#Encryption) |Azure によるセキュリティ保証:[Azure セキュリティ](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)、[Azure Security Center](https://azure.microsoft.com/services/security-center/)、[監査ログ](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Azure Functions と Azure Logic Apps の比較

Functions と Logic Apps は、サーバーレス ワークロードを可能にする Azure サービスです。 Azure Functions がサーバーレスのコンピューティング サービスであるのに対し、Azure Logic Apps はサーバーレスのワークフローを提供します。 どちらでも複雑な "*オーケストレーション*" を作成することができます。 オーケストレーションは、Logic Apps において複雑なタスクを遂行するために実行される、"*アクション*" と呼ばれる関数またはステップの集まりです。 たとえば注文のバッチを処理するのであれば、多数の関数のインスタンスを並列に実行し、すべてのインスタンスの完了を待った後、その集計結果を計算する関数を実行することになるでしょう。

Azure Functions では、コードを記述したり [Durable Functions 拡張機能](durable/durable-functions-concepts.md)を使用したりすることによって、オーケストレーションを開発します。 Logic Apps では、GUI を使用するか構成ファイルを編集することによってオーケストレーションを作成します。

オーケストレーションを構築するときは、ロジック アプリから関数を呼び出したり、関数からロジック アプリを呼び出したりしながら、さまざまなサービスを組み合わせることができます。 どのように個々のオーケストレーションを構築するかは、サービスの機能や個人の好みに基づいて選択します。 これらのサービスの主な違いをいくつか次の表に示します。
 
|  | Durable Functions | Logic Apps |
| --- | --- | --- |
| 開発 | コード第一 (命令型) | デザイナー第一 (宣言型) |
| 接続 | [ビルトインのバインド (約 10 種類)](functions-triggers-bindings.md#supported-bindings) およびカスタム バインド (コードを記述) | [コネクタの豊富なコレクション](../connectors/apis-list.md)、[Enterprise Integration Pack (B2B のシナリオ向け)](../logic-apps/logic-apps-enterprise-integration-overview.md)、[カスタム コネクタの構築](../logic-apps/custom-connector-overview.md) |
| Actions | 個々のアクティビティは Azure 関数 (アクティビティ関数のコードを記述する) |[既製のアクションの豊富なコレクション](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| 監視 | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)、[Azure Monitor ログ](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| 管理 | [REST API](durable/durable-functions-http-api.md)、[Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)、[REST API](https://docs.microsoft.com/rest/api/logic/)、[PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp)、[Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| 実行コンテキスト | [ローカル](functions-runtime-overview.md)またはクラウドで実行できます | クラウドでのみ動作します|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Functions と WebJobs の比較

Azure Functions と同様、Azure App Service WebJobs と WebJobs SDK は開発者向けに設計された "*コード第一*" の統合サービスです。 どちらも [Azure App Service](../app-service/overview.md) の上に構築されたものであり、[ソース管理の統合](../app-service/deploy-continuous-deployment.md)、[認証](../app-service/overview-authentication-authorization.md)、[Application Insights との統合による監視](functions-monitoring.md)などの機能をサポートします。

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs と WebJobs SDK

App Service の *WebJobs* 機能を使用すると、スクリプトやコードを App Service Web Apps のコンテキストで実行することができます。 *WebJobs SDK* は、WebJobs 向けに設計されたフレームワークです。Azure サービス内のイベントをきっかけにして実行するコードの作成が、このフレームワークによって単純化されます。 たとえば、Azure Storage に画像の BLOB が作成されたことをきっかけにして、サムネイル画像を作成することもできます。 WebJobs SDK は、.NET コンソール アプリケーションとして実行され、この .NET コンソール アプリケーションを WebJobs にデプロイすることができます。 

WebJobs と WebJobs SDK は組み合わせて使ったときに最も効果的に機能しますが、WebJobs SDK なしで WebJobs を使うことも、その逆も可能です。 WebJobs は、App Service のサンドボックス内で動作するあらゆるプログラムまたはスクリプトを実行できます。 WebJobs SDK コンソール アプリケーションは、オンプレミス サーバーなど、コンソール アプリケーションの実行にさえ対応していれば、どこででも実行できます。

### <a name="comparison-table"></a>比較表

Azure Functions は、WebJobs SDK の上に構築されているため、同じイベント トリガーや他の Azure サービスとの接続を数多く共有します。 以下に、Azure Functions を使用するか WebJobs と WebJobs SDK を使用するかの選択において考慮すべき事柄をいくつか示します。

|  | Functions | WebJobs と WebJobs SDK |
| --- | --- | --- |
|[サーバーレスのアプリ モデル](https://azure.microsoft.com/solutions/serverless/)と[自動スケール](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[ブラウザーでの開発とテスト](functions-create-first-azure-function.md) |✔||
|[従量課金制の価格](functions-scale.md#consumption-plan)|✔||
|[Logic Apps との統合](functions-twitter-email.md)|✔||
| トリガー イベント |[Timer](functions-bindings-timer.md)<br>[Azure Storage キューと BLOB](functions-bindings-storage-blob.md)<br>[Azure Service Bus のキューとトピック](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub、Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Azure Storage キューと BLOB](functions-bindings-storage-blob.md)<br>[Azure Service Bus のキューとトピック](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[ファイル システム](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| サポートされている言語  |C#<br>F#<br>JavaScript<br>Java (プレビュー)<br>Python (プレビュー) |C#<sup>1</sup>|
|パッケージ マネージャー|NPM と NuGet|NuGet<sup>2</sup>|

<sup>1</sup> (WebJobs SDK なしの) WebJobs では、C#、Java、JavaScript、Bash、.cmd、.bat、PowerShell、PHP、TypeScript、Python などがサポートされます。 これは、包括的な一覧ではありません。 WebJobs は、App Service のサンドボックス内で動作するあらゆるプログラムまたはスクリプトを実行できます。

<sup>2</sup> (WebJobs SDK なしの) WebJobs では NPM と NuGet がサポートされています。

### <a name="summary"></a>まとめ

Azure Functions は、Azure App Service WebJobs よりも開発者の生産性を向上させます。 また、プログラミング言語、開発環境、Azure サービスの統合、および価格に関して、より多くのオプションが提供されます。 ほとんどの場合、それが最適な選択肢になるでしょう。

WebJobs が最適な選択肢となるシナリオは 2 つです。それらを次に示します。

* イベントをリッスンするコード (`JobHost` オブジェクト) をより細かく制御する必要がある。 Functions では、[host.json](functions-host-json.md) ファイルで `JobHost` の動作をカスタマイズする方法に限りがあります。 JSON ファイル内の文字列で指定できる範囲を超えた作業も、ときには必要になります。 たとえば、Azure Storage のカスタム再試行ポリシーを構成できるのは、WebJobs SDK だけです。
* コード スニペットの実行対象の App Service アプリがあり、そのコード スニペットをアプリと同じ Azure DevOps 環境で管理したい。

その他のシナリオで、Azure またはサードパーティのサービスと連携するコード スニペットを実行する場合は、WebJobs と WebJobs SDK の組み合わせよりも、Azure Functions を選ぶようにしてください。

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow、Logic Apps、Functions、および WebJobs の連携

これらのサービスのうち、1 つだけを選択する必要はありません。 これらのサービスは、外部のサービスと連携するのと同様に、互いに連携して動作します。

フローからロジック アプリを呼び出すことができます。 ロジック アプリから関数を呼び出したり、関数からロジック アプリを呼び出したりすることが可能です。 その例については、「[Azure Logic Apps と統合される関数を作成する](functions-twitter-email.md)」を参照してください。

Microsoft Flow、Logic Apps、および Functions の統合は、今後ますます強まっていきます。 あるサービスで作成したものは、別のサービスで使用できます。

統合サービスの詳細については、以下のリンクを参照してください。

* [Christopher Anderson によるプレゼンテーション「Leveraging Azure Functions & Azure App Service for integration scenarios (統合シナリオで Azure Functions と Azure App Service を使用する)」](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Charles Lamanna によるプレゼンテーション「Integrations Made Simple (統合をもっとシンプルに)」](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps のライブ Web キャスト](https://aka.ms/logicappslive)
* [Microsoft Flow のよく寄せられる質問](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>次の手順

初めてのフロー、ロジック アプリ、関数アプリを実際に作ってみましょう。 以下のいずれかのリンクを選択してください。

* [Get started with Microsoft Flow (Microsoft Flow の概要)](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [初めての Azure 関数の作成](functions-create-first-azure-function.md)
