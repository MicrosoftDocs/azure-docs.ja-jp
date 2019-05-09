---
title: Azure Logic Apps からの Web API と REST API のデプロイと呼び出し | Microsoft Docs
description: Azure Logic Apps でシステム統合ワークフローのために Web API と REST API をデプロイして呼び出します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: a9049ba1fbd7d3bdce061d277f6a7a02d9b1e4b7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880066"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>カスタム API をデプロイして Azure Logic Apps のワークフローから呼び出す

ロジック アプリ ワークフローで使用するために[カスタム API を作成](./logic-apps-create-api-app.md)したら、API をデプロイする必要があります。デプロイ後に呼び出しが可能になります。 API は [Web アプリ](../app-service/overview.md)としてデプロイできますが、[API アプリ](../app-service/app-service-web-tutorial-rest-api.md)としてデプロイすることを検討してください。クラウドやオンプレミスで API を構築、ホスト、利用するとき、作業が簡単になります。 API のコードを変更する必要はありません。コードを API アプリにデプロイするだけです。 API は [Azure App Service](../app-service/overview.md) でホストできます。Azure App Service は、高い拡張性と容易な API ホスティングを提供するサービスとしてのプラットフォーム (PaaS) です。

ロジック アプリからあらゆる API を呼び出せますが、[OpenAPI (旧称 Swagger) メタデータ](https://swagger.io/specification/)を追加すると最も効率的になります。このメタデータは API の操作とパラメーターを説明するものです。 この OpenAPI ファイルを利用することで、API とロジック アプリとより簡単に統合され、効率的に連動します。

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Web アプリまたは API アプリとして API をデプロイする

ロジック アプリからカスタム API を呼び出すには、先に、Web アプリまたは API アプリとして Azure App Service に API をデプロイします。 また、Logic Apps Designer で OpenAPI ファイルを読めるように、API 定義プロパティを設定し、Web アプリまたは API アプリの [[クロスオリジン リソース共有 (CORS) を使用する]](../app-service/overview.md) をオンにします。

1. [Azure Portal](https://portal.azure.com) で、Web アプリまたは API アプリを選択します。

2. アプリ メニューが開いたら、**[API]** で **[API 定義]** を選択します。 **[API 定義の場所]** を OpenAPI の swagger.json ファイルの URL に設定します。

   通常、URL は `https://{name}.azurewebsites.net/swagger/docs/v1)` 形式で表示されます。

   ![カスタム API の OpenAPI ファイルへのリンク](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. **[API]** で **[CORS]** を選択します。 **[許可されたオリジン]** の CORS ポリシーを **'*'** (すべて許可) に設定します。

   この設定により、ロジック アプリ デザイナーからの要求が許可されます。

   ![ロジック アプリ デザイナーからカスタム API への要求を許可する](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

詳細については、[Azure App Service での CORS を使用した RESTful API のホスト](../app-service/app-service-web-tutorial-rest-api.md)に関する記事をご覧ください。

## <a name="call-your-custom-api-from-logic-app-workflows"></a>ロジック アプリ ワークフローからカスタム API を呼び出す

API 定義プロパティと CORS を設定すると、カスタム API のトリガーとアクションをロジック アプリ ワークフローに追加できるようになります。 

*  OpenAPI URL を含む Web サイトを表示するには、Logic Apps Designer でサブスクリプション Web サイトを閲覧できます。

*  OpenAPI ドキュメントでポイントし、利用可能なアクションと入力を表示するには、[HTTP + Swagger アクション](../connectors/connectors-native-http-swagger.md)を使用します。

*  [HTTP アクション](../connectors/connectors-native-http.md)を使用した要求をいつでも作成し、OpenAPI ドキュメントがない API や OpenAPI ドキュメントを公開しない API を含む、あらゆる API を呼び出すことができます。

## <a name="next-steps"></a>次の手順

* [カスタム コネクタの概要](../logic-apps/custom-connector-overview.md)