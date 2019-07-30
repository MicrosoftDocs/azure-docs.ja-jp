---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133860"
---
## <a name="set-up-your-web-server-or-project"></a>Web サーバーまたはプロジェクトの設定

> 代わりにこのサンプルのプロジェクトをダウンロードすることもできます。 次のいずれかを実行します。
> 
> - Node.js などのローカル Web サーバーでプロジェクトを実行するには、[プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)します。
>
> - (省略可能) IIS サーバーでプロジェクトを実行するには、[Visual Studio プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)します。
>
> その後、コード サンプルを構成してから実行するために、[構成手順](#register-your-application)に進みます。

## <a name="prerequisites"></a>前提条件

* このチュートリアルを実行するには、[Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core)、[Visual Studio 2017](https://www.visualstudio.com/downloads/) と統合された IIS Express などのローカル Web サーバーが必要です。

* Node.js を使用してプロジェクトを実行する場合は、[Visual Studio Code](https://code.visualstudio.com/download) などの 統合開発環境 (IDE) をインストールしてプロジェクト ファイルを編集します。

* このガイドの手順は Node.js と Visual Studio 2017 の両方に基づいていますが、その他の任意の開発環境または Web サーバーを使用できます。

## <a name="create-your-project"></a>プロジェクトを作成する

> ### <a name="option-1-nodejs-or-other-web-servers"></a>オプション 1:Node.js またはその他の Web サーバー
> [Node.js](https://nodejs.org/en/download/) がインストールされていることを確認し、以下を実行します。
> - アプリケーションをホストするフォルダーを作成します。
>
> ### <a name="option-2-visual-studio"></a>オプション 2:Visual Studio
> Visual Studio を使用しているときに、新しいプロジェクトを作成する場合は、以下を行います。
> 1. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。
> 1. **[Visual C#\Web]** で **[ASP.NET Web アプリケーション (.NET Framework)]** を選択します。
> 1. アプリケーションの名前を入力し、 **[OK]** を選択します。
> 1. **[新しい ASP.NET Web アプリケーション]** で **[空]** を選択します。

## <a name="create-the-spa-ui"></a>SPA UI を作成する
1. JavaScript SPA の *index.html* ファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして **[追加]**  >  **[新しい項目]**  >  **[HTML ページ]** を選択し、ページの名前を *index.html* にします。

1. *Index.html* ファイルに、次のコードを追加します。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > 上記のスクリプトにある MSAL.js のバージョンを、[MSAL.js リリース](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)の最新のリリース バージョンに置き換えることができます。
