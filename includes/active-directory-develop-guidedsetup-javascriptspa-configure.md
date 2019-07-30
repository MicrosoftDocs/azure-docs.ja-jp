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
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133859"
---
## <a name="register-your-application"></a>アプリケーションの登録

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. お使いのアカウントで複数のテナントにアクセスできる場合は、右上でアカウントを選択した後、使用する Azure AD テナントにポータル セッションを設定します。
1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. **[リダイレクト URI]** セクションで、ドロップダウン リストから **Web** プラットフォームを選択し、ご使用の Web サーバーに基づいてアプリケーション URL に値を設定します。 

   Visual Studio と Node.js でのリダイレクト URL の設定と取得の詳細については、次の 2 つのセクションを参照してください。

1. **[登録]** を選択します。
1. 後で使用するために、アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。
1. このクイック スタートでは、[暗黙的な許可フロー](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。 登録済みのアプリケーションの左側のウィンドウで、 **[認証]** を選択します。
1. **[詳細設定]** の **[暗黙的な許可]** で、 **[ID トークン]** チェック ボックスと **[アクセス トークン]** チェック ボックスをオンにします。 このアプリではユーザーのサインインを実行して API を呼び出す必要があるため、ID トークンとアクセス トークンが必要です。
1. **[保存]** を選択します。

> #### <a name="set-a-redirect-url-for-nodejs"></a>Node.js でリダイレクト URL を設定する
> Node.js の場合は、Web サーバーのポートを *server.js* ファイルで設定できます。 このチュートリアルでは、参照用にポート 30662 を使用しますが、使用可能なその他の任意のポートを使用できます。 
>
> アプリケーション登録情報の中にリダイレクト URL を設定するには、 **[アプリケーションの登録]** ウィンドウに切り替え、以下のいずれかを行います。
>
> - **リダイレクト URL** として *`http://localhost:30662/`* を設定します。
> - カスタム TCP ポートを使用している場合は、 *`http://localhost:<port>/`* を使用します (ここで、 *\<port>* はカスタム TCP ポート番号です)。
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Visual Studio でリダイレクト URL を設定する
> Visual Studio のリダイレクト URL を取得するには、以下を行います。
> 1. **Solution Explorer** で、プロジェクトを選択します。
>
>    **[プロパティ]** ウィンドウが開きます。 開かない場合は、**F4** キーを押します。
>
>    ![[JavaScriptSPA プロジェクトのプロパティ] ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. **[URL]** の値をコピーします。
 
> 1. **[アプリケーション登録]** ウィンドウに切り替え、コピーした値を **リダイレクト URL** として貼り付けます。

#### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

1. プロジェクトの設定中に作成した *index.html* ファイルに、アプリケーション登録情報を追加します。 ファイルの先頭で、`<script></script>` タグの中に、次のコードを追加します。

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    各値の説明:
    - *\<Enter_the_Application_Id_here>* は、登録したアプリケーションの**アプリケーション (クライアント) ID** です。
    - *\<Enter_the_Tenant_info_here>* は、次のオプションのいずれかに設定されます。
       - お使いのアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を**テナント ID** または**テナント名** (例: *contoso.microsoft.com*) に置き換えます。
       - お使いのアプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます
       - お使いのアプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。
