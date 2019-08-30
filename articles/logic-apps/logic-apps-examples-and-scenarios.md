---
title: 例と一般的なシナリオ - Azure Logic Apps
description: Azure Logic Apps の例、シナリオ、チュートリアル、手順
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 07/31/2019
ms.openlocfilehash: 000de22105615c3f6aa015b07e13bf8a47955b52
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706800"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps の一般的なシナリオ、例、チュートリアル、手順

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) には、オンプレミスの SQL Server または SAP から Azure Cognitive Services まで、[すぐに使用できるコネクタが数百個](../connectors/apis-list.md)用意されているため、さまざまなサービスの調整と統合に使用できます。 Logic Apps サービスは "サーバーレス" なので、スケールやインスタンスに関する心配は不要です。 必要なことは、ワークフローが実行するトリガーとアクションに関するワークフローの定義のみです。 拡張性、可用性、およびパフォーマンスは、基になるプラットフォームによって処理されます。 Logic Apps は、複数のシステム全体で複数のアクションを調整する必要があるユース ケースやシナリオに特に適しています。

ここでは、Azure Logic Apps がサポートする数多くのパターンと機能を理解するうえで役立つ、一般的な例とシナリオを紹介します。

## <a name="popular-starting-points-for-logic-app-workflows"></a>ロジック アプリ ワークフローの一般的な開始点

