---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095477"
---
#### <a name="no-response-from-the-backend-service"></a>バックエンド サービスからの応答がない

ローカルでテストしている場合は、バックエンド サービスが実行されており、正しいポートを使用していることを確認します。

**Azure API アプリ**に対してテストしている場合は、サービスが実行中であること、展開済みであること、エラーなしで開始されていることを確認します。

クライアントを使用してテストしている場合は、 **[Postman](https://www.postman.com/downloads)** またはモバイル アプリ構成で、ベース アドレスが正しく指定されていることを必ず確認してください。 ローカルでテストするときには、ベース アドレスは `https://<api_name>.azurewebsites.net/` または `https://localhost:5001/` である必要があります。

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>デバッグ セッションの開始または停止後、Android で通知を受信できない

デバッグ セッションを開始または停止した後で再登録してください。 デバッガーにより、新しい **Firebase** トークンが生成されます。 通知ハブのインストールも更新する必要があります。

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>バックエンド サービスから 401 状態コードを受信する

**apikey** 要求ヘッダーが設定されていること、およびこの値がバックエンド サービス用に構成したものと一致することを確認します。

ローカルでのテスト時にこのエラーが発生した場合は、クライアント構成で定義したキー値が、[API](#create-the-api-app) で使用されている **Authentication:ApiKey** ユーザー設定値と一致していることを確認してください。

**API アプリ**を使用してテストしている場合は、クライアント構成ファイルのキー値が、[API アプリ](#create-the-api-app)で使用している **Authentication:ApiKey** アプリケーション設定と一致していることを確認してください。

> [!NOTE]
> バックエンド サービスを展開した後にこの設定を作成または変更した場合は、サービスを有効にするために再起動する必要があります。

「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了していない場合は、**Authorize** 属性を **NotificationsController** クラスに適用していないことを確認してください。

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>バックエンド サービスから 404 状態コードを受信する

エンドポイントと HTTP 要求メソッドが正しいことを確認します。 たとえば、エンドポイントは次のように指定する必要があります。

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

また、ローカルでテストする場合は次のようになります。

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

クライアント アプリでベース アドレスを指定する場合は、`/` で終了するようにしてください。 ローカルでテストするときには、ベース アドレスは `https://<api_name>.azurewebsites.net/` または `https://localhost:5001/` である必要があります。

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>登録できず、通知ハブのエラー メッセージが表示される

テスト デバイスがネットワーク接続を備えていることを確認します。 次に、**HttpResponse** で **StatusCode** プロパティ値を検査するようにブレークポイントを設定して、Http 応答の状態コードを確認します。

状態コードに基づいて、該当する場合は前のトラブルシューティングの推奨事項を確認します。

各 API の特定の状態コードを返す行にブレークポイントを設定します。 次に、ローカルでデバッグするときにバックエンド サービスを呼び出します。

適切なペイロードを使用して、 **[Postman](https://www.postman.com/downloads)** でバックエンド サービスが予期したとおりに動作していることを確認します。 対象のプラットフォームのクライアント コードによって作成された実際のペイロードを使用します。

プラットフォーム固有の構成セクションを確認して、実行されなかったステップがないことを確認します。 適切な値が、適切なプラットフォームの `installation id` および `token` 変数に対して解決されていることを確認します。

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>デバイスの ID を解決できず、エラー メッセージが表示される

プラットフォーム固有の構成セクションを確認して、実行されなかったステップがないことを確認します。
