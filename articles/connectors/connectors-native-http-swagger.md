---
title: Azure Logic Apps からの REST エンドポイントの呼び出し | Microsoft Docs
description: Azure Logic Apps で HTTP + Swagger コネクタを使用して REST エンドポイントと通信するタスクとワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
tags: connectors
ms.topic: article
ms.date: 07/18/2016
ms.openlocfilehash: 9408b66f74391b080ef46c758b07850b2ae8de57
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893398"
---
# <a name="call-rest-endpoints-with-http--swagger-connector-in-azure-logic-apps"></a>Azure Logic Apps で HTTP + Swagger コネクタを使用して REST エンドポイントを呼び出す

ロジック アプリのワークフローで HTTP + Swagger アクションを使用すると、[Swagger ドキュメント](https://swagger.io)から REST エンドポイントへのファースト クラス コネクタを作成できます。 ロジック アプリ デザイナーの優れたエクスペリエンスを使用し、ロジック アプリを拡張して任意の REST エンドポイントを呼び出すこともできます。

コネクタ付きのロジック アプリを作成する方法については、「[新しいロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)」を参照してください。

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>トリガーまたはアクションとしての HTTP + Swagger の使用

HTTP + Swagger トリガーおよびアクションは、[HTTP アクション](connectors-native-http.md)と同様に機能しますが、ロジック アプリ デザイナーでは、[Swagger メタデータ](https://swagger.io)から API の構造と出力を公開することでエクスペリエンスが向上します。 また、HTTP + Swagger コネクタをトリガーとして使用することもできます。 ポーリング トリガーを実装する場合は、「[Logic Apps から他の API、サービス、およびシステムを呼び出すカスタム API の作成](../logic-apps/logic-apps-create-api-app.md#polling-triggers)」に記載されているポーリング パターンに従ってください。

では、[ロジック アプリのトリガーとアクション](../connectors/apis-list.md)について詳しく説明します。

ロジック アプリでワークフローのアクションとして HTTP + Swagger 操作を使用する方法の例を次に示します。

1. **[新しいステップ]** をクリックします。
2. **[アクションの追加]** を選択します。
3. アクションの検索ボックスに「 **swagger** 」と入力して、HTTP + Swagger アクションを表示します。
   
    ![HTTP + Swagger アクションの選択](./media/connectors-native-http-swagger/using-action-1.png)
4. Swagger ドキュメントの URL を入力します。
   
   * ロジック アプリ デザイナーで使用するには、この URL が HTTPS エンドポイントであり、CORS が有効になっている必要があります。
   * Swagger ドキュメントがこの要件を満たしていない場合は、CORS を有効にした Azure Storage を使用してドキュメントを格納できます。
5. **[次へ]** をクリックし、Swagger ドキュメントを読み込んで表示します。
6. HTTP 呼び出しに必要なすべてのパラメーターを追加します。
   
    ![Complete HTTP action](./media/connectors-native-http-swagger/using-action-2.png)
7. ロジック アプリを保存して発行するには、デザイナーのツールバーの **[保存]** をクリックします。

### <a name="host-swagger-from-azure-storage"></a>Azure Storage からの Swagger のホスト
ホストされていない Swagger ドキュメントや、デザイナーのセキュリティとクロス オリジンの要件を満たしていない Swagger ドキュメントを参照することが必要になることがあります。 この問題を解決するには、Swagger ドキュメントを Azure Storage に格納し、CORS を有効にすることで、ドキュメントを参照します。  

Azure Storage で Swagger ドキュメントを作成、構成し、格納する手順を次に示します。

1. [Azure BLOB ストレージでストレージ アカウントを作成します](../storage/common/storage-create-storage-account.md)  この手順を実行するには、アクセス許可を **[パブリック アクセス]** に設定します。

2. BLOB で CORS を有効にします。 

   [この PowerShell スクリプト](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) を使用して、この設定を自動的に構成できます。

3. Swagger ファイルを BLOB にアップロードします。 

   この手順は、[Azure Portal](https://portal.azure.com) から、または [Azure ストレージ エクスプローラー](https://storageexplorer.com/)などのツールから実行できます。

4. Azure BLOB ストレージ内のドキュメントへの HTTPS リンクを参照します  

   リンクは、次の形式を使用します。

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>技術的な詳細
この HTTP + Swagger コネクタでサポートされているトリガーとアクションの詳細を次に示します。

## <a name="http--swagger-triggers"></a>HTTP + Swagger トリガー
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 HTTP + Swagger コネクタにはトリガーが 1 つあります。 トリガーの詳細については[こちら](../connectors/apis-list.md)を参照してください。

| トリガー | 説明 |
| --- | --- |
| HTTP + Swagger |HTTP 呼び出しを実行し、応答コンテンツを返します |

## <a name="http--swagger-actions"></a>HTTP + Swagger アクション
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 HTTP + Swagger コネクタには、使用可能なアクションが 1 つあります。 アクションの詳細については[こちら](../connectors/apis-list.md)を参照してください。

| Action | 説明 |
| --- | --- |
| HTTP + Swagger |HTTP 呼び出しを実行し、応答コンテンツを返します |

### <a name="action-details"></a>アクションの詳細
HTTP + Swagger コネクタには、使用可能なアクションが 1 つ用意されています。 各アクションの情報、必須および任意の入力フィールド、アクションの使用に伴う出力の詳細を次に示します。

#### <a name="http--swagger"></a>HTTP + Swagger
Swagger メタデータを使用して HTTP 送信要求を行います。
アスタリスク (*) は、必須フィールドであることを意味します。

| 表示名 | プロパティ名 | 説明 |
| --- | --- | --- |
| メソッド* |method |使用する HTTP 動詞 |
| URI* |uri |HTTP 要求の URI |
| headers |headers |含める HTTP ヘッダーの JSON オブジェクト |
| 本文 |body |HTTP 要求の本文 |
| Authentication |[認証] |要求に使用する認証 詳細については、「[HTTP コネクタ](connectors-native-http.md#authentication)」を参照してください。 |

**出力の詳細**

HTTP 応答

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |オブジェクト |応答ヘッダー |
| 本文 |オブジェクト |応答オブジェクト |
| 状態コード |int |HTTP 状態コード |

### <a name="http-responses"></a>HTTP 応答
さまざまなアクションを呼び出すと、特定の応答を受け取る場合があります。 次の表に、対応する応答と説明を示します。

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました。 |

## <a name="next-steps"></a>次の手順

* [ロジック アプリを作成します](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [他のコネクタを見つけます](apis-list.md)
