---
title: サーバーレス シナリオ - Azure サービスを使用して Customer Insights ダッシュボードを作成する | Microsoft Docs
description: Azure Logic Apps と Azure Functions を使用してカスタマーのダッシュボードを構築することで、カスタマーのフィードバックやソーシャル メディアのデータなどを管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 29d56e64f118fe36fb4905dca20d9fba2f210665
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295926"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Azure Logic Apps と Azure Functions を使用して Customer Insights ストリーミング ダッシュボードを作成する

Azure には、インフラストラクチャを気にすることなく、クラウドですばやくアプリをビルドしてホストするのに役立つ、[サーバーレス](https://azure.microsoft.com/solutions/serverless/) ツールが用意されています。 このチュートリアルでは、カスタマーからのフィードバックによってトリガーされ、機械学習を使用してフィードバックを分析し、Power BI や Azure Data Lake などのソースに洞察を発行するダッシュボードを作成することができます。

このソリューションに向けて、サーバーレス アプリ用の主要な Azure コンポーネントである[Azure Functions](https://azure.microsoft.com/services/functions/) と [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) を使用します。
Azure Logic Apps では、サーバーレス コンポーネント間でオーケストレーションを作成したり、200 を超えるサービスや API に接続したりできるように、クラウド内のサーバーレス ワークフロー エンジンが提供されます。 Azure Functions は、クラウド内でサーバーレス コンピューティングを実現します。 このソリューションでは、定義済みのキーワードに基づいてカスタマーのツイートにフラグを設定するために、Azure Functions を使用します。

このシナリオでは、カスタマーからのフィードバックを検出するとトリガーされるロジック アプリを作成します。 カスタマー フィードバックへの応答に役立つコネクタには、Outlook.com、Office 365、Survey Monkey、Twitter、[Web フォームからの HTTP 要求](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/)などがあります。 作成するワークフローは、Twitter のハッシュタグを監視します。

[Visual Studio でソリューション全体をビルド](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)し、[Azure Resource Manager テンプレートを使用してソリューションをデプロイ](../logic-apps/logic-apps-create-deploy-template.md)することができます。 このソリューションの作成方法に関するビデオ チュートリアルについては、[こちらの Channel 9 ビデオ](https://aka.ms/logicappsdemo)をご覧ください。 

## <a name="trigger-on-customer-data"></a>カスタマー データによるトリガー

1. Azure Portal または Visual Studio で、空のロジック アプリを作成します。 

   ロジック アプリを初めて使用する場合は、[Azure Portal 用のクイックスタート](../logic-apps/quickstart-create-first-logic-app-workflow.md)または [Visual Studio 用のクイックスタート](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)を参照してください。

2. ロジック アプリ デザイナーで、次のアクションを含む Twitter トリガーを検索して追加します。**新しいツイートの投稿時**

3. キーワードまたはハッシュタグに基づいてツイートをリッスンするようにトリガーを設定します。

   Twitter トリガーなどのポーリングベースのトリガーでは、反復プロパティによって、ロジック アプリが新しいアイテムを確認する頻度が決定されます。

   ![Twitter のトリガーの例][1]

すべての新しいツイートに対してロジック アプリがトリガーされるようになりました。 次に、表現されるセンチメントをより良く把握するために、ツイート データを取得して分析することができます。 

## <a name="analyze-tweet-text"></a>ツイート テキストの分析

テキストの背後にあるセンチメントを検出するために、[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を使用できます。

1. ロジック アプリ デザイナーのトリガーの下で、 **[新しいステップ]** を選択します。

2. **テキスト分析**コネクタを検索します。

3. **[Detect Sentiment (センチメントの検出)]** アクションを選択します。

4. メッセージが表示される場合は、テキスト分析サービス用の有効な Cognitive Services キーを指定します。

5. **[要求本文]** の下で、 **[ツイート テキスト]** フィールドを選択します。これにより、ツイート テキストが分析用の入力として提供されます。

ツイート データとそのツイートに関する洞察を取得したら、その他のいくつかの関連するコネクタと、そのアクションを使用できますようになります。

* **Power BI - ストリーミング データセットへの行の追加**:Power BI ダッシュボードで受信ツイートを表示します。
* **Azure Data Lake - ファイルの追加**:カスタマーのデータを Azure Data Lake データセットに追加して Analytics ジョブに含めます。
* **SQL - 行の追加**:後で取得できるようにデータベースにデータを格納します。
* **Slack - メッセージの送信**:アクションを必要とする可能性がある否定的なフィードバックについて Slack チャンネルに通知します。

また、データに対してカスタム処理を実行するために、Azure 関数を作成することもできます。 

## <a name="process-data-with-azure-functions"></a>Azure Functions でのデータ処理

関数を作成する前に、Azure サブスクリプションで関数アプリを作成します。 また、ロジック アプリで関数を直接呼び出すには、関数が HTTP トリガー バインディングを持つ必要があります。たとえば、**HttpTrigger** テンプレートを使用します。 詳しくは、[Azure Portal で最初の関数アプリと関数を作成する方法](../azure-functions/functions-create-first-azure-function-azure-portal.md)に関するページを参照してください。

このシナリオでは、Azure 関数の要求本文としてツイート テキストを使用します。 関数コードで、ツイート テキストがキーワードまたはフレーズを含むかどうかを判定するロジックを定義します。 シナリオに応じて、単純または複雑な関数を作成します。
関数の最後で、データと共にロジック アプリへの応答を返します。データには、たとえば `containsKeyword` のような単純なブール値や複合オブジェクトを使用します。

> [!TIP]
> ロジック アプリ内で関数からの複雑な応答にアクセスするには、**Parse JSON** アクションを使用します。

完了したら、関数を保存し、構築しているロジック アプリに、アクションとして関数を追加します。

## <a name="add-azure-function-to-logic-app"></a>ロジック アプリへの Azure 関数の追加

1. ロジック アプリ デザイナーの **[Detect Sentiment]\(センチメントの検出\)** アクションで、 **[新しいステップ]** を選択します。

2. **Azure Functions** コネクタを検索し、作成した関数を選択します。

3. **[要求本文]** の下で **[ツイート テキスト]** を選択します。

![構成済みの Azure 関数のステップ][2]

## <a name="run-and-monitor-your-logic-app"></a>ロジック アプリの実行と監視

現在または過去のロジック アプリの実行を確認するには、Azure Logic Apps によって提供されるデバッグと監視の豊富な機能を使用できます。これらの機能は、Azure Portal や Visual Studio で、または Azure REST API および SDK を通じて提供されます。

ロジック アプリを簡単にテストするには、ロジック アプリ デザイナーで **[トリガーの実行]** を選択します。 トリガーは、条件を満たすツイートが見つかるまで、指定したスケジュールに基づいてツイートをポーリングします。 実行が進行する間、デザイナーで実行の進行状況がリアルタイムに表示されます。

Visual Studio または Azure Portal で過去の実行履歴を表示するには、以下の操作を実行します。 

* Visual Studio Cloud Explorer を開きます。 ロジック アプリを検索し、アプリのショートカット メニューを開きます。 **[実行履歴を開く]** を選択します。

  > [!TIP]
  > このコマンドが Visual Studio 2019 にない場合は、Visual Studio の最新の更新プログラムが適用されていることを確認してください。

* Azure Portal でロジック アプリを検索します。 ロジック アプリのメニューで、 **[概要]** を選択します。 

## <a name="create-automated-deployment-templates"></a>自動デプロイ テンプレートを作成する

ロジック アプリのソリューションを作成したら、アプリをキャプチャし、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md#template-deployment)として世界中の Azure リージョンにデプロイすることができます。 この機能を使用すると、パラメーターを変更して、異なるバージョンのアプリを作成したり、Azure Pipelines にソリューションを統合したりできます。 Azure Functions をデプロイ テンプレートに含めることもできるので、すべての依存関係を含むソリューション全体を 1 つのテンプレートとして管理できます。 詳しくは、[ロジック アプリのデプロイ テンプレートの作成方法](../logic-apps/logic-apps-create-deploy-template.md)に関するページを参照してください。

Azure 関数を使用したデプロイ テンプレートの例については、[Azure クイックスタート テンプレート レポジトリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps のその他の例とシナリオを探す](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
