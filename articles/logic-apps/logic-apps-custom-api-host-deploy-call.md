---
title: カスタム Web API および REST API の呼び出し
description: Azure Logic Apps からの Web API と REST API の呼び出し
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659774"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>カスタム API をデプロイして Azure Logic Apps のワークフローから呼び出す

ロジック アプリのワークフローで使用する[独自の API を作成](./logic-apps-create-api-app.md)したら、これらの API を呼び出す前にまずデプロイする必要があります。 API は [Web アプリ](../app-service/overview.md)としてデプロイできますが、[API アプリ](../app-service/app-service-web-tutorial-rest-api.md)としてデプロイすることを検討してください。クラウドやオンプレミスで API を構築、ホスト、利用するとき、作業が簡単になります。 API のコードを変更する必要はありません。コードを API アプリにデプロイするだけです。 API は [Azure App Service](../app-service/overview.md) でホストできます。Azure App Service は、高い拡張性と容易な API ホスティングを提供するサービスとしてのプラットフォーム (PaaS) です。

ロジック アプリからあらゆる API を呼び出せますが、[Swagger メタデータ](https://swagger.io/specification/)を追加すると最も効率的になります。このメタデータは API の操作とパラメーターを説明するものです。 この Swagger ドキュメントを使用することで、API をより簡単にロジック アプリに統合し、いっそう有効にすることができます。

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Web アプリまたは API アプリとして API をデプロイする

ロジック アプリからカスタム API を呼び出すには、先に、Web アプリまたは API アプリとして Azure App Service に API をデプロイします。 ロジック アプリ デザイナーで Swagger ドキュメントを読めるようにするには、API 定義プロパティを設定し、Web アプリまたは API アプリの [[クロスオリジン リソース共有 (CORS) を使用する]](../app-service/overview.md) をオンにします。

1. [Azure Portal](https://portal.azure.com) で、Web アプリまたは API アプリを選択します。

2. アプリ メニューが開いたら、 **[API]** で **[API 定義]** を選択します。 **[API 定義の場所]** を swagger.json ファイルの URL に設定します。

   通常、URL は `https://{name}.azurewebsites.net/swagger/docs/v1)` 形式で表示されます。

   ![カスタム API の Swagger ドキュメントへのリンク](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. **[API]** で **[CORS]** を選択します。 **[許可されたオリジン]** の CORS ポリシーを **'*'** (すべて許可) に設定します。

   この設定により、ロジック アプリ デザイナーからの要求が許可されます。

   ![ロジック アプリ デザイナーからカスタム API への要求を許可する](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

詳細については、[Azure App Service での CORS を使用した RESTful API のホスト](../app-service/app-service-web-tutorial-rest-api.md)に関する記事をご覧ください。

## <a name="call-your-custom-api-from-logic-app-workflows"></a>ロジック アプリ ワークフローからカスタム API を呼び出す

API 定義プロパティと CORS を設定すると、カスタム API のトリガーとアクションをロジック アプリ ワークフローに追加できるようになります。 

*  OpenAPI URL を含む Web サイトを表示するには、Logic Apps Designer でサブスクリプション Web サイトを閲覧できます。

*  Swagger ドキュメントでポイントし、利用可能なアクションと入力を表示するには、[HTTP + Swagger アクション](../connectors/connectors-native-http-swagger.md)を使用します。

*  [HTTP アクション](../connectors/connectors-native-http.md)を使用した要求をいつでも作成し、Swagger ドキュメントがない API や Swagger ドキュメントを公開しない API を含む、あらゆる API を呼び出すことができます。

## <a name="next-steps"></a>次のステップ

* [カスタム コネクタの概要](../logic-apps/custom-connector-overview.md)
