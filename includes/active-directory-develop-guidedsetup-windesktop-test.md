---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804479"
---
## <a name="test-your-code"></a>コードのテスト

Visual Studio で、お使いのプロジェクトを実行するには、**F5** キーを押します。 以下に示すように、アプリケーション **MainWindow** が表示されます。

![アプリケーションのテスト](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

初めてアプリケーションを実行して **[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** ボタンを選択すると、サインインを求められます。 テストを行うには、Azure Active Directory アカウント (職場または学校のアカウント) または Microsoft アカウント (live.com、outlook.com) を使用します。

![アプリケーションにサインインする](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションに初めてサインインするときに、次に示すように、アプリケーションがプロファイルにアクセスし、サインインすることを許可することへの同意を求められます。

![アプリケーションによるアクセスに同意する](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインインしたら、Microsoft Graph API の呼び出しによって返されたユーザー プロファイル情報が表示されます。 結果は、**[API Call Results]\(API コールの結果\)** ボックスに表示されます。 `AcquireTokenInteractive` または `AcquireTokenSilent` の呼び出しを介して取得されたトークンに関する基本情報は、**[Token Info]\(トークン情報\)** ボックスに表示されます。 結果には、以下のプロパティが含まれます。

|プロパティ  |形式  |説明 |
|---------|---------|---------|

|**Username** |<span>user@domain.com</span> |ユーザーを識別するために使用されるユーザー名。| |**Token Expires** |DateTime |トークンの有効期限が切れる日時。 MSAL では、必要に応じてトークンを更新することで、有効期限日を延長します。|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 このスコープは、アプリケーション登録ポータルで登録されたすべてのアプリケーションで、既定で自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 Microsoft Graph API には、ユーザーの予定表を表示するための *Calendars.Read* スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、*Calendars.Read* の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、*Calendars.Read* スコープを `acquireTokenSilent` 呼び出しに追加します。

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
