---
title: HTTP または HTTPS を使用してサービス エンドポイントを呼び出す
description: Azure Logic Apps からサービス エンドポイントに送信 HTTP または HTTPS 要求を送信します
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/18/2021
tags: connectors
ms.openlocfilehash: dab5b755347e46d8d509e8014bba8f496ca9c900
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719442"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Azure Logic Apps から HTTP または HTTPS でサービス エンドポイントを呼び出す

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) および組み込みの HTTP トリガーまたはアクションを使用すると、HTTP または HTTPS 経由で他のサービスやシステム上のエンドポイントに送信要求を送信できる自動化されたタスクとワークフローを作成できます。 代わりに受信 HTTPS 呼び出しを受信してそれに応答するには、組み込みの [Request トリガーと Response アクション](../connectors/connectors-native-reqres.md)を使用します。

たとえば、Web サイトのサービス エンドポイントは、そのエンドポイントを特定のスケジュールで確認することによって監視できます。 Web サイトの停止など、そのエンドポイントで指定されたイベントが発生すると、そのイベントによってロジック アプリのワークフローがトリガーされ、そのワークフロー内のアクションが実行されます。

* 定期的なスケジュールでエンドポイントを調べる、つまり "*ポーリング*" するには、ワークフローの最初のステップとして [HTTP トリガーを追加](#http-trigger)します。 トリガーによるエンドポイントの調査ごとに、トリガーからそのエンドポイントに対して、呼び出しまたは *要求* の送信が行われます。 エンドポイントの応答によって、ロジック アプリのワークフローが実行されるかどうかが決定します。 エンドポイントの応答からの任意のコンテンツが、トリガーによってロジック アプリのアクションに渡されます。

* ワークフロー内のどこか他の場所からエンドポイントを呼び出すには、[HTTP アクションを追加します](#http-action)。 エンドポイントの応答によって、ワークフローの以降のアクションの実行方法が決まります。

この記事では、ロジック アプリが他のサービスやシステムに送信呼び出しを送信できるように HTTP トリガーと HTTP アクションを使用する方法について説明します。

[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (以前の Secure Sockets Layer (SSL))、自己署名証明書、[Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml) などの、ロジック アプリからの送信呼び出しの暗号化、セキュリティ、承認については、[アクセスとデータのセキュリティ保護 - 他のサービスやシステムへの送信呼び出しへのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 呼び出すターゲット エンドポイントの URL

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

* ターゲット エンドポイントの呼び出し元となるロジック アプリ。 HTTP トリガーで開始するには、[空のロジック アプリを作成します](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 HTTP アクションを使用するには、任意のトリガーで対象のロジック アプリを起動します。 この例では、最初のステップとして HTTP トリガーを使用します。

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP トリガーの追加

この組み込みトリガーは、エンドポイントに指定された URL に対して HTTP 呼び出しを実行し、応答を返します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. デザイナーの検索ボックスで、 **[組み込み]** を選択します。 検索ボックスに、フィルターとして「`http`」と入力します。 **[トリガー]** の一覧から、 **[HTTP]** トリガーを選択します。

   ![HTTP トリガーを選択する](./media/connectors-native-http/select-http-trigger.png)

   この例では、トリガー名を "HTTP trigger" に変更して、このステップにわかりやすい名前を付けています。 また、この例では後で HTTP アクションを追加します。どちらの名前も一意である必要があります。

1. ターゲット エンドポイントへの呼び出しに含める [HTTP トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)の値を指定します。 トリガーでターゲット エンドポイントを確認する頻度を設定します。

   ![HTTP トリガー パラメーターを入力する](./media/connectors-native-http/http-trigger-parameters.png)

   **[なし]** 以外の認証の種類を選択した場合、認証設定は、選択に基づいて変化します。 HTTP に使用できる認証の種類の詳細については、以下のトピックを参照してください。

   * [送信呼び出しに認証を追加する](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [マネージド ID を利用してリソースへのアクセスを認証する](../logic-apps/create-managed-service-identity.md)

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

1. トリガーが起動したときに実行されるアクションを使用して、ロジック アプリのワークフローを引き続き構築します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

<a name="http-action"></a>

## <a name="add-an-http-action"></a>HTTP アクションの追加

この組み込みアクションは、エンドポイントに指定された URL に対して HTTP 呼び出しを実行し、応答を返します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーでロジック アプリを開きます。

   この例では、最初のステップとして HTTP トリガーを使用します。

1. HTTP アクションを追加するステップで、 **[新しいステップ]** を選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「`http`」と入力します。 **[アクション]** の一覧で、 **[HTTP]** アクションを選択します。

   ![HTTP アクションを選択する](./media/connectors-native-http/select-http-action.png)

   この例では、アクション名を "HTTP action" に変更して、このステップにわかりやすい名前を付けています。

1. ターゲット エンドポイントへの呼び出しに含める [HTTP アクション パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)の値を指定します。

   ![HTTP アクションのパラメーターを入力する](./media/connectors-native-http/http-action-parameters.png)

   **[なし]** 以外の認証の種類を選択した場合、認証設定は、選択に基づいて変化します。 HTTP に使用できる認証の種類の詳細については、以下のトピックを参照してください。

   * [送信呼び出しに認証を追加する](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [マネージド ID を利用してリソースへのアクセスを認証する](../logic-apps/create-managed-service-identity.md)

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="trigger-and-action-outputs"></a>トリガーとアクションの出力

ここでは、以下の情報を返す HTTP トリガーまたはアクションからの出力の詳細情報を示します。

| プロパティ | Type | 説明 |
|----------|------|-------------|
| `headers` | JSON オブジェクト | 要求のヘッダー |
| `body` | JSON オブジェクト | 要求の本文の内容を含むオブジェクト |
| `status code` | Integer | 要求の状態コード |
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

## <a name="content-with-multipartform-data-type"></a>マルチパート/フォームデータ型のコンテンツ

HTTP 要求に `multipart/form-data` 型を含むコンテンツを処理する目的で、このフォーマットを使用することで、`$content-type` 属性と `$multipart` 属性を含む JSON オブジェクトを HTTP 要求の本文に追加できます。

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

たとえば、Excel ファイルの HTTP POST 要求を Web サイトに送信するロジック アプリがあるとします。このとき、`multipart/form-data` 型をサポートする、そのサイトの API を使用します。 このアクションは次のようになります。

![マルチパート フォーム データ](./media/connectors-native-http/http-action-multipart.png)

基礎ワークフロー定義で HTTP アクションの JSON 定義を示す同じ例は次のようになります。

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>Content with application/x-www-form-urlencoded type

HTTP 要求の本文に form-urlencoded データを提供するには、データのコンテンツの種類が `application/x-www-form-urlencoded` であることを指定する必要があります。 HTTP トリガーまたはアクションで、`content-type` ヘッダーを追加します。 ヘッダー値を `application/x-www-form-urlencoded` に設定します。

たとえば、HTTP POST 要求を Web サイトに送信するロジック アプリがあり、`application/x-www-form-urlencoded` 型をサポートするとします。 このアクションは次のようになります。

!['content-type' ヘッダーが 'application/x-www-form-urlencoded' に設定されている HTTP 要求を示すスクリーンショット](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>非同期の要求 - 応答の動作

既定では、Azure Logic Apps での HTTP ベースのすべてのアクションは、標準的な[非同期操作パターン](/azure/architecture/patterns/async-request-reply)に従います。 このパターンでは、HTTP アクションがエンドポイント、サービス、システム、または API に対して要求を呼び出す、または送信した後、受信側が直ちに ["202 ACCEPTED"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) 応答を返すよう規定されます。 このコードは、受信側が要求を受け入れたが、処理が完了していないことを確認します。 応答には、受信側が処理を停止して ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) 成功応答またはその他の 202 以外の応答が返されるまで、呼び出し元が非同期要求の状態をポーリングまたは確認するために使用できる URL およびリフレッシュ ID を指定する `location` ヘッダーを含めることができます。 ただし、呼び出し元は要求の処理が完了するまで待機する必要はなく、次のアクションの実行を継続できます。 詳細については、[マイクロサービスの非同期統合によるマイクロサービスの自律性の強制](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)に関するページを参照してください。

* ロジック アプリ デザイナーでは、HTTP アクション (トリガーではありません) に **非同期パターン** 設定があります。これは既定で有効になっています。 この設定では、呼び出し元は処理が終了するのを待たずに次のアクションに進むことができますが、処理が停止するまで状態のチェックは継続されます。 無効にした場合、この設定では次のアクションに進む前に、呼び出し元が処理の終了を待機するように指定されます。

  この設定を見つけるには、次の手順を実行します。

  1. HTTP アクションのタイトル バーで、省略記号 ( **...** ) ボタンを選択します。これにより、アクションの設定が開きます。

  1. **[非同期パターン]** 設定を探します。

     !["非同期パターン" 設定](./media/connectors-native-http/asynchronous-pattern-setting.png)

* HTTP アクションの基になる JavaScript Object Notation (JSON) 定義では、暗黙的に非同期操作パターンに従います。

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>非同期操作の無効化

場合によっては、特定のシナリオで HTTP アクションの非同期動作を無効化する必要がある場合があります。たとえば、次のような場合です。

* [長時間実行されるタスクで HTTP タイムアウトを回避する](#avoid-http-timeouts)
* [場所ヘッダーの確認を無効にする](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>**非同期パターン** 設定をオフにする

1. ロジック アプリ デザイナーの HTTP アクションのタイトル バーで、省略記号 ( **...** ) ボタンを選択します。これにより、アクションの設定が開きます。

1. **[非同期パターン]** 設定を探し、有効になっている場合は設定を **[オフ]** にし、 **[完了]** を選択します。

   !["非同期パターン" 設定を無効にする](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>アクションの JSON 定義で非同期パターンを無効にする

HTTP アクションの基になる JSON 定義で、アクションが同期操作パターンに従うように、アクションの定義に[ `"DisableAsyncPattern"` 操作オプションを追加します](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)。 詳細については、「[アクションを同期的に実行する](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern)」も参照してください。

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>長時間実行されるタスクで HTTP タイムアウトを回避する

HTTP 要求には[タイムアウト制限](../logic-apps/logic-apps-limits-and-config.md#http-limits)があります。 この制限によってタイムアウトする、実行時間の長い HTTP アクションがある場合、次のオプションがあります。

* アクションが継続的にポーリングしたり、要求の状態を確認したりしないように、[HTTP アクションの非同期操作パターンを無効にします](#disable-asynchronous-operations)。 アクションは代わりに、要求が処理を終了した後、受信側が状態と結果を応答するまで待機します。

* HTTP アクションを [HTTP Webhook アクション](../connectors/connectors-native-webhook.md)に置き換えます。これにより、要求が処理を完了した後、受信側が状態と結果を応答するまで待機します。

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>場所ヘッダーの確認を無効にする

一部のエンドポイント、サービス、システム、または API は、`location` ヘッダーのない "202 ACCEPTED" 応答を返します。 `location` ヘッダーが存在しない場合に、HTTP アクションが要求の状態を継続的に確認しないようにするには、次のオプションを使用します。

* アクションが継続的にポーリングしたり、要求の状態を確認したりしないように、[HTTP アクションの非同期操作パターンを無効にします](#disable-asynchronous-operations)。 アクションは代わりに、要求が処理を終了した後、受信側が状態と結果を応答するまで待機します。

* HTTP アクションを [HTTP Webhook アクション](../connectors/connectors-native-webhook.md)に置き換えます。これにより、要求が処理を完了した後、受信側が状態と結果を応答するまで待機します。

## <a name="known-issues"></a>既知の問題

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>省略された HTTP ヘッダー

HTTP トリガーまたはアクションにこれらのヘッダーが含まれている場合、Logic Apps は警告やエラーを表示することなく、生成された要求メッセージからこれらのヘッダーを削除します。

* `Accept-*` ヘッダー (`Accept-version` を除く)
* `Allow`
* `Content-*` ヘッダー (`Content-Disposition`、`Content-Encoding`、および `Content-Type` を除く)。これらは、POST 操作と PUT 操作を使用するときには受け入れられます。 ただし、GET 操作を使用する場合、これらのヘッダーは Logic Apps によって削除されます。
* `Cookie` ヘッダー。ただし、**Cookie** プロパティを使用して指定した値は、Logic Apps によって受け入れられます。
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Logic Apps では、これらのヘッダーが含まれる HTTP トリガーまたはアクションを使用するロジック アプリの保存は妨げられませんが、これらのヘッダーは無視されます。

## <a name="connector-reference"></a>コネクタのレファレンス

トリガーとアクションのパラメーターの詳細については、以下のセクションを参照してください。

* [HTTP トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP アクション パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>次のステップ

* [アクセスとデータのセキュリティ保護 - 他のサービスやシステムへの送信呼び出しへのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [Logic Apps のコネクタ](../connectors/apis-list.md)
