---
title: Mobile Apps を使用した Apache Cordova への認証の追加 | Microsoft Docs
description: Azure App Service で Mobile Apps を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Apache Cordova アプリのユーザーを認証する方法について説明します。
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b0634038dbf5771ac1aa0bc00d007e758171b238
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443500"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Apache Cordova アプリへの認証の追加
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-users) を今すぐチェックしてください。
>

## <a name="summary"></a>まとめ
このチュートリアルでは、サポートされている ID プロバイダーを使用して、Apache Cordova で todolist クイック スタート プロジェクトに認証を追加します。 最初に、このチュートリアルの基になっている [Mobile Apps の使用] チュートリアルを完了しておく必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、App Service を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

これで、バックエンドへの匿名アクセスが無効になっていることを確認できます。 Visual Studio で次の操作を行います。

* チュートリアル「[Mobile Apps の使用]」を実行したときに作成したプロジェクトを開きます。
* **Google Android エミュレーター**でアプリケーションを実行します。
* アプリの開始後に、「予期しない接続エラー」が表示されていることを確認します。

次に、Mobile App バックエンドのリソースを要求する前にユーザーを認証するようにアプリを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する
1. **Visual Studio** でプロジェクトを開き、編集する `www/index.html` ファイルを開きます。
2. head セクションで `Content-Security-Policy` メタ タグを見つけます。  許可されているソースの一覧に OAuth ホストを追加します。

   | プロバイダー | SDK プロバイダー名 | OAuth ホスト |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    サンプルの Content-Security-Policy (Azure Active Directory に実装されている) は次のようになっています。

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    `https://login.microsoftonline.com` を前の表の OAuth ホストに置き換えます。  content-security-policy メタ タグの詳細については、[Content-Security-Policy に関するドキュメント] をご覧ください。

    一部の認証プロバイダーについては、適切なモバイル デバイスで使用する場合、Content-Security-Policy を変更する必要がありません。  たとえば、Android デバイスで Google 認証を使用する場合は、Content-Security-Policy を変更する必要はありません。

3. 編集する `www/js/index.js` ファイルを開き、`onDeviceReady()` メソッドを見つけて、クライアント作成コードの下に次のコードを追加します。

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    テーブル参照を作成し、UI を更新する既存のコードをこのコードで置き換えます。

    login() メソッドは、プロバイダーでの認証を開始します。 login() メソッドは、JavaScript Promise を返す非同期関数です。  初期化の残りの部分は promise の応答内に配置されているため、login() メソッドが完了するまで実行されません。

4. 先ほど追加したコードで、 `SDK_Provider_Name` を実際のログイン プロバイダーの名前に置き換えます。 たとえば、Azure Active Directory の場合、 `client.login('aad')`を使用します。
5. プロジェクトを実行します。  プロジェクトの初期化が終了すると、アプリケーションにより、選択した認証プロバイダーの OAuth ログイン ページが表示されます。

## <a name="next-steps"></a>次のステップ
* Azure App Service を使用した [認証の詳細] を確認します。
* このチュートリアルの続きとして、Apache Cordova アプリに [プッシュ通知] を追加します。

SDK の使用方法を確認してください。

* [Apache Cordova SDK]
* [ASP.NET サーバー SDK]
* [Node.js サーバー SDK]

<!-- URLs. -->
[Mobile Apps の使用]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy に関するドキュメント]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[プッシュ通知]: app-service-mobile-cordova-get-started-push.md
[認証の詳細]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET サーバー SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js サーバー SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
