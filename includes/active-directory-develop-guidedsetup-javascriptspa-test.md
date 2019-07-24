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
ms.openlocfilehash: c13c9ece54d512adf271532a4ac17876a88d733e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804467"
---
## <a name="test-your-code"></a>コードのテスト

### <a name="test-with-node"></a>ノードでのテスト

Visual Studio を使用していない場合は、お使いの Web サーバーが開始されていることを確認します。

1. **index.html** ファイルの場所に基づく TCP ポートをリッスンするように、サーバーを構成します。 ノードの場合、アプリケーション フォルダーからコマンド ライン プロンプトで次のコマンドを実行することで、Web サーバーを起動してポートをリッスンします。

    ```bash
    npm install
    node server.js
    ```
1. ブラウザーを開き、「 http://<span></span>localhost:30662」または「 http://<span></span>localhost:{ポート}」と入力します (ここで、**"ポート"** は Web サーバーがリッスンしているポートです)。 index.html ファイルの内容と **[サインイン]** ボタンが表示されるはずです。

<p><!-- -->

### <a name="test-with-visual-studio"></a>Visual Studio でのテスト

Visual Studio を使用している場合は、プロジェクト ソリューションを選択したことを確認し、**F5** キーを押してプロジェクトを実行します。 ブラウザーで http://<span></span>localhost:{ポート} の場所を開くと、**[サインイン]** ボタンが表示されます。

## <a name="test-your-application"></a>アプリケーションのテスト

ブラウザーに index.html ファイルが読み込まれたら、**[サインイン]** をクリックします。 Microsoft ID プラットフォーム エンドポイントでサインインするように求められます。

![JavaScript SPA アカウントにサインインする](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションに初めてサインインするとき、アプリケーションがプロファイルにアクセスし、サインインできることへの同意を求められます。

![アプリケーションによるアクセスに同意する](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインイン後は、ページに Microsoft Graph API の応答が表示され、そこに、返されたユーザー プロファイル情報が表示されるはずです。

![Microsoft Graph API 呼び出しから期待される結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API は、ユーザーのプロファイルを読み込むためにスコープ `user.read` を必要とします。 このスコープは既定で、登録ポータルに登録されているすべてのアプリケーションで自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API には、ユーザーの予定表を表示するための `Calendars.Read` スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、`Calendars.Read` の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、`acquireTokenSilent` の呼び出しに `Calendars.Read` スコープを追加します。

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、トークンを取得するための呼び出し内のスコープとして `clientId` を使用できます。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]