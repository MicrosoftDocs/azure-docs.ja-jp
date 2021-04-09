---
title: 例と一般的なシナリオ
description: Azure Logic Apps の一般的なシナリオ、チュートリアル、手順を紹介します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 5f7afe044ab4f782f2028598c2c56e75edf95860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317373"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps の一般的なシナリオ、例、チュートリアル、手順

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) には、SQL Server または SAP から Azure Cognitive Services まで、[すぐに使用できるコネクタが数百個](../connectors/apis-list.md)用意されているため、さまざまなサービスの調整と統合に使用できます。 Logic Apps サービスは "サーバーレス" なので、スケールやインスタンスに関する心配は不要です。 必要なことは、ワークフローが実行するトリガーとアクションに関するワークフローの定義のみです。 拡張性、可用性、およびパフォーマンスは、基になるプラットフォームによって処理されます。 Logic Apps は、複数のシステムとサービス全体でアクションを調整する必要があるユース ケースやシナリオに特に適しています。

Azure Logic Apps がサポートする機能とパターンについて学習するために、この記事では、一般的な出発点、例、およびシナリオについて説明します。

## <a name="common-starting-points-for-logic-app-workflows"></a>ロジック アプリ ワークフローの一般的な開始点

すべてのロジック アプリは [*トリガー*](../logic-apps/logic-apps-overview.md#logic-app-concepts) (1 つのトリガーのみ) から始まります。トリガーによって、ロジック アプリ ワークフローが開始され、あらゆるデータがそのトリガーの一部として渡されます。 一部のコネクタには、次のような種類のトリガーが用意されています。

* *ポーリング トリガー*:新しいデータのサービス エンドポイントを定期的にチェックします。 新しいデータが存在する場合、トリガーは入力としてデータを使用して新しいワークフロー インスタンスを実行します。

* *プッシュ トリガー*:サービス エンドポイントのデータをリッスンし、特定のイベントが発生するまで待ちます。 イベントが発生すると、トリガーが即時に呼び出され、入力として使用できる任意のデータを使用する新しいワークフロー インスタンスが作成され、実行されます。

一般的に使用されるトリガーについて説明する例を次に示します。

* "*ポーリング*" トリガー:

  * [**繰り返し** トリガー](../connectors/connectors-native-recurrence.md)を使用すると、開始日時と、ロジック アプリを実行する頻度を設定できます。 たとえば、ロジック アプリをトリガーする曜日と時刻を選択できます。 詳細については、以下のトピックを参照してください。<p>

    * [Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [チュートリアル:スケジュールに基づいて定期的に実行される自動化されたワークフローを Azure Logic Apps を使用して作成する](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **メールを受信したとき** トリガーでは、[Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)、[Gmail](/connectors/gmail/)、[Outlook.com](/connectors/outlook/) などの Logic Apps でサポートされる任意のメール プロバイダーからの新規メールをロジック アプリでチェックすることができます。

    > [!IMPORTANT]
    > Gmail コネクタを使用する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみでこのコネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)に関する記事を参照してください。

    詳細については、以下のトピックを参照してください。<p>

    * [チュートリアル:Azure Logic Apps を使用して承認ベースの自動化されたワークフローを作成する](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [チュートリアル:Azure Logic Apps、Azure Functions、Azure Storage を使用してメール処理のタスクを自動化する](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [**HTTP** トリガー](../connectors/connectors-native-http.md) は、HTTP または HTTPS 経由でサービス エンドポイントを呼び出すことができます。 詳しくは、[HTTP エンドポイントを使用したワークフローの呼び出し、トリガー、入れ子](../logic-apps/logic-apps-http-endpoint.md)に関する記事をご覧ください

* "*プッシュ*" トリガー:

  * [**要求** トリガー](../connectors/connectors-native-reqres.md) では、受信 HTTPS 要求を受信できます。

  * [**HTTP Webhook** トリガー](../connectors/connectors-native-webhook.md)は、サービスに *コールバック URL* を登録することでサービス エンドポイントにサブスクライブします。 この方法では、指定されたイベントが発生したときにサービスからトリガーに通知することができるので、トリガーでサービスをポーリングする必要がありません。

指定されたイベントが発生すると、トリガーが呼び出され、新しいロジック アプリ ワークフロー インスタンスが作成され、ワークフローのアクションが実行されます。 ワークフロー全体でトリガーのすべてのデータにアクセスできます。 たとえば、Twitter の **新しいツイートで** トリガーは、実行されているロジック アプリにツイートの内容を渡します。 Azure Logic Apps の使用を開始するには、次のクイックスタート トピックを試してください。

* [クイック スタート: Azure Logic Apps を使用して初めてのワークフローを作成する - Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [クイック スタート: Azure Logic Apps を使用して自動化されたタスク、プロセス、およびワークフローを作成する - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [クイック スタート: Visual Studio Code の使用による、自動化されたロジック アプリ ワークフローの作成と管理](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>制御フローおよびエラー処理の機能

ロジック アプリには、条件、スイッチ、ループ、スコープなど、高度な制御フローの機能が多数用意されています。 耐障害性を備えたソリューションを実現するために、ワークフローにエラーおよび例外処理を実装することもできます。

* [条件付きステートメント](../logic-apps/logic-apps-control-flow-conditional-statement.md)と [switch ステートメント](../logic-apps/logic-apps-control-flow-switch-statement.md)に基づいてさまざまなアクションを実行する
* [ループを使用してステップを繰り返したり、配列やコレクションの項目を処理したりする](../logic-apps/logic-apps-control-flow-loops.md)
* [スコープを使用してアクションをグループ化する](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [ワークフローにエラーと例外の処理を追加する](../logic-apps/logic-apps-exception-handling.md)
* [ユース ケース:医療関連企業が HL7 FHIR ワークフローのロジック アプリの例外処理を使用する方法](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>カスタム API とコネクタを作成する

コネクタを公開していないシステムおよびサービスについては、ロジック アプリを拡張することもできます。

* [Azure Logic Apps おから呼び出すカスタム API を作成する](../logic-apps/logic-apps-create-api-app.md)
* [ポーリング トリガー パターンで新しいデータまたはイベントを定期的に確認する](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [webhook トリガー パターンで新しいデータまたはイベントを待ち受ける](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [ポーリング アクション パターンで長時間タスクを実行する](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [webhook アクション パターンで長時間タスクを実行する](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Azure Logic Apps のカスタム コネクタ](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>企業間取引 (B2B) ソリューションの構築

組織の間にエンタープライズ統合ソリューションとシームレスな通信を実現するために、Azure Logic Apps で Enterprise Integration Pack (EIP) を使用して、このようなシナリオ向けの自動化されたスケーラブルなワークフローを構築することができます。 組織どうしは、異なるプロトコルと形式を使用していても、メッセージを電子的に交換することができます。 異なる形式は、EIP によって、組織のシステムで処理できる、AS2、X12、EDIFACT、RosettaNet などの業界標準プロトコルをサポートする形式に変換されます。 これらのソリューションを構築する前に、統合アカウントを作成できます。これは、ロジック アプリ ワークフローで定義して使用する成果物のためのセキュリティで保護され、スケーラブルで管理可能なコンテナーを提供する個別の Azure リソースです。 たとえば、成果物には取引先、契約、マップ、スキーマ、証明書、バッチ構成などが含まれます。

* [概要:Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ソリューション](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps で B2B エンタープライズ統合用の統合アカウントを作成および管理する](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Azure 仮想ネットワーク リソースにアクセスする

場合によっては、ご利用のロジック アプリと統合アカウントで、Azure 仮想ネットワーク内の仮想マシン (VM) や他のシステムまたはサービスなど、セキュリティで保護されたリソースにアクセスする必要が生じます。 このアクセスを設定するために、統合サービス環境 (ISE) を作成して、そこで自分のロジック アプリをビルドおよび実行することができます。 ISE は、ストレージなどの専用のリソースを使用し、パブリックで "グローバル" なマルチテナント Logic Apps サービスとは別に実行される、プライベートで分離された Logic Apps サービスのインスタンスです。 分離したプライベート インスタンスとパブリック グローバル インスタンスを分けることで、他の Azure テナントが自分のアプリのパフォーマンスに与える可能性がある影響 ("うるさい隣人" エフェクトとも呼ばれる) を減らすことができます。

* [概要:Azure Logic Apps から Azure Virtual Network リソースにアクセスする](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>ロジック アプリのデプロイ、管理、監視

Visual Studio、Azure DevOps、またはその他のソース管理および自動ビルド ツールを使用して、ロジック アプリを完全に開発してデプロイできます。 リソース テンプレートでワークフローおよび依存接続のデプロイをサポートするために、ロジック アプリでは、Azure リソース デプロイ テンプレートが使用されます。 こうしたテンプレートは、Visual Studio ツールによって自動的に生成され、バージョン管理のためにソース管理機能にチェックインできます。 ワークフロー実行ステータスの通知および診断ログについては、Azure Logic Apps によって監視やアラートも提供されます。

### <a name="deploy"></a>配置

* [クイック スタート: Azure Logic Apps を使用して自動化されたタスク、プロセス、およびワークフローを作成する - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [概要:ロジック アプリのデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Logic Apps でのデプロイを自動化するために Azure Resource Manager テンプレートを作成する](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Logic Apps 用の Azure Resource Manager テンプレートをデプロイする](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [サンプル:Azure Logic Apps から Azure Service Bus キューに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps から Azure Storage アカウントに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps の関数アプリ アクションを設定し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps から統合アカウントに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps を使用した Azure Pipelines の調整](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>管理する

* [Visual Studio を使用してロジック アプリを管理する](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [B2B エンタープライズ統合用の統合アカウントを作成および管理する](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Azure Logic Apps で統合サービス環境 (ISE) を管理する](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>モニター

* [Azure Logic Apps での実行状態の監視、トリガー履歴の確認、アラートの設定](../logic-apps/monitor-logic-apps.md)
* [Azure Monitor ログを設定し、Azure Logic Apps の診断データを収集する](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Azure Monitor ログを設定し、Azure Logic Apps で B2B メッセージの診断データを収集する](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Azure Logic Apps の Azure Monitor ログで監視と追跡を行うクエリを表示および作成します](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>コンテンツ タイプ、変換の処理

Azure Logic Apps [ワークフロー定義言語](./logic-apps-workflow-definition-language.md)でさまざまな関数を使用して、複数のコンテンツ タイプにアクセスしたり、そのコンテンツ タイプを変換したりできます。 たとえば、文字列、JSON、および XML を、`@json()` および `@xml()` ワークフロー式に変換することができます。 Logic Apps エンジンではコンテンツ タイプが保持され、サービス間での無損失のコンテンツ転送がサポートされます。

* [Azure Logic Apps における各種コンテンツの扱い](../logic-apps/logic-apps-content-type.md) (`application/`、`application/octet-stream`、`multipart/formdata` など)
* [Azure Logic Apps および Power Automate の式で関数を使用するためのリファレンス ガイド](../logic-apps/workflow-definition-language-functions-reference.md)
* [Azure Logic Apps のワークフロー定義言語スキーマ](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>その他の統合と機能

Azure Logic Apps は、Azure Functions、Azure API Management、Azure App Service、カスタム HTTP エンドポイント (例: REST、SOAP) など、多くのサービスと統合できます。

* [Azure Logic Apps から Azure 関数を呼び出す](../logic-apps/logic-apps-azure-functions.md)
* [チュートリアル:Azure Functions と Azure Service Bus を使用してロジック アプリを呼び出すか、またはトリガーする](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [チュートリアル:Azure Logic Apps と Azure Functions を使用して Customer Insights ストリーミング ダッシュボードを作成する](../logic-apps/logic-apps-scenario-social-serverless.md)
* [チュートリアル:Azure Logic Apps および Azure Cognitive Services と統合して Twitter の投稿のセンチメントを分析する関数を作成する](../azure-functions/functions-twitter-email.md)
* [チュートリアル:Power BI と Azure Logic Apps を使用して AI で動くソーシャル ダッシュボードを構築する](https://aka.ms/logicappsdemo)
* [チュートリアル:Azure Event Grid と Logic Apps を使用して仮想マシンの変更を監視する](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [チュートリアル:Azure Logic Apps で IoT Hub とメールボックスに接続した状態での IoT リモート監視と通知](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [ブログ:Azure Logic Apps を使用して SOAP サービスを呼び出す](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>エンド ツー エンドのシナリオ

* [ホワイトペーパー:Logic Apps などの Azure サービスを使用したエンド ツー エンドのケース管理の統合](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>顧客事例

他の Azure サービスや Microsoft 製品と共に Azure Logic Apps を使用して、複雑なプロセスを簡素化、整理、自動化、調整することで、俊敏性を高め、コア ビジネスに集中できるようになった[企業の事例](https://aka.ms/logic-apps-customer-stories)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Logic Apps 用コネクタ](../connectors/apis-list.md)の詳細情報
* [Azure Logic Apps を使用した B2B エンタープライズ統合シナリオ](../logic-apps/logic-apps-enterprise-integration-overview.md)の詳細情報