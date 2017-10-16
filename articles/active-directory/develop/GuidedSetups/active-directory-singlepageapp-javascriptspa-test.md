---
title: "Azure AD v2 JS SPA のガイド付きセットアップ - テスト | Microsoft Docs"
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
ms.openlocfilehash: c888760ab311e8ac08b1e625bb837f91047db645
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>コードのテスト

> ### <a name="testing-with-visual-studio"></a>Visual Studio でのテスト
> Visual Studio を使用している場合は、`F5` キーを押してプロジェクトを実行します。ブラウザーが開き、 *http://localhost:{port}* に移動します。移動先に *[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンが表示されます。

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Python または別の Web サーバーでのテスト
> Visual Studio を使用していない場合は、Web サーバーが起動し、*index.html* ファイルが格納されているフォルダーに基づいて TCP ポートをリッスンするように構成されていることを確認してください。 Python の場合は、アプリのフォルダーからコマンド プロンプト/端末で次のコードを実行することで、ポートのリッスンを開始できます。
> 
> ```bash
> python -m http.server 8080
> ```
>  その後、ブラウザーを開き、「*http://localhost:8080*」または「*http://localhost:{port}*」と入力します。*port* は Web サーバーがリッスンしているポートに対応します。 index.html ページのコンテンツは、*[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンを使用して表示する必要があります。

## <a name="test-your-application"></a>アプリケーションのテスト

ブラウザーが *index.html* を読み込んだら、*[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンをクリックします。 今回が初めてのテストの場合は、Microsoft Azure Active Directory v2 エンドポイントにリダイレクトされ、サインインすることを求められます。
 
![サンプル画面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>同意
アプリケーションに本当に初めてサインインすると、次のような同意画面が表示され、同意する必要があります。

 ![同意画面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>予想される結果
Microsoft Graph API の呼び出しと応答によって返されるユーザー プロファイル情報が表示されます。
 
 ![結果](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

取得したトークンに関する基本情報は *[アクセス トークン]* ボックスと *[ID Token Claims]\(ID トークン要求\)* ボックスにも表示されます。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API は、ユーザーのプロファイルを読み込むためにスコープ `user.read` を必要とします。 このスコープは既定では、登録ポータルに登録されるすべてのアプリケーションで自動的に追加されます。 バックエンド サーバーのカスタム API に加え、他のいくつかの Microsoft Graph API は、場合によっては、追加のスコープを必要とします。 たとえば、Microsoft Graph の場合、ユーザーの予定表を一覧表示するにはスコープ `Calendars.Read` が必要です。 アプリケーションのコンテキストでユーザーの予定表にアクセスするには、委任されたアクセス許可 `Calendars.Read` をアプリケーション登録の情報に追加し、`acquireTokenSilent` 呼び出しにスコープ `Calendars.Read` を追加する必要があります。 スコープの数を増やすと、ユーザーは追加で同意を求められる可能性があります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、`acquireTokenSilent` や `acquireTokenRedirect` の呼び出し内のスコープとして `clientId` を使用できます。

<!--end-collapse-->
