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
ms.openlocfilehash: f3da6fd75ac485a3e22a428934928795f1feae28
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487935"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services の **Group Calling Hero Sample** は、Communication Services の Calling Web クライアント ライブラリを使用して、グループ通話エクスペリエンスを構築する方法を示します。

このサンプルのクイックスタートでは、サンプルがどのように動作するかを学習した後で、サンプルをローカル コンピューターで実行します。 次に、独自の Azure Communication Services リソースを使用して、サンプルを Azure にデプロイします。

## <a name="download-code"></a>コードをダウンロードする

このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero) にあります。

## <a name="overview"></a>概要

このサンプルには、クライアント側アプリケーションとサーバー側アプリケーションの両方があります。 **クライアント側アプリケーション** は、Microsoft の Fluent UI フレームワークを使用する React/Redux Web アプリケーションです。 このアプリケーションは、クライアント側アプリケーションが Azure に接続するのを助ける ASP.NET Core の **サーバー側アプリケーション** に要求を送信します。

サンプルは次のようになります。

:::image type="content" source="../media/calling/landing-page.png" alt-text="サンプル アプリケーションのランディング ページを示すスクリーンショット。":::

[Start a Chat]\(通話を開始\) ボタンを押すと、サーバー側アプリケーションからユーザー アクセス トークンがフェッチされます。 このトークンは、クライアント アプリを Azure Communication Services に接続するために使用されます。 トークンの取得後、使用するカメラとマイクを指定するように求められます。 切り替えコントロールを使用して、これらのデバイスを無効または有効にできます。

:::image type="content" source="../media/calling/pre-call.png" alt-text="サンプル アプリケーションの通話開始前の画面を示すスクリーンショット。":::

表示名とデバイスを構成すると、通話セッションに参加できるようになります。 その後、メインの通話キャンバスが表示され、通話のコア エクスペリエンスが提供されます。

:::image type="content" source="../media/calling/main-app.png" alt-text="サンプル アプリケーションのメイン画面を示すスクリーンショット。":::

通話のメイン画面のコンポーネントは以下のとおりです。

- **メディア ギャラリー**: 参加者が表示されるメイン領域。 参加者のカメラが有効になっている場合は、そのビデオ フィードがここに表示されます。 各参加者の表示名とビデオ ストリーム (ある場合) がタイル表示されます。
- **ヘッダー**:ここには通話のメイン コントロールがあり、設定と参加者のサイド バーの切り替え、ビデオとマイクのオン/オフ切り替え、画面の共有、通話の終了を行えます。
- **サイド バー**: ヘッダーのコントロールを使用して、参加者と設定情報の表示を切り替えます。 このコンポーネントは、右上隅にある 'X' を使用して閉じることができます。 参加者のサイド バーには参加者の一覧と、チャットするユーザーをさらに招待するためのリンクが表示されます。 [設定] サイドバーでは、マイクとカメラの設定を構成できます。

以下で、サンプルを設定するための前提条件と手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 詳細については、[無料アカウントの作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページをご覧ください。
- [Node.js (12.18.4 以上)](https://nodejs.org/en/download/)
- [Visual Studio (2019 以上)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (使用する Visual Studio インスタンス (32 または 64 ビット) に対応するバージョンを必ずインストールしてください)
- Azure Communication Services リソース。 詳細については、[Azure Communication リソースの作成](../../quickstarts/create-communication-resource.md)に関するページを参照してください。 このクイックスタートで使用するリソースの **接続文字列** を記録する必要があります。

## <a name="locally-deploy-the-service--client-applications"></a>サービスおよびクライアント アプリケーションをローカルにデプロイする

グループ通話のサンプルは、基本的には、ClientApp と Service.NET アプリの 2 つのアプリケーションです。

ローカルにデプロイする場合は、両方のアプリケーションを起動する必要があります。 ブラウザーからサーバー アプリにアクセスすると、ローカルにデプロイされた ClientApp がユーザー エクスペリエンスとして使用されます。

このサンプルをローカルでテストするには、通話の URL を使用して複数のブラウザー セッションを開き、マルチユーザー通話をシミュレートします。

### <a name="before-running-the-sample-for-the-first-time"></a>サンプルを初めて実行する前に行う手順

1. PowerShell、Windows ターミナル、コマンド プロンプト、またはそれと同等のインスタンスを開き、サンプルの複製先のディレクトリに移動します。
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Azure portal から `Connection String` を取得します。 接続文字列の詳細については、[Azure Communication リソースの作成](../../quickstarts/create-communication-resource.md)に関するページをご覧ください。
4. `Connection String` を取得したら、Service .NET フォルダーの下で **Calling/appsetting.json** ファイルに接続文字列を追加します。 変数 `ResourceConnectionString` に接続文字列を入力します。

### <a name="local-run"></a>ローカルの実行

1. Calling フォルダーに移動し、Visual Studio で `Calling.csproj` ソリューションを開きます。
2. `Calling` プロジェクトを実行します。 ブラウザーを `localhost:5001` で開きます。

## <a name="publish-the-sample-to-azure"></a>サンプルを Azure に発行する

1. `Calling` プロジェクトを右クリックし、[発行] を選択します。
2. 新しい発行プロファイルを作成し、Azure サブスクリプションを選択します。
3. 発行する前に、`Edit App Service Settings` を使用して接続文字列を追加し、`ResourceConnectionString` をキーとして入力し、(appsettings.json からコピーした) 接続文字列を値として指定します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../../quickstarts/create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[GitHub からサンプルをダウンロードする](https://github.com/Azure-Samples/communication-services-web-calling-hero)

詳細については、次の記事を参照してください。

- [Calling クライアント ライブラリ](../../quickstarts/voice-video-calling/calling-client-samples.md)について理解する
- [通話のしくみ](../../concepts/voice-video-calling/about-call-types.md)の詳細について確認する

### <a name="additional-reading"></a>その他の情報

- [サンプル](./../overview.md) - その他のサンプルと例については、「サンプルの概要」ページを参照してください。
- [Redux](https://redux.js.org/) - クライアント側の状態管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft が提供する UI ライブラリ
- [React](https://reactjs.org/) - ユーザー インターフェイスを構築するためのライブラリ
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Web アプリケーションを構築するためのフレームワーク