すべてのロジック アプリは[*トリガー*](../logic-apps/logic-apps-overview.md#logic-app-concepts) (1 つのトリガーのみ) から始まります。トリガーによって、ロジック アプリ ワークフローが開始され、あらゆるデータがそのトリガーの一部として渡されます。 一部のコネクタには、次のような種類のトリガーが用意されています。

* *ポーリング トリガー*:新しいデータのサービス エンドポイントを定期的にチェックします。 新しいデータが存在する場合、トリガーは入力としてデータを使用して新しいワークフロー インスタンスを実行します。

* *プッシュ トリガー*:サービス エンドポイントのデータをリッスンし、特定のイベントが発生するまで待ちます。 イベントが発生すると、トリガーが即時に呼び出され、入力として使用できる任意のデータを使用する新しいワークフロー インスタンスが作成され、実行されます。

一般的なトリガー例をいくつか紹介します。

* ポーリング:

  * [**繰り返し**トリガー](../connectors/connectors-native-recurrence.md)を使用すると、開始日時と、ロジック アプリを実行する頻度を設定できます。 たとえば、ロジック アプリをトリガーする曜日と時刻を選択できます。 詳細については、以下のトピックを参照してください。

    * [Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [チュートリアル:Azure Logic Apps を使用してスケジュールに従ってトラフィックをチェックする](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * "メールを受信したとき" トリガーでは、[Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)、[Gmail](https://docs.microsoft.com/connectors/gmail/)、[Outlook.com](https://docs.microsoft.com/connectors/outlook/) などの Logic Apps でサポートされる任意のメール プロバイダーからの新規メールをチェックすることができます。 詳細については、以下のトピックを参照してください。 

    * [チュートリアル:Azure Logic Apps でメーリング リスト要求を管理する](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [チュートリアル:Azure Logic Apps を使用してメールと添付ファイルの処理を自動化する](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [**HTTP** トリガー](../connectors/connectors-native-http.md)を使用すると、HTTP 上で通信することで、指定されたサービス エンドポイントをロジック アプリでチェックできます。
  
* プッシュ:

  * [**要求**トリガー](../connectors/connectors-native-reqres.md)は、ロジック アプリが HTTP 要求を受信し、何らかの方法でリアル タイムに イベントに応答できるようにします。

  * [**HTTP Webhook** トリガー](../connectors/connectors-native-webhook.md)は、サービスに*コールバック URL* を登録することでサービス エンドポイントにサブスクライブします。 この方法では、指定されたイベントが発生したときにサービスからトリガーに通知することができるので、トリガーでサービスをポーリングする必要がありません。

新しいデータまたはイベントに関する通知を受信した後に、トリガーが呼び出され、新しいロジック アプリ ワークフロー インスタンスが作成され、ワークフローのアクションが実行されます。 ワークフロー全体でトリガーのすべてのデータにアクセスできます。 たとえば、"新しいツイートで" トリガーは、実行されているロジック アプリにツイートの内容を渡します。 Azure Logic Apps の使用を開始するには、次のクイックスタート トピックを試してください。

* [クイック スタート:Azure portal で Azure Logic Apps を使用して自動化されたワークフローを初めて作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [クイック スタート:Visual Studio を使用することによって Azure Logic Apps で自動化されたタスク、プロセス、およびワークフローを作成する](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [クイック スタート:Visual Studio Code の使用による、自動化されたロジック アプリ ワークフローの作成と管理](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>トリガーへの応答とアクションの拡張

コネクタを発行していない可能性があるシステムおよびサービスについては、ロジック アプリを拡張することもできます。

* [カスタムのトリガーまたはアクションを作成する](../logic-apps/logic-apps-create-api-app.md)
* [ワークフロー実行に対して実行時間の長いアクションを設定する](../logic-apps/logic-apps-create-api-app.md)
* [webhook で外部イベントとアクションに応答する](../logic-apps/logic-apps-create-api-app.md)
* [HTTP 要求に対する同期応答によってワークフローを呼び出したり、トリガーしたり、または入れ子にする](../logic-apps/logic-apps-http-endpoint.md)
* [チュートリアル:AI で動くソーシャル ダッシュボードを Logic Apps と Power BI を使用して数分で構築する](https://aka.ms/logicappsdemo)
* [ビデオ: Twilio SMS webhook に応答してテキストで返信する](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>制御フロー、エラー処理、およびログの機能

ロジック アプリには、条件、スイッチ、ループ、スコープなど、高度な制御フローの機能が多数用意されています。 耐障害性を備えたソリューションを実現するために、ワークフローにエラーおよび例外処理を実装することもできます。 ワークフロー実行ステータスの通知および診断ログについては、Azure Logic Apps によって監視やアラートも提供されます。

* [条件付きステートメント](../logic-apps/logic-apps-control-flow-conditional-statement.md)と [switch ステートメント](../logic-apps/logic-apps-control-flow-switch-statement.md)に基づいてさまざまなアクションを実行する
* [ループを使用してステップを繰り返したり、配列やコレクションの項目を処理したりする](../logic-apps/logic-apps-control-flow-loops.md)
* [スコープを使用してアクションをグループ化する](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [ワークフローでエラーおよび例外処理を作成する](../logic-apps/logic-apps-exception-handling.md)
* [ユース ケース:医療関連企業が HL7 FHIR ワークフローのロジック アプリの例外処理を使用する方法](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [既存のロジック アプリで監視、ログ記録、アラートをオンにする](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [ロジック アプリの作成時に監視と診断ログ記録をオンにする](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>ロジック アプリのデプロイと管理

Visual Studio、Azure DevOps、またはその他のソース管理および自動ビルド ツールを使用して、ロジック アプリを完全に開発してデプロイできます。 リソース テンプレートでワークフローおよび依存接続のデプロイをサポートするために、ロジック アプリでは、Azure リソース デプロイ テンプレートが使用されます。 こうしたテンプレートは、Visual Studio ツールによって自動的に生成され、バージョン管理のためにソース管理機能にチェックインできます。

* [Visual Studio でロジック アプリを作成してデプロイする](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [既存のロジック アプリで監視、ログ記録、アラートをオンにする](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [ロジック アプリ デプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [サンプル: Azure Logic Apps から Azure Service Bus キューに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル: Azure Logic Apps から Azure Storage アカウントに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル: Azure Logic Apps の関数アプリ アクションを設定し、Azure DevOps に Azure Pipelines を使用してデプロイする](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル: Azure Logic Apps から統合アカウントに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>実行時のコンテンツ タイプ、変換

Azure Logic Apps [ワークフロー定義言語](https://aka.ms/logicappsdocs)でさまざまな関数を使用して、複数のコンテンツ タイプにアクセスしたり、そのコンテンツ タイプを変換したりできます。 たとえば、文字列、JSON、および XML を、`@json()` および `@xml()` ワークフロー式に変換することができます。 Logic Apps エンジンではコンテンツ タイプが保持され、サービス間での無損失のコンテンツ転送がサポートされます。

* [ロジック アプリでのワークフロー式の動作](../logic-apps/logic-apps-author-definitions.md)
* [JSON 以外のコンテンツ タイプを処理する](../logic-apps/logic-apps-content-type.md)。`application/xml`、`application/octet-stream`、`multipart/formdata` など
* [Azure Logic Apps のワークフロー定義言語スキーマ](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>その他の統合と機能

ロジック アプリにより、Azure Functions、Azure API Management、Azure App Service 、カスタム HTTP エンドポイント (例: REST、SOAP) など、多くのサービスとの統合も実現します。

* [Azure Serverless でリアルタイムのソーシャル ダッシュ ボードを作成する](../logic-apps/logic-apps-scenario-social-serverless.md)
* [ロジック アプリから Azure Functions を呼び出す](../logic-apps/logic-apps-azure-functions.md)
* [チュートリアル:Azure Functions でロジック アプリをトリガーする](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [チュートリアル:Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [チュートリアル:Azure Logic Apps および Azure Cognitive Services と統合して Twitter の投稿のセンチメントを分析する関数を作成する](../azure-functions/functions-twitter-email.md)
* [チュートリアル:Azure Logic Apps で IoT Hub とメールボックスに接続した状態での IoT リモート監視と通知](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [ブログ:ロジック アプリから SOAP エンドポイントを呼び出す](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>エンド ツー エンドのシナリオ

* [ホワイトペーパー:Logic Apps などの Azure サービスを使用したエンド ツー エンドのケース管理の統合](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>顧客事例

他の Azure サービスや Microsoft 製品と共に Azure Logic Apps を使用して、複雑なプロセスを簡素化、整理、自動化、調整することで、俊敏性を高め、コア ビジネスに集中できるようになった[企業の事例](https://aka.ms/logic-apps-customer-stories)をご覧ください。

## <a name="next-steps"></a>次の手順

* [Logic Apps 用コネクタ](../connectors/apis-list.md)の詳細情報
* [Azure Logic Apps を使用した B2B エンタープライズ統合シナリオ](../logic-apps/logic-apps-enterprise-integration-overview.md)の詳細情報
