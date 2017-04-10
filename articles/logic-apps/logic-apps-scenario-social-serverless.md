---
title: "シナリオ - Azure Serverless を使用して Customer Insights ダッシュボードを作成する | Microsoft Docs"
description: "Azure Logic Apps と Azure Functions を使用してダッシュボードを構築し、顧客からのフィードバックやソーシャル データなどを管理する方法の例を示します。"
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 4676e0da4309b1460e471f94946161fa22d46226
ms.lasthandoff: 03/30/2017

---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Azure Logic Apps と Azure Functions を使用してリアルタイム Customer Insights ダッシュボードを作成する

Azure Serverless ツールは、インフラストラクチャを気にすることなく、クラウドでアプリケーションをすばやく構築してホストするための強力な機能を提供します。  このシナリオでは、ダッシュボードを作成して顧客からのフィードバックをトリガーし、Machine Learning を使用してフィードバックを分析します。さらに、Power BI や Azure Data Lake などのソースに洞察を発行します。

## <a name="overview-of-the-scenario-and-tools-used"></a>シナリオと使用するツールの概要

このソリューションを実装するには、Azure のサーバーレス アプリの主要な 2 つのコンポーネント、[Azure Functions](https://azure.microsoft.com/services/functions/) と [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) を利用します。

Logic Apps は、クラウド内のサーバーレス ワークフロー エンジンです。  サーバーレス コンポーネント間のオーケストレーションを提供し、100 を超えるサービスと API にも接続します。  このシナリオでは、顧客からのフィードバックをトリガーするロジック アプリを作成します。  顧客からのフィードバックへの対応に役立つコネクタには、Outlook.com、Office 365、Survey Monkey、Twitter、[Web フォームからの](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/) HTTP 要求などがあります。  以下のワークフローでは、Twitter のハッシュタグを監視します。

Functions は、クラウド内でサーバーレス コンピューティングを実現します。  このシナリオでは、Azure Functions を使用して、事前に定義された一連のキーワードに基づいて顧客のツイートにフラグを設定します。

ソリューション全体は [Visual Studio で構築](logic-apps-deploy-from-vs.md)して、[リソース テンプレートの一部としてデプロイ](logic-apps-create-deploy-template.md)できます。  [Channel 9](http://aka.ms/logicappsdemo) に、このシナリオのビデオ チュートリアルもあります。

## <a name="building-the-logic-app-to-trigger-on-customer-data"></a>顧客のデータをトリガーするロジック アプリの構築

Visual Studio か Azure Portal で[ロジック アプリを作成](logic-apps-create-a-logic-app.md)した後、次の操作を実行します。

1. Twitter から **[On New Tweets (新しいツイート)]** にトリガーを追加します。
1. キーワードまたはハッシュタグに対してツイートをリッスンするようにトリガーを構成します。

> [!NOTE]
> このトリガーの反復プロパティは、ポーリングベースのトリガーに対してロジック アプリが新しいアイテムを確認する頻度を決定します。

![Twitter のトリガーの例][1]

これで、このアプリはすべての新しいツイートに対して起動されます。  そのツイートのデータを取得し、表現されたセンチメントをより深く理解できます。  そのためには、[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を使用してテキストのセンチメントを検出します。

1. **[新しいステップ]** をクリックします。
1. **[テキスト分析]** コネクタを選択または検索します。
1. **[Detect Sentiment (センチメントの検出)]** 操作を選択します。
1. メッセージが表示される場合は、テキスト分析サービス用の有効な Cognitive Services キーを入力します。
1. 分析対象のテキストとして **[ツイート テキスト]** を追加します。

これで、ツイート データ、ツイートに対する洞察、関連するその他の各種コネクタを用意できました。
* Power BI - ストリーミング データセットへの行の追加: Power BI ダッシュボードにリアルタイムでツイートを表示します。
* Azure Data Lake - ファイルの追加: 顧客のデータを Azure Data Lake データセットに追加して Analytics ジョブに含めます。
* SQL - 行の追加: 後で取得できるようにデータベースにデータを格納します。
* Slack - メッセージの送信: アクションが必要な否定的なフィードバックについて Slack チャンネルにアラートを送信します。

Azure 関数を使用して、データ上でさらにカスタム コンピューティングを行うこともできます。

## <a name="enriching-the-data-with-an-azure-function"></a>Azure 関数を使用したデータの強化

関数を作成する前に、Azure サブスクリプションに関数アプリが必要です。  ポータルでの Azure 関数の作成の詳細については、[こちら](../azure-functions/functions-create-first-azure-function-azure-portal.md)を参照してください。

ロジック アプリから直接呼び出される関数の場合は、HTTP トリガー バインディングが必要です。  **HttpTrigger** テンプレートを使用することをお勧めします。

このシナリオでは、Azure 関数の要求本文はツイート テキストです。  関数のコードでは、ツイート テキストにキーワードや語句が含まれているかどうかに関するロジックを単に定義します。  関数自体は、シナリオでの必要性に応じて、単純な形のままにすることも、複雑なものにすることもできます。

関数の最後で、ロジック アプリに何らかのデータと共に応答を返すだけです。  これは、単純なブール値 (`containsKeyword` など) でも複雑なオブジェクトでもかまいません。

![構成済みの Azure 関数のステップ][2]

> [!TIP]
> ロジック アプリ内で関数からの複雑な応答にアクセスする場合は、Parse JSON アクションを使用します。

関数を保存すると、上で作成したロジック アプリに追加できます。  ロジック アプリ内で、次の操作を実行します。

1. **[新しいステップ]** をクリックして追加します。
1. **[Azure Functions]** コネクタを選択します。
1. 既存の関数を使用するように選択し、作成した関数を参照します。
1. **[要求本文]** の **[ツイート テキスト]** を送信します。

## <a name="running-and-monitoring-the-solution"></a>ソリューションの実行と監視

Logic Apps でサーバーレス オーケストレーションを作成する利点の 1 つが、豊富なデバッグと監視の機能です。  すべての実行 (現在または過去) は、Visual Studio や Azure Portal から、または REST API や SDK を使用して表示できます。

ロジック アプリをテストする最も簡単な方法の 1 つは、デザイナーの **[実行]** ボタンを使用することです。  **[実行]** をクリックすると、イベントが検出されるまで 5 秒ごとにトリガーがポーリングされ、実行の進行状況がリアルタイムに表示されます。

以前の実行履歴は、Azure Portal の [概要] ブレードか、Visual Studio Cloud Explorer を使用して確認できます。

## <a name="creating-a-deployment-template-for-automated-deployments"></a>自動デプロイ用のデプロイ テンプレートの作成

ソリューションの開発後、そのソリューションをキャプチャし、Azure デプロイ テンプレートを使用して世界中の任意の Azure リージョンにデプロイすることができます。  これは、このワークフローの別バージョン用にパラメーターを変更する場合や、ビルドとリリースのパイプラインにこのソリューションを統合する場合にも便利です。  デプロイ テンプレートの作成の詳細については、[この記事](logic-apps-create-deploy-template.md)を参照してください。

Azure Functions をデプロイ テンプレートに組み込むこともできるので、すべての依存関係を含むソリューション全体を 1 つのテンプレートとして管理できます。  関数のデプロイ テンプレートの例については、[Azure クイックスタート テンプレート レポジトリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)を参照してください。

## <a name="whats-next"></a>参照トピック

* [Azure Logic Apps のその他の例とシナリオを参照する](logic-apps-examples-and-scenarios.md)
* [このソリューションのエンド ツー エンドでの作成に関するビデオ チュートリアルを視聴する](http://aka.ms/logicappsdemo)
* [ロジック アプリ内で例外をキャッチして処理する方法を確認する](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
