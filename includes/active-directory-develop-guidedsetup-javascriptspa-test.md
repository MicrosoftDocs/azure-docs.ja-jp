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
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133237"
---
## <a name="test-your-code"></a>コードのテスト

次のいずれかの環境で、自分のコードをテストします。

### <a name="test-with-nodejs"></a>Node.js でテストする

Visual Studio を使用していない場合は、お使いの Web サーバーが開始されていることを確認します。

1. *index.html* ファイルの場所に基づく TCP ポートをリッスンするように、サーバーを構成します。 Node.js では、アプリケーション フォルダーからコマンド ライン プロンプトで次のコマンドを実行することで、Web サーバーを起動してポートをリッスンします。

    ```bash
    npm install
    node server.js
    ```
1. ブラウザーで、「**http://\<span>\</span>localhost:30662**」または「**http://\<span>\</span>localhost:{port}** 」と入力します (ここで、*port* は Web サーバーがリッスンしているポートです)。 *index.html* ファイルの内容と **[サインイン]** ボタンが表示されるはずです。

### <a name="test-with-visual-studio"></a>Visual Studio でのテスト

Visual Studio を使用している場合は、プロジェクト ソリューションを選択し、F5 キーを押して自分のプロジェクトを実行します。 ブラウザーで http://<span></span>localhost:{port} の場所を開くと、 **[サインイン]** ボタンが表示されるはずです。

## <a name="test-your-application"></a>アプリケーションのテスト

ブラウザーに *index.html* ファイルが読み込まれたら、 **[サインイン]** を選択します。 Microsoft ID プラットフォーム エンドポイントにサインインするように求められます。

![JavaScript SPA アカウント サインイン ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションの初回のサインインでは、お使いのプロファイルへのアクセス権を付与してサインインすることを求められます。

![[Permissions requested]\(アクセス許可が要求されています\) ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインインすると、お使いのユーザー プロファイル情報が Microsoft Graph API 応答に返されてページに表示されます。

![Microsoft Graph API 呼び出しの結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 このスコープは既定で、登録ポータルに登録されているすべてのアプリケーションで自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API には、ユーザーの予定表を表示するための *Calendars.Read* スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、*Calendars.Read* の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、*Calendars.Read* スコープを `acquireTokenSilent` 呼び出しに追加します。

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、トークンを取得するための呼び出し内のスコープとして *clientId* を使用できます。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
