---
title: Mobile Apps を使用した Android での認証の追加 | Microsoft Docs
description: Azure App Service の Mobile Apps 機能を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Android アプリのユーザーを認証する方法について説明します。
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f138911510db4e6839ff96317fa6004e449e58be
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443595"
---
# <a name="add-authentication-to-your-android-app"></a>Android アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーとかかわることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-users) を今すぐチェックしてください。
>

## <a name="summary"></a>まとめ
このチュートリアルでは、サポートされている ID プロバイダーを使用して、Android で todolist クイック スタート プロジェクトに認証を追加します。 最初に、このチュートリアルの基になっている [Mobile Apps の使用] チュートリアルを完了しておく必要があります。

## <a name="register"></a>アプリを認証に登録し、Azure App Services を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>許可されている外部リダイレクト URL にアプリを追加する

認証をセキュリティで保護するには、アプリ用の新しい URL スキームの定義が必要になります。 これによって、認証プロセスが完了すると認証システムからアプリにリダイレクトできます。 このチュートリアル全体を通して、URL スキーム _appname_ を使用します。 ただし、選択したあらゆる URL スキームを使用できます。 URL スキームは、モバイル アプリに対して一意である必要があります。 サーバー側でリダイレクトを有効にするには、以下の手順に従います。

1. [Azure Portal] で、App Service を選択します。

2. **[認証/承認]** メニュー オプションをクリックします。

3. **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** に `appname://easyauth.callback` を入力します。  この文字列の _appname_ は、モバイル アプリケーションの URL スキームです。  プロトコルの通常の URL 仕様 (文字と数字のみを使用し、文字で始まる) に従う必要があります。  数か所で URL スキームに合わせてモバイル アプリケーション コードを調整する必要があるため、選択した文字列をメモしておく必要があります。

4. Click **OK**.

5. **[Save]** をクリックします。

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Android Studio で、[Mobile Apps の使用]に関するチュートリアルで完成させたプロジェクトを開きます。 **[Run (実行)]** メニューの **[Run app (アプリの実行)]** をクリックし、アプリの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

     この例外は、認証されないユーザーとしてアプリがバックエンドにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、Mobile Apps バックエンドのリソースを要求する前にユーザーを認証するようにアプリを更新します。

## <a name="add-authentication-to-the-app"></a>アプリケーションに認証を追加する
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>クライアントに認証トークンをキャッシュする
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>次の手順
これで基本的な認証チュートリアルは完了しましたので、引き続き次のいずれかのチュートリアルのご利用を検討してください。

* [プッシュ通知を Android アプリに追加する](app-service-mobile-android-get-started-push.md)。
  Azure Notification Hubs を使用してプッシュ通知を送信するように Mobile Apps バックエンドを構成する方法について説明します。
* [Android アプリのオフライン同期を有効にする](app-service-mobile-android-get-started-offline-data.md)。
  Mobile Apps バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Mobile Apps の使用]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
