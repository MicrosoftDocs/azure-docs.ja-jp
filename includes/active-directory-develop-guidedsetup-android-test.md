---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: c8e5e4f826d7835a1fd38d1db5bfeab19b679b30
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203722"
---
## <a name="test-your-app"></a>アプリをテストする

1. デバイス/エミュレーターに対してコードを実行します。
2. Azure Active Directory アカウント (職場または学校アカウント) または Microsoft アカウント (live.com、outlook.com) を使用してサインインを試みます。 

    ![アプリケーションをテストする](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![ユーザー名とパスワードを入力する](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>アプリに同意する

ユーザーが初めてアプリケーションにログインすると、次に示すように、アプリが必要とするアクセス許可に同意するよう求められます。 

![アプリケーションによるアクセスに同意する](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>成功です。

サインインして同意した後、アプリに Microsoft Graph API からの応答が表示されます。 この特定の呼び出しは、**/me** エンドポイントを対象とし、[ユーザー プロファイル](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get)を返します。 他の Microsoft Graph エンドポイントの一覧については、[Microsoft Graph API 開発者向けドキュメント](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)をご覧ください。

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *User.Read* スコープが必要です。 このスコープは、アプリケーション登録ポータルで登録されたすべてのアプリケーションで自動的に構成されます。 他の API では、追加のスコープが必要になります。 たとえば、Microsoft Graph API には、ユーザーの予定表を表示するための *Calendars.Read* スコープが必要です。

ユーザーの予定表にアクセスするには、*Calendars.Read* の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、*Calendars.Read* スコープを `acquireTokenSilent` 呼び出しに追加します。 

> [!NOTE]
> アプリの登録を変更すると、ユーザーに追加の同意が求められることがあります。

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
