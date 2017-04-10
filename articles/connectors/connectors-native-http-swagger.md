
---
title: "Logic Apps に HTTP + Swagger アクションを追加する | Microsoft Docs"
description: "HTTP + Swagger アクションと操作の概要"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c0edbe421050ad46f6d31fd6416df4b344b233ad
ms.openlocfilehash: ade380b7fc6adfb929f42c0e6c75b3fa613c45b1
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-http--swagger-action"></a>HTTP + Swagger アクションの概要
HTTP + Swagger アクションでは、 [Swagger ドキュメント](https://swagger.io)を使用して REST エンドポイントへのファースト クラス コネクタを作成できます。 ロジック アプリ デザイナーの優れたエクスペリエンスを使用し、ロジック アプリを拡張して任意の REST エンドポイントを呼び出すこともできます。

ロジック アプリで HTTP + Swagger アクションの使用を開始する方法については、 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事をご覧ください。

- - -
## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>トリガーまたはアクションとしての HTTP + Swagger の使用
HTTP + Swagger トリガーおよびアクションは [HTTP アクション](connectors-native-http.md)と同様に機能しますが、[Swagger メタデータ](https://swagger.io)を使用してデザイナーで API と出力の状態を表示することでデザイン エクスペリエンスが向上します。 また、HTTP + Swagger をトリガーとして使用することもできます。 ポーリング トリガーを実装する場合は、「 [Logic Apps で使用するカスタム API の作成](../logic-apps/logic-apps-create-api-app.md#polling-triggers)」に記載されているポーリング パターンに従ってください。

[ロジック アプリのトリガーとアクションの詳細についてはこちらを参照してください](connectors-overview.md)

ロジック アプリでワークフローのアクションとして HTTP + Swagger 操作を使用する方法の例を次に示します。

1. **[新しいステップ]** をクリックします。
2. **[アクションの追加]**を選択します。
3. アクションの検索ボックスに「 **swagger** 」と入力して、HTTP + Swagger アクションを表示します。
   
    ![HTTP + Swagger アクションの選択](./media/connectors-native-http-swagger/using-action-1.png)
4. Swagger ドキュメントの URL を入力します。
   
   * ロジック アプリ デザイナーで使用するには、この URL が HTTPS エンドポイントであり、CORS が有効になっている必要があります。
   * Swagger ドキュメントがこの要件を満たしていない場合は、 [CORS を有効にした Azure Storage](#hosting-swagger-from-storage) を使用してドキュメントを格納できます。
5. **[次へ]** をクリックし、Swagger ドキュメントを読み込んで表示します。
6. HTTP 呼び出しに必要なすべてのパラメーターを追加します。
   
    ![Complete HTTP action](./media/connectors-native-http-swagger/using-action-2.png)
7. ツール バーの左上隅にある **[保存]** をクリックすると、ロジック アプリが保存されて発行 (アクティブ化) されます。

### <a name="host-swagger-from-azure-storage"></a>Azure Storage からの Swagger のホスト
ホストされていない Swagger ドキュメントや、デザイナーのセキュリティとクロス オリジンの要件を満たしていない Swagger ドキュメントを参照することが必要になることがあります。 この問題を解決するには、Swagger ドキュメントを Azure Storage に格納し、CORS を有効にすることで、ドキュメントを参照します。  

Azure Storage で Swagger ドキュメントを作成、構成し、格納する手順を次に示します。

1. [Azure BLOB ストレージでストレージ アカウントを作成します](../storage/storage-create-storage-account.md)  (これを行うには、アクセス許可を **[パブリック アクセス]** に設定します)。
2. BLOB で CORS を有効にします。 [こちらの PowerShell スクリプト](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) を使用して、この設定を自動的に構成できます。
3. Swagger ファイルを BLOB にアップロードします。 これは、[Azure Portal](https://portal.azure.com)、または [Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールから実行できます。
4. Azure BLOB ストレージ内のドキュメントへの HTTPS リンクを参照します  (リンクは `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*` の形式に従います)。

## <a name="technical-details"></a>技術的な詳細
この HTTP + Swagger コネクタでサポートされているトリガーとアクションの詳細を次に示します。

## <a name="http--swagger-triggers"></a>HTTP + Swagger トリガー
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 [トリガーの詳細についてはこちらを参照してください。](connectors-overview.md) HTTP + Swagger コネクタにはトリガーが&amp;1; つあります。

| トリガー | Description |
| --- | --- |
| HTTP + Swagger |HTTP 呼び出しを実行し、応答コンテンツを返します |

## <a name="http--swagger-actions"></a>HTTP + Swagger アクション
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 [アクションの詳細についてはこちらを参照してください。](connectors-overview.md) HTTP + Swagger コネクタには、使用可能なアクションが&amp;1; つあります。

| アクション | Description |
| --- | --- |
| HTTP + Swagger |HTTP 呼び出しを実行し、応答コンテンツを返します |

### <a name="action-details"></a>アクションの詳細
HTTP + Swagger コネクタには、使用可能なアクションが&1; つ用意されています。 各アクションの情報、必須および任意の入力フィールド、アクションの使用に伴う出力の詳細を次に示します。

#### <a name="http--swagger"></a>HTTP + Swagger
Swagger メタデータを使用して HTTP 送信要求を行います。
アスタリスク (*) は、必須フィールドであることを意味します。

| 表示名 | プロパティ名 | Description |
| --- | --- | --- |
| メソッド* |静的メソッド |使用する HTTP 動詞 |
| URI* |uri |HTTP 要求の URI |
| ヘッダー |ヘッダー |含める HTTP ヘッダーの JSON オブジェクト |
| 本文 |本文 |HTTP 要求の本文 |
| 認証 |authentication |要求に使用する認証 詳細については、[HTTP](connectors-native-http.md#authentication) に関するページをご覧ください。 |

**出力の詳細**

HTTP 応答

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| headers |オブジェクト |応答ヘッダー |
| 本文 |オブジェクト |応答オブジェクト |
| 状態コード |int |HTTP 状態コード |

### <a name="http-responses"></a>HTTP 応答
さまざまなアクションを呼び出すと、特定の応答を受け取る場合があります。 次の表に、対応する応答と説明を示します。

| Name | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました。 |

- - -
## <a name="next-steps"></a>次のステップ
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md) してください。 [API のリスト](apis-list.md)を参照すると、Logic Apps で使用可能な他のコネクタについて確認できます。


