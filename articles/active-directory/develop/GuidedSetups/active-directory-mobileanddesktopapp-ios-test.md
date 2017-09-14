---
title: "Azure AD v2 iOS の概要 - テスト | Microsoft Docs"
description: "iOS (Swift) アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
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
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 2420723eed9ae8ba1ba6156482ef4b4de9ce2fc0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/15/2017

---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>iOS アプリケーションから Microsoft Graph API にクエリ実行をテストする

`Command` + `R` を押し、シミュレーターでコードを実行します。

![サンプル画面](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

テストの準備ができたら、*‘Microsoft Graph API の呼び出し’* をタップします。ユーザー名とパスワードを入力するように求められます。

### <a name="consent"></a>同意
アプリケーションに初めてサインインすると、下記のような同意の画面が表示され、明示的に承認する必要があります。

![同意画面](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>予想される結果
*[ログ]* セクションに、Microsoft Graph API の呼び出しによって返されるユーザー プロファイル情報が表示されます。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API は、ユーザーのプロファイルを読み込むためにスコープ `user.read` を必要とします。 このスコープは既定では、登録ポータルに登録されるすべてのアプリケーションで自動的に追加されます。 バックエンド サーバーのカスタム API に加え、他のいくつかの Microsoft Graph API は、場合によっては、追加のスコープを必要とします。 たとえば、Microsoft Graph の場合、ユーザーの予定表を一覧表示するにはスコープ `Calendars.Read` が必要です。 アプリケーションのコンテキストでユーザーの予定表にアクセスするには、委任されたアクセス許可 `Calendars.Read` をアプリケーション登録の情報に追加し、`acquireTokenSilent` 呼び出しにスコープ `Calendars.Read` を追加する必要があります。 スコープの数を増やすと、ユーザーは追加で同意を求められる可能性があります。

<!--end-collapse-->




