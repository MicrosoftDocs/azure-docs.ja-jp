---
title: "Azure AD v2 Android の概要 - テスト | Microsoft Docs"
description: "Android アプリがアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする API を、Azure Active Directory v2 エンドポイントから呼び出す方法"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
## <a name="test-your-code"></a>コードのテスト

1. コードをデバイスやエミュレーターにデプロイします。
2. テストする準備ができたら、Microsoft Azure Active Directory アカウント (組織アカウント) または Microsoft アカウント (live.com、outlook.com) を使用してサインインします。 

![サンプル画面](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![サインイン](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>同意
ユーザーがアプリケーションに初めてサインインすると、下記のような同意の画面が表示され、明示的に承認する必要があります。 

![同意](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>予想される結果
ユーザー プロファイルの取得に使用される、Microsoft Graph API ‘me’ エンドポイント呼び出しの結果が表示されます。https://graph.microsoft.com/v1.0/me 一般的な Microsoft Graph エンドポイントの一覧については、この[記事](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)をご覧ください。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API は、ユーザーのプロファイルを読み込むためにスコープ `user.read` を必要とします。 このスコープは既定では、登録ポータルに登録されるすべてのアプリケーションで自動的に追加されます。 バックエンド サーバーのカスタム API に加え、他のいくつかの Microsoft Graph API は、場合によっては、追加のスコープを必要とします。 たとえば、Microsoft Graph の場合、ユーザーの予定表を一覧表示するにはスコープ `Calendars.Read` が必要です。 アプリケーションのコンテキストでユーザーの予定表にアクセスするには、委任されたアクセス許可 `Calendars.Read` をアプリケーション登録の情報に追加し、`acquireTokenSilentAsync` 呼び出しにスコープ `Calendars.Read` を追加する必要があります。 スコープの数を増やすと、ユーザーは追加で同意を求められる可能性があります。

<!--end-collapse-->

