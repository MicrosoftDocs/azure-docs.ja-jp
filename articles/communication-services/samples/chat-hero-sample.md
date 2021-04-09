---
title: グループ チャットのヒーロー サンプル
titleSuffix: An Azure Communication Services sample overview
description: Azure Communication Services を使用したチャットのヒーロー サンプルの概要。これにより、開発者はサンプルの内部動作とその変更方法をより詳しく学習することができます。
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0eb396935b42b040ab9f152a4965922664deb451
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487831"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>グループ チャットのヒーロー サンプルを使ってみる

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

> [!IMPORTANT]
> [このサンプルは GitHub で入手できます。](https://github.com/Azure-Samples/communication-services-web-chat-hero)


Azure Communication Services の **グループ チャットのヒーロー サンプル** は、Communication Services のチャット Web クライアント ライブラリーを使用して、グループ通話エクスペリエンスを構築する方法を示します。

このサンプルのクイックスタートでは、サンプルをローカル コンピューターで実行する前に、サンプルがどのように動作するかを学習します。 次に、独自の Azure Communication Services リソースを使用して、サンプルを Azure にデプロイします。


## <a name="overview"></a>概要

このサンプルには、クライアント側アプリケーションとサーバー側アプリケーションの両方があります。 **クライアント側アプリケーション** は、Microsoft の Fluent UI フレームワークを使用する React/Redux Web アプリケーションです。 このアプリケーションは、クライアント側アプリケーションが Azure に接続するのを助ける ASP.NET Core の **サーバー側アプリケーション** に要求を送信します。

サンプルは次のようになります。

:::image type="content" source="./media/chat/landing-page.png" alt-text="サンプル アプリケーションのランディング ページを示すスクリーンショット。":::

[Start a Chat]\(チャットを開始\) ボタンを押すと、Web アプリケーションは、サーバー側アプリケーションからユーザー アクセス トークンをフェッチします。 このトークンは、クライアント アプリを Azure Communication Services に接続するために使用されます。 トークンが取得されると、チャットで自分を表す名前と絵文字を指定するように求められます。

:::image type="content" source="./media/chat/pre-chat.png" alt-text="アプリケーションのチャット前の画面を示すスクリーンショット。":::

表示名と絵文字を構成すると、チャット セッションに参加できるようになります。 これで、コア チャット エクスペリエンスを提供するメインのチャット キャンバスが表示されます。

:::image type="content" source="./media/chat/main-app.png" alt-text="サンプル アプリケーションのメイン画面を示すスクリーンショット。":::

メイン チャット画面のコンポーネント:

- **メイン チャット領域**: これは、ユーザーがメッセージを送受信できるコア チャット エクスペリエンスです。 メッセージを送信するには、入力領域を使用して Enter キーを押します (または、[送信] ボタンを使用します)。 受信されたチャット メッセージは、適切な名前と絵文字を使って送信者によって分類されます。 チャット領域には、次の 2 種類の通知が表示されます。1) ユーザーが入力中の入力通知と、2) メッセージの送信済みと既読の通知。
- **ヘッダー**:ここには、チャット スレッドのタイトルと、参加者と設定のサイド バーを切り替えるためのコントロール、およびチャット セッションを終了するための退出ボタンがユーザーに表示されます。
- **サイド バー**: ヘッダーでコントロールを使用して切り替えると、ここに参加者と設定情報が表示されます。 参加者のサイド バーには、チャットの参加者の一覧と、チャット セッションに参加者を招待するためのリンクが含まれています。 設定のサイド バーでは、チャット スレッドのタイトルを構成できます。

以下で、サンプルを設定するための前提条件と手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウントを作成します。 詳細については、[無料アカウントの作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Node.js (8.11.2 以上)](https://nodejs.org/en/download/)
- [Visual Studio (2017 以上)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (使用する Visual Studio インスタンス (32 または 64 ビット) に対応するバージョンを必ずインストールしてください)
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../quickstarts/create-communication-resource.md)に関するページを参照してください。 このクイックスタートで使用するリソースの **接続文字列** を記録する必要があります。

## <a name="locally-deploying-the-service--client-app"></a>サービスおよびクライアント アプリをローカルにデプロイする

シングル スレッドのチャット サンプルは、基本的に 2 つの "アプリケーション" (クライアントとサーバー アプリケーション) です。

chat.csproj で Visual Studio を開き、デバッグ モードで実行します。これにより、チャットのフロントエンド サービスが開始されます。 ブラウザーからサーバー アプリにアクセスすると、ローカルにデプロイされているチャットのフロントエンド サービスにトラフィックがリダイレクトします。

このサンプルをローカルでテストするには、チャットの URL を使用して複数のブラウザー セッションを開き、マルチユーザー チャットをシミュレートします。

## <a name="before-running-the-sample-for-the-first-time"></a>サンプルを初めて実行する前

1. PowerShell、Windows ターミナル、コマンド プロンプト、またはそれと同等のインスタンスを開き、サンプルの複製先のディレクトリに移動します。
2. `git clone https://github.com/Azure-Samples/communication-services-web-chat-hero.git`
3. Azure portal から `Connection String` を取得します。 接続文字列の詳細については、[Azure Communication リソースの作成](../quickstarts/create-communication-resource.md)に関するページを参照してください。
4. `Connection String` を取得したら、Chat フォルダーにある **Chat/appsettings.json** ファイルに接続文字列を追加します。 変数 `ResourceConnectionString` に接続文字列を入力します。

### <a name="local-run"></a>ローカルの実行

1. Chat フォルダーに移動し、Visual Studio で `Chat.csproj` ソリューションを開きます。
2. プロジェクトを実行します。 ブラウザーを localhost:5000 で開きます。

#### <a name="troubleshooting"></a>トラブルシューティング

- ソリューションがビルドされない。NPM のインストール/ビルド中にエラーがスローされる

   C# ソリューションをクリーン/リビルドする

## <a name="publish-the-sample-to-azure"></a>サンプルを Azure に発行する

1. `Chat` プロジェクトを右クリックし、[発行] を選択します。
2. 新しい発行プロファイルを作成し、Azure サブスクリプションを選択します。
3. 発行する前に、`Edit App Service Settings` を使用して接続文字列を追加し、`ResourceConnectionString` をキーとして入力し、(appsettings.json からコピーした) 接続文字列を値として指定します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../quickstarts/create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[GitHub からサンプルをダウンロードする](https://github.com/Azure-Samples/communication-services-web-chat-hero)

詳細については、次の記事を参照してください。

- [チャットの概念](../concepts/chat/concepts.md)について学習する
- [チャット クライアント ライブラリ](../concepts/chat/sdk-features.md)について理解する
- [Contoso Med App](https://github.com/Azure-Samples/communication-services-contoso-med-app) サンプルを確認する

## <a name="additional-reading"></a>その他の情報

- [サンプル](./overview.md) - その他のサンプルと例については、「サンプルの概要」ページを参照してください。
- [Redux](https://redux.js.org/) - クライアント側の状態管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft が提供する UI ライブラリ
- [React](https://reactjs.org/) - ユーザー インターフェイスを構築するためのライブラリ
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Web アプリケーションを構築するためのフレームワーク
