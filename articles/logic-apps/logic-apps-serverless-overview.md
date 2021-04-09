---
title: 概要 - クラウド ベースのアプリおよびソリューションのための Azure サーバーレス
description: Azure Logic Apps と Azure Functions を使用して、インフラストラクチャを気にすることなくクラウド ベースのアプリおよびソリューションを作成する方法について説明します。
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788097"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Azure Logic Apps と Azure Functions を使用してクラウド ベースのアプリおよびソリューションを構築するための概要

[サーバーレス](https://azure.microsoft.com/solutions/serverless/) アプリには、開発速度の向上、コードの削減、簡単さ、スケーリングなどの利点があります。 この記事では、サーバーレス ソリューションのさまざまな属性と Azure サーバーレスのオファリングについて説明します。

## <a name="what-is-serverless"></a>サーバーレスとは

サーバーレスは、サーバーがないことを示すわけではなく、開発者がサーバーについて心配する必要がないことを意味しています。 従来のアプリケーション開発の大きな部分は、アプリケーションの要求を満たすためのソリューションのスケーリング、ホスティング、監視に関する質問に答えるところにありました。 サーバーレスでは、これらの質問はソリューションの一部として対応されます。 また、サーバーレス アプリは消費ベースのプランに基づいて課金されます。 アプリが使用されないかぎり、料金は発生しません。 これらの機能により、開発者はソリューションのビジネス ロジックにのみ集中することができます。

サーバーレスに関する Azure のコア サービスは、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) と [Azure Functions](https://azure.microsoft.com/services/functions/) です。 どちらのソリューションも先に説明した原則に従っており、開発者が最小のコードで強力なクラウド アプリを構築できるようにします。

## <a name="what-is-azure-logic-apps"></a>Azure Logic Apps とは

[Azure Logic Apps](logic-apps-overview.md) は、クラウド内のスケーラブルな統合やワークフローを簡略化して実装するための方法を提供します。 このサービスでは、ワークフローと呼ばれる一連のステップとしてプロセスをモデル化して自動化するためのビジュアル デザイナーが用意されています。 サーバーレス アプリを他の API にすばやく接続するための、クラウド サービスとオンプレミス システムにわたる[コネクタ](../connectors/apis-list.md)が多数存在します。 どのロジック アプリも、"Dynamics CRM にアカウントが追加されたとき" のようなトリガーで開始されます。 トリガーが起動すると、ワークフローによりアクション、変換、条件付きロジックの組み合わせが実行されます。 Logic Apps は、特にプロセスが外部のシステムまたは API との対話を必要とする場合、プロセス内のさまざまな Azure Functions を調整するときの最適な選択肢です。

Logic Apps の操作を開始するには、[最初のロジック アプリの作成](quickstart-create-first-logic-app-workflow.md)から始めます。 Logic Apps の技術情報の詳細については、「[Azure Logic Apps のワークフロー定義言語のスキーマ参照](logic-apps-workflow-definition-language.md)」を参照してください。

## <a name="what-is-azure-functions"></a>Azure Functions とは

Azure Functions は、コード ("関数") をクラウドで手軽に実行できるサービスです。 アプリ全体や必要なインフラストラクチャを気にせずに、現在の問題に必要なコードのみを記述できます。 Functions は開発の生産性をさらに向上させることができ、またユーザーは C#、F#、Node.js、Python、PHP などの任意の開発言語を使用できます。 支払いはコードの実行時間に対してだけであり、Azure が必要に応じてスケールします。

Azure Functions の使用を開始するには、最初に「[Azure Portal で初めての関数を作成する](../azure-functions/functions-get-started.md)」を参照してください。 Functions の技術情報の詳細については、「[Azure Functions の開発者向けガイド](../azure-functions/functions-reference.md)」を参照してください。

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Azure でサーバーレス アプリを構築してデプロイするにはどうすればよいですか。

Azure には、サーバーレス アプリの開発、デプロイ、管理を行うための豊富なツールが用意されています。 [Visual Studio のツール](logic-apps-serverless-get-started-vs.md)、または [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) を使用して、Azure portal でアプリを直接ビルドすることができます。 アプリをビルドしたら、[Azure Resource Manager テンプレートを使用してそのアプリをすばやくデプロイ](logic-apps-deploy-azure-resource-manager-templates.md)できます。 Azure では、Azure portal からのアクセス、API または SDK 経由のアクセス、または Azure Monitor ログおよび Application Insights に対する統合ツールでのアクセスが可能な監視が提供されています。

## <a name="next-steps"></a>次のステップ

* [Visual Studio でサーバーレス アプリをビルドする](logic-apps-serverless-get-started-vs.md)
* [サーバーレスを使用して Customer Insights ダッシュボードを作成する](logic-apps-scenario-social-serverless.md)
* [ロジック アプリ デプロイを自動化する](logic-apps-azure-resource-manager-templates-overview.md)