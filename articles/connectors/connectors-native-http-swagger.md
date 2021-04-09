---
title: Azure Logic Apps から REST エンドポイントに接続する
description: Azure Logic Apps を使用して、自動化されたタスク、プロセス、およびワークフロー内で REST エンドポイントを監視します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 810aaae9634a7de8d07b6d49edd0c6c2eda96754
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730883"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して REST エンドポイントを呼び出す

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と組み込み HTTP + Swagger コネクタを使用すると、ロジック アプリを構築することで、[Swagger ファイル](https://swagger.io)を介して任意の REST エンドポイントを定期的に呼び出すワークフローを自動化できます。 HTTP + Swagger トリガーおよびアクションは [HTTP トリガーおよびアクション](connectors-native-http.md)と同様に機能しますが、Swagger ファイルで記述された API の構造と出力を公開することにより、ロジック アプリ デザイナーでのエクスペリエンスが向上します。 ポーリング トリガーを実装する場合は、[ロジック アプリから他の API、サービス、システムを呼び出すためのカスタム API の作成](../logic-apps/logic-apps-create-api-app.md#polling-triggers)に関するページで説明されているポーリング パターンに従ってください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ターゲットの REST エンドポイントが記述されている (OpenAPI ではなく) Swagger ファイルの URL

  通常、コネクタが機能するためには、REST エンドポイントがこの条件を満たしている必要があります。

  * Swagger ファイルは、パブリックにアクセス可能な HTTPS URL でホストされている必要があります。
  
  * Swagger ファイルでは、定義に各操作の `operationID` が含まれている必要があります。 そうでない場合、コネクタによって Swagger ファイル内の最後の操作のみが表示されます。 

  * Swagger ファイルでは、[クロスオリジン リソース共有 (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) が有効になっている必要があります。

  参照する Swagger ファイルがホストされていない場合やセキュリティとクロスオリジンの要件を満たしていない場合は、[Azure ストレージ アカウント内の BLOB コンテナーにその Swagger ファイルをアップロード](#host-swagger)し、そのストレージ アカウントで CORS を有効にすると、そのファイルを参照できるようになります。

  このトピックの例では、[Cognitive Services アカウントとアクセス キー](../cognitive-services/cognitive-services-apis-create-account.md)を必要とする [Cognitive Services Face API](../cognitive-services/face/overview.md) を使用します。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

* ターゲット エンドポイントの呼び出し元となるロジック アプリ。 HTTP + Swagger トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 HTTP + Swagger アクションを使用するには、任意のトリガーで対象のロジック アプリを起動します。 この例では、最初のステップとして HTTP + Swagger トリガーを使用しています。

## <a name="add-an-http--swagger-trigger"></a>HTTP + Swagger トリガーの追加

この組み込みトリガーは、REST API を記述した Swagger ファイルの URL に HTTP 要求を送信し、そのファイルの内容を含む応答を返します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. デザイナーの検索ボックスに、フィルターとして「swagger」と入力します。 **[トリガー]** の一覧から、 **[HTTP + Swagger]** トリガーを選択します。

   ![HTTP + Swagger トリガーを選択する](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. **[Swagger エンドポイント URL]** ボックスに Swagger ファイルの URL を入力し、 **[次へ]** を選択します。

   この例では、[Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) に対して、米国西部リージョンにある Swagger URL を使用します。

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![ロジック アプリ デザイナーのスクリーンショット。[HTTP + Swagger] トリガーで [Swagger エンドポイント URL] プロパティが URL 値に設定されています。](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Swagger ファイルで記述された操作がデザイナーに表示されたら、使用する操作を選択します。

   ![ロジック アプリ デザイナーのスクリーンショット。[HTTP + Swagger] トリガーと Swagger 操作を表示する一覧を確認できます。](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. エンドポイント呼び出しに含めるトリガー パラメーターの値を指定します (パラメーターは、選択した操作によって異なります)。 トリガーでエンドポイントを呼び出す頻度を設定します。

   この例では、トリガー名を "HTTP + Swagger trigger: Face - Detect" に変更して、このステップの名前をよりわかりやすくします。

   ![ロジック アプリ デザイナーのスクリーンショット。[HTTP + Swagger] トリガーに [Face - Detect] 操作が表示されています。](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

   HTTP + Swagger に使用できる認証の種類の詳細については、[送信呼び出しへの認証の追加](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)に関するページを参照してください。

1. トリガーが起動したときに実行されるアクションを使用して、ロジック アプリのワークフローを引き続き構築します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="add-an-http--swagger-action"></a>HTTP + Swagger アクションの追加

この組み込みアクションは、REST API を記述した Swagger ファイルの URL に HTTP 要求を送信し、そのファイルの内容を含む応答を返します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーでロジック アプリを開きます。

1. HTTP + Swagger アクションを追加するステップで、 **[新しいステップ]** を選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. デザイナーの検索ボックスに、フィルターとして「swagger」と入力します。 **[アクション]** の一覧で、 **[HTTP + Swagger]** アクションを選択します。

    ![HTTP + Swagger アクションの選択](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. **[Swagger エンドポイント URL]** ボックスに Swagger ファイルの URL を入力し、 **[次へ]** を選択します。

   この例では、[Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) に対して、米国西部リージョンにある Swagger URL を使用します。

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Swagger エンドポイントの URL を入力する](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Swagger ファイルで記述された操作がデザイナーに表示されたら、使用する操作を選択します。

   ![Swagger ファイル内の操作](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. エンドポイント呼び出しに含めるアクション パラメーターの値を指定します (パラメーターは、選択した操作によって異なります)。

   この例では、パラメーターがありませんが、アクション名を "HTTP + Swagger action: Face - Identify" に変更して、このステップの名前をよりわかりやすくします。

   ![操作の詳細](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

   HTTP + Swagger に使用できる認証の種類の詳細については、[送信呼び出しへの認証の追加](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)に関するページを参照してください。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Azure Storage での Swagger のホスト

ホストされていない、またはセキュリティとクロスオリジンの要件を満たしていない Swagger ファイルは、Azure ストレージ アカウント内の BLOB コンテナーに Swagger ファイルをアップロードし、そのストレージ アカウントで CORS を有効にすると参照できます。 Swagger ファイルを作成および設定し、Azure Storage に保存するには、次の手順に従います。

1. [Azure Storage アカウント](../storage/common/storage-account-create.md)を作成します。

1. ここで、BLOB に対して CORS を有効にします。 ストレージ アカウント メニューの **[CORS]** を選択します。 **[Blob service]** タブで以下の値を指定し、 **[保存]** を選択します。

   | プロパティ | 値 |
   |----------|-------|
   | **許可されたオリジン** | `*` |
   | **許可されたメソッド** | `GET`, `HEAD`, `PUT` |
   | **許可されたヘッダー** | `*` |
   | **公開されるヘッダー** | `*` |
   | **最長有効期間** (秒) | `200` |
   |||

   この例では [Azure portal](https://portal.azure.com) を使用していますが、[Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用することもできます。また、このサンプル [PowerShell スクリプト](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)を使用して、この設定を自動的に構成することもできます。

1. [BLOB コンテナーを作成します](../storage/blobs/storage-quickstart-blobs-portal.md)。 コンテナーの **[概要]** ウィンドウで、 **[アクセス レベルを変更します]** を選択します。 **[パブリック アクセス レベル]** の一覧で、 **[BLOB (BLOB 専用の匿名読み取りアクセス)]** を選択し、 **[OK]** を選択します。

1. [Azure portal](https://portal.azure.com) または [Azure Storage Explorer](https://storageexplorer.com/) のいずれかから、[Swagger ファイルを BLOB コンテナーにアップロード](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)します。

1. BLOB コンテナー内のファイルを参照するには、次の形式の HTTPS URL を Azure Storage Explorer から取得します。URL の大文字と小文字は区別されます。

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>コネクタのレファレンス

ここでは、以下の情報を返す HTTP + Swagger トリガーまたはアクションからの出力の詳細情報を示します。 HTTP + Swagger 呼び出しにより、次の情報が返されます。

| プロパティ名 | Type | 説明 |
|---------------|------|-------------|
| headers | object | 要求のヘッダー |
| body | object | JSON オブジェクト | 要求の本文の内容を含むオブジェクト |
| status code | INT | 要求の状態コード |
|||

| status code | 説明 |
|-------------|-------------|
| 200 | [OK] |
| 202 | 承認済み |
| 400 | 正しくない要求 |
| 401 | 権限がありません |
| 403 | Forbidden |
| 404 | 見つかりません |
| 500 | 内部サーバー エラー。 不明なエラーが発生しました。 |
|||

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
