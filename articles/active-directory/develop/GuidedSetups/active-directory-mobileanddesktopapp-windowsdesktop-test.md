---
title: "Azure AD v2 Windows デスクトップの概要 - テスト |Microsoft Docs"
description: "Windows デスクトップ .NET (XAML) アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>コードのテスト

アプリケーションをテストするには、Visual Studio で `F5` キーを押してプロジェクトを実行します。 メイン ウィンドウが表示されます。

![サンプル画面](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

テストする準備ができたら、*[Call Microsoft Graph API]* (Microsoft Graph API の呼び出し) をクリックし、Microsoft Azure Active Directory アカウント (組織アカウント) または Microsoft アカウント (live.com、outlook.com) を使用してサインインします。 初めての場合は、ユーザーにサインインを求めるウィンドウが表示されます。

![サインイン](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>同意
アプリケーションに初めてサインインすると、下記のような同意の画面が表示され、明示的に承認する必要があります。

![同意画面](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>予想される結果
API 呼び出し結果の画面に、Microsoft Graph API の呼び出しによって返されるユーザー プロファイル情報が表示されます。

`AcquireTokenAsync` または `AcquireTokenSilentAsync` によって取得したトークンに関する以下の基本情報も、トークン情報ボックスに表示されます。

|プロパティ  |形式  |Description |
|---------|---------|---------|
|名前 | {ユーザーのフルネーム} |ユーザーの姓と名|
|ユーザー名 |<span>user@domain.com</span> |ユーザーの識別に使用されるユーザー名|
|トークン有効期限 |{DateTime}         |トークンの有効期限が切れる時刻。 必要に応じて MSAL によりトークンは更新され、有効期限が延長されます。|
|アクセス トークン |{文字列}         |承認ヘッダーを必要とする HTTP 要求に送信されるトークンの文字列|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細
Graph API は、ユーザー プロファイルの読み取りに `user.read` スコープが必要です。 このスコープは既定では、登録ポータルに登録されるすべてのアプリケーションで自動的に追加されます。 バックエンド サーバーのカスタム API に加え、他のいくつかの Graph API は、追加のスコープを必要とします。 たとえば Graph では、ユーザーの予定表を一覧表示するのに `Calendars.Read` が必要です。 アプリケーションのコンテキストでユーザーの予定表にアクセスするには、`Calendars.Read` が委任されたアプリケーション登録の情報を追加してから、`AcquireTokenAsync` 呼び出しに `Calendars.Read` を追加する必要があります。 スコープの数を増やすと、ユーザーは追加で同意を求められる可能性があります。

<!--end-collapse-->



