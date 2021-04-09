---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1318c47bcded47159006977db09604bb53674973
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487936"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services の **Group Calling Hero Sample for iOS** は、Communication Services の Calling iOS クライアント ライブラリを使用して、音声とビデオを含むグループ通話エクスペリエンスを構築する方法を示します。 このサンプル クイックスタートでは、サンプルを設定して実行する方法について説明します。 背景情報については、サンプルの概要を参照してください。

## <a name="overview"></a>概要

このサンプルはネイティブ iOS アプリケーションであり、音声通話とビデオ通話の両機能を備えた通話エクスペリエンスを実現するために、Azure Communication Services iOS クライアント ライブラリが使用されています。 このアプリケーションは、サーバー側コンポーネントを使用してアクセス トークンをプロビジョニングします。そのアクセス トークンを使用して、Azure Communication Services クライアント ライブラリが初期化されます。 サーバー側コンポーネントを構成する際は、[Azure Functions を使用した信頼できるサービス](../../tutorials/trusted-service-tutorial.md)についてのチュートリアルに従ってください。

サンプルは次のようになります。

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="サンプル アプリケーションのランディング ページを示すスクリーンショット。":::

[Start new call]\(新しい通話を開始\) ボタンを押すと、iOS アプリケーションによって新しい通話が作成され、それに参加します。 このアプリケーションでは、既存の通話の ID を指定することで、Azure Communication Services の既存の通話に参加することもできます。

通話に参加すると、カメラとマイクへのアクセスをアプリケーションに許可するよう求められます。 さらに、表示名を入力するように求められます。

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="サンプル アプリケーションの通話開始前の画面を示すスクリーンショット。":::

表示名とデバイスを構成すると、通話に参加できるようになります。 メインの通話キャンバスが表示され、通話のコア エクスペリエンスが提供されます。

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="サンプル アプリケーションのメイン画面を示すスクリーンショット。":::

通話のメイン画面のコンポーネントは以下のとおりです。

- **メディア ギャラリー**: 参加者が表示されるメイン領域。 参加者のカメラが有効になっている場合は、そのビデオ フィードがここに表示されます。 参加者ごとに個別のタイルがあり、その表示名とビデオ ストリーム (存在する場合) が表示されます。 ギャラリーでは複数の参加者がサポートされており、通話への参加者が追加または削除されると更新されます。
- **アクション バー**: 主要な通話コントロールが配置される場所です。 これらのコントロールを使用して、ビデオとマイクのオンとオフを切り替えたり、画面を共有したり、通話を終了したりすることができます。

以下で、サンプルを設定するための前提条件と手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。
- Azure Communication Services リソース。 詳細については、[Azure Communication リソースの作成](../../quickstarts/create-communication-resource.md)に関するページを参照してください。
- [信頼済みサービスのロジック](../../tutorials/trusted-service-tutorial.md)を実行してアクセス トークンをフェッチする Azure 関数。

## <a name="running-sample-locally"></a>サンプルをローカルで実行する

グループ通話サンプルは、XCode を使用してローカルで実行することができます。 開発者は、各自の物理デバイスまたはエミュレーターを使用してアプリケーションをテストできます。

### <a name="before-running-the-sample-for-the-first-time"></a>サンプルを初めて実行する前

1. `pod install` を実行して依存関係をインストールします。
2. XCode で `ACSCall.xcworkspace` を開きます。
3. `AppSettings.plist` を更新します。 `acsTokenFetchUrl` キーの値を、認証エンドポイントの URL に設定します。

### <a name="run-sample"></a>サンプルを実行する

XCode でサンプルをビルドして実行します。

## <a name="optional-securing-an-authentication-endpoint"></a>(省略可) 認証エンドポイントのセキュリティを確保する

このサンプルはデモンストレーション上、既定では、パブリックにアクセスできるエンドポイントを使用して、Azure Communication Services トークンをフェッチします。 運用のシナリオでは、セキュリティを確保した独自のエンドポイントを使用して独自のトークンをプロビジョニングすることをお勧めします。

別途構成を行うことにより、このサンプルは、**Azure Active Directory** (Azure AD) で保護されたエンドポイントへの接続をサポートし、Azure Communication Services のトークンをフェッチするアプリにユーザー ログインを要求することができます。 以下の手順を参照してください。

1. アプリの Azure Active Directory 認証を有効にします。  
   - [アプリを Azure Active Directory に登録する (iOS または macOS プラットフォームの設定を使用)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Azure AD ログインを使用するように App Service または Azure Functions アプリを構成する](../../../app-service/configure-authentication-provider-aad.md)
2. Azure Active Directory の [アプリの登録] から、登録済みアプリの概要ページに移動します。 `Application (client) ID`、`Directory (tenant) ID`、`Application ID URI` を書き留めておきます。

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure portal における Azure Active Directory の構成。":::

3. Xcode で `AppSettings.plist` を開き、次のキー値を追加します。
   - `acsTokenFetchUrl`:Azure Communication Services トークンを要求するための URL 
   - `isAADAuthEnabled`:Azure Communication Services トークン認証を要求するかどうかを示すブール値
   - `aadClientId`:アプリケーション (クライアント) ID
   - `aadTenantId`:ディレクトリ (テナント) ID
   - `aadRedirectURI`:リダイレクト URI は、`msauth.<app_bundle_id>://auth` の形式にする必要があります。
   - `aadScopes`:認可を求めるユーザーから要求されるアクセス許可スコープの配列。 認証エンドポイントへのアクセスを許可するには、この配列に `<Application ID URI>/user_impersonation` を追加します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../../quickstarts/create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [Calling クライアント ライブラリ](../../quickstarts/voice-video-calling/calling-client-samples.md)について理解する
- [通話のしくみ](../../concepts/voice-video-calling/about-call-types.md)の詳細について確認する

### <a name="additional-reading"></a>その他の情報

- [サンプル](./../overview.md) - その他のサンプルと例については、「サンプルの概要」ページを参照してください。