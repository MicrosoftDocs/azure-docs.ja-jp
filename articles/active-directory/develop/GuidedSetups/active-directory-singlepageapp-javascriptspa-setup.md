---
title: "Azure AD v2 JS SPA のガイド付きセットアップ - セットアップ | Microsoft Docs"
description: "JavaScript SPA アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: fc9f88cc8d23abcfa8ea30e346192732b422ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
## <a name="setting-up-your-web-server-or-project"></a>Web サーバーまたはプロジェクトの設定

> 代わりにこのサンプルのプロジェクトをダウンロードすることもできます。 
> - [Visual Studio プロジェクトのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> or
> - Python などのローカル Web サーバー向けの[プロジェクト ファイルのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip)
>
> 次に[構成手順](#create-an-application-express)に進み、実行前にコード サンプルを構成します。

## <a name="prerequisites"></a>前提条件
このガイド付きセットアップを実行するには、[Python http.server](https://www.python.org/downloads/)、[http-server](https://www.npmjs.com/package/http-server/)、[.NET Core](https://www.microsoft.com/net/core) または [Visual Studio 2017](https://www.visualstudio.com/downloads/) と統合された IIS Express などのローカル Web サーバーが必要です。 

このガイドの手順は Python と Visual Studio 2017 の両方に基づいていますが、他の開発環境または Web サーバーでも自由に使用できます。

## <a name="create-your-project"></a>プロジェクトを作成する 

> ### <a name="option-1-visual-studio"></a>方法 1: Visual Studio 
> Visual Studio を使用して新しいプロジェクトを作成している場合は、以下の手順に従って新しい Visual Studio ソリューションを作成します。
> 1.    Visual Studio で、`File` > `New` > `Project` を選択します。
> 2.    `Visual C#\Web` で、`ASP.NET Web Application (.NET Framework)` を選択します。
> 3.    アプリケーションに名前を付けて、*[OK]* をクリックします。
> 4.    `New ASP.NET Web Application` で、`Empty` を選択します。

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>方法 2: Python/ その他の Web サーバー
> [Python](https://www.python.org/downloads/) がインストールされていることを確認し、以下の手順に従います。
> - アプリケーションをホストするフォルダーを作成します。


## <a name="create-your-single-page-applications-ui"></a>Single Page Application の UI を作成する
1.  JavaScript SPA の *index.html* ファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして `Add` > `New Item` > `HTML page` を選択し、これに index.html の名前を付けます
2.  次のコードをページに追加します。
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
