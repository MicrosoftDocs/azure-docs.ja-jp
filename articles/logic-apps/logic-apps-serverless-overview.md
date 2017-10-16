---
title: "Azure Serverless の概要 | Microsoft Docs"
description: "インフラストラクチャについて考えなくても、クラウド内の強力なソリューションを作成します。"
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
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 6803e22a78e27c15ff4fec301cd5bdd55aacd3e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Functions と Logic Apps を含む Azure Serverless の概要

サーバーレス アプリケーションには、開発速度の向上、必要なコードの削減、およびスケールの簡素化という利点があります。  この記事では、サーバーレス ソリューションのさまざまな属性と Azure Serverless サービスについて説明します。

## <a name="what-is-serverless"></a>サーバーレスとは

サーバーレスは、サーバーがないことを示すわけではなく、開発者がサーバーについて心配する必要がないことを示しているだけです。  従来のアプリケーション開発の大きな部分は、アプリケーションの要求を満たすためのソリューションのスケーリング、ホスティング、監視に関する質問に答えるところにありました。  サーバーレスでは、これらの質問はソリューションの一部として対応されます。  さらに、サーバーレス アプリケーションは消費ベースのプランに基づいて課金されます。  アプリケーションが使用されなければ、使用料は発生しません。  これらの機能により、開発者はソリューションのビジネス ロジックだけに重点を置くことができます。

サーバーレスに関する Azure のコア サービスは、[Azure Functions](https://azure.microsoft.com/services/functions/) と [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) です。  これらのソリューションはどちらも上の原則に従っており、開発者が最小のコードで強力なクラウド アプリケーションを構築できるようにします。

## <a name="what-are-azure-functions"></a>Azure Functions とは

Azure Functions は、小規模なコード ("関数") をクラウドで手軽に実行できるソリューションです。 目の前の問題に必要なコードだけを記述すればよく、アプリケーション全体や、コードを実行するインフラストラクチャのことを考える必要がありません。 Functions は開発の生産性をさらに向上させることができ、またユーザーは C#、F#、Node.js、Python、PHP などの任意の開発言語を使用できます。 支払いはコードの実行時間に対してだけであり、Azure が必要に応じてスケールします。

Azure Functions の利用をすぐに開始する場合は、「 [初めての Azure 関数の作成](../azure-functions/functions-create-first-azure-function.md)」を参照してください。 Azure Functions の詳細な技術情報をお探しの場合は、 [開発者向けリファレンス](../azure-functions/functions-reference.md)を参照してください。

## <a name="what-are-azure-logic-apps"></a>Azure Logic Apps とは

Azure Logic Apps は、クラウド内のスケーラブルな統合やワークフローを簡略化して実装するための方法を提供します。 また、ワークフローと呼ばれる一連のステップとしてプロセスをモデル化して自動化するためのビジュアル デザイナーも用意されています。  サーバーレス アプリを他の API にすばやく接続するための、クラウドおよびオンプレミス サービスにわたる[多数のコネクタ](../connectors/apis-list.md)が存在します。  ロジック アプリはトリガー ("Dynamics CRM にアカウントが追加されたとき" など) によって起動することができ、その後も数多くのアクション、変換、条件ロジックを組み合わせて起動できます。  Logic Apps は、特にプロセスが外部のシステムまたは API との対話を必要とする場合、プロセス内のさまざまな Azure Functions を調整するときの最適な選択肢です。

Logic Apps の操作を開始するには、[最初のロジック アプリの作成](logic-apps-create-a-logic-app.md)から始めます。  Logic Apps に関するより詳細な技術情報をお探しの場合は、[開発者向けリファレンス](logic-apps-workflow-actions-triggers.md)を参照してください。

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Azure でサーバーレス アプリケーションを構築してデプロイするにはどうすればよいですか。

Azure は、サーバーレス アプリの開発、デプロイ、管理の全体にわたる、豊富な機能を持つツール セットを提供しています。  アプリは Azure Portal で直接、または [Visual Studio のツール](logic-apps-serverless-get-started-vs.md)を使用して構築できます。  アプリケーションが開発されたら、そのアプリケーションを[直ちにデプロイ](logic-apps-create-deploy-template.md)できます。  Azure ではまた、サーバーレス アプリに対する監視も提供されます。  この監視には、API または SDK 経由で、あるいは OMS と Application Insights に対する統合ツールを使用して Azure Portal からアクセスできます。

## <a name="next-steps"></a>次のステップ

* [Visual Studio でサーバーレス アプリの構築を開始する](logic-apps-serverless-get-started-vs.md)
* [Serverless を使用して Customer Insights ダッシュボードを作成する](logic-apps-scenario-social-serverless.md)
* [ロジック アプリのデプロイ テンプレートを構築する](logic-apps-create-deploy-template.md)