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
ms.openlocfilehash: da2477b19327273fe922ac81f909233cb4ef8f06
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502936"
---
## <a name="setting-up-your-web-server-or-project"></a>Web サーバーまたはプロジェクトの設定

> 代わりにこのサンプルのプロジェクトをダウンロードすることもできます。
> - Node などのローカル Web サーバー向けの[プロジェクト ファイルのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
>
> or
> - [Visual Studio プロジェクトのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> 次に[構成手順](#register-your-application)に進み、実行前にコード サンプルを構成します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを実行するには、[Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core)、または [Visual Studio 2017](https://www.visualstudio.com/downloads/) と統合された IIS Express などのローカル Web サーバーが必要です。

Node.js を使用してプロジェクトを実行する場合は、[Visual Studio Code](https://code.visualstudio.com/download) などの IDE をインストールしてプロジェクト ファイルを編集します。

このガイドの手順は Node.js と Visual Studio 2017 の両方に基づいていますが、他の開発環境または Web サーバーでも自由に使用できます。

## <a name="create-your-project"></a>プロジェクトを作成する

> ### <a name="option-1-node-other-web-servers"></a>オプション 1:Node/その他の Web サーバー
> [Node.js](https://nodejs.org/en/download/) がインストールされていることを確認し、以下の手順に従います。
> - アプリケーションをホストするフォルダーを作成します。

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>オプション 2:Visual Studio
> Visual Studio を使用して新しいプロジェクトを作成している場合は、以下の手順に従って新しい Visual Studio ソリューションを作成します。
> 1.    Visual Studio で次の操作を行います。**[ファイル] > [新規作成] > [プロジェクト]**
> 2.    **[Visual C#\Web]** で **[ASP.NET Web アプリケーション (.NET Framework)]** を選択します
> 3.    アプリケーションの名前を入力し、**[OK]** を選択します
> 4.    **[新しい ASP.NET Web アプリケーション]** で **[空]** を選択します

## <a name="create-your-single-page-applications-ui"></a>Single Page Application の UI を作成する
1. JavaScript SPA の `index.html` ファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして **[追加] > [新しい項目] > [HTML ページ]** を選択し、ページの名前を index.html にします。

2. 次のコードをページに追加します。
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.4/js/msal.js"></script>
       <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
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
