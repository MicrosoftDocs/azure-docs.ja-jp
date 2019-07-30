---
title: Microsoft ID プラットフォーム Android のクイック スタート | Azure
description: Android アプリケーションで、Microsoft ID プラットフォーム エンドポイントによるアクセス トークンを必要とする API を呼び出す方法を説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c22d93d39f086aaa6e2f103d3becbe9376b49b0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324546"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>クイック スタート:Android アプリからユーザーにサインインし、Microsoft Graph API を呼び出す

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

このクイック スタートには、Android アプリケーションから個人や仕事、学校のアカウントへのサインイン、アクセス トークンの取得、Microsoft Graph API の呼び出しを行う方法を示すコード サンプルが含まれています。

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **前提条件**
> * Android Studio 
> * Android 16+ が必須 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする
> クイック スタート アプリケーションを開始する方法としては、次の 2 つの選択肢があります。
> * [簡易] [選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選択肢 2: アプリケーションを登録し、コード サンプルを手動で構成する](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
> アプリを登録するには、
> 1. 新しい [Azure portal の [アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) ウィンドウに移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2:アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-your-application"></a>手順 1: アプリケーションの登録
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://aka.ms/MobileAppReg) ページに移動します。
> 1. **[新規登録]** を選択します。
> 1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
>      - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `AndroidQuickstart`)。
>      - このページの他の構成はスキップできます。 
>      - [`Register`] ボタンをクリックします。
> 1. 新しいアプリをクリックし、`Authentication` > `Add Platform` > `Android` の順に選択します。    
>      - Android Studio プロジェクトのパッケージ名を入力します。 
>      - 署名ハッシュを生成します。 手順についてはポータルを参照してください。
> 1. [`Configure`] を選択し、後に備えて ***MSAL 構成*** JSON を保存します。 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>手順 1:アプリケーションの作成
> このクイック スタートのコード サンプルを動作させるには、Auth ブローカーと互換性があるリダイレクト URI を追加する必要があります。 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-android/green-check.png) アプリケーションはこれらの属性で構成されています

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

* [コード サンプルのダウンロード](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>手順 3:プロジェクトを構成する

> [!div renderon="docs"]
> 上のオプション 1 を選択した場合は、以下の手順を省略できます。 

> [!div renderon="portal" class="sxs-lookup"]
> 1. プロジェクトを解凍し、Android Studio で開きます。
> 1. **[app]**  >  **[src]**  >  **[main]**  >  **[res]**  >  **[raw]** で、**auth_config.json** を開きます。
> 1. **auth_config.json** を編集し、Azure portal からの JSON に置き換えます。 そうせずに、手動で変更する場合は、次のようにします。
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
>    ```
> 
> 1. **[app]**  >  **[manifests]** で、**AndroidManifest.xml** を開きます。
> 1. **manifest\application** ノードに次のアクティビティを貼り付けます。 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. アプリを実行します。 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > このクイックスタートは、Enter_the_Supported_Account_Info_Here をサポートしています。

> [!div renderon="docs"]
> 1. プロジェクトを解凍し、Android Studio で開きます。
> 1. **[app]**  >  **[res]**  >  **[raw]** で、**auth_config.json** を開きます。
> 1. **auth_config.json** を編集し、Azure portal からの JSON に置き換えます。 そうせずに、手動でこれらの変更をする場合は、次のようにします。
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. **[app]**  >  **[manifests]** で、**AndroidManifest.xml** を開きます。
> 1. **manifest\application** ノードに次のアクティビティを追加します。 このコード スニペットにより **BrowserTabActivity** が登録され、OS は認証の完了後、アプリケーションを再開することが可能になります。
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. `Enter_the_Package_Name` および `Enter_the_Signature_Hash` を、Azure portal に登録した値に置き換えます。 
> 1. アプリを実行します。 

## <a name="more-information"></a>詳細情報

このクイック スタートの詳細については、以下のセクションをお読みください。

### <a name="getting-msal"></a>MSAL の取得

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) はユーザーをサインインし、Microsoft ID プラットフォームによって保護されている API へのアクセス用のトークンを要求するために使用するライブラリです。 Gradle 3.0+ を使用してこれをインストールするには、**Gradle Scripts** > **build.gradle (Module: app)** の **Dependencies** に以下を追加します。

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```java
import com.microsoft.identity.client.*;
```

続いて、次のコードを使用して MSAL を初期化します。

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |各値の説明: ||
> |---------|---------|
> |`R.raw.auth_config` | このファイルには、アプリ/クライアント ID、サインイン対象ユーザー、リダイレクト URI、その他のいくつかのカスタマイズ オプションなど、アプリケーションの構成が含まれています。 |

### <a name="requesting-tokens"></a>トークンの要求

MSAL には、トークンの取得に使用する 2 つのメソッド `acquireToken`、`acquireTokenSilentAsync` があります。

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: トークンを対話形式で取得する

状況によっては、ユーザーが Microsoft ID プラットフォームと対話する必要があります。 このような場合、エンド ユーザーは、自分のアカウントの選択、自分の資格情報の入力、またはアプリから要求されたアクセス許可への同意を要求される可能性があります。 たとえば、次のように入力します。 

* ユーザーが初めてアプリケーションにサインインした場合
* ユーザーが自分のパスワードをリセットした場合、ユーザーは自分の資格情報を入力する必要がある 
* 同意が取り消された場合 
* アプリが明示的に同意を要求する場合。 
* アプリケーションがリソースへのアクセスを初めて要求している場合
* MFA またはその他の条件付きアクセス ポリシーが必要な場合

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |各値の説明:||
> |---------|---------|
> | `SCOPES` | 要求するスコープを含む (つまり、Microsoft Graph 用の `{ "user.read" }` またはカスタム Web API 用の `{ "<Application ID URL>/scope" }` (つまり、`api://<Application ID>/access_as_user`)) |
> | `getAuthInteractiveCallback` | 認証後にアプリケーションに制御が戻るときに実行されるコールバック |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: ユーザー トークンを自動で取得する

アプリは、トークンを要求するたびに、ユーザーにサインインを要求するべきではありません。 ユーザーが既にサインインしている場合は、この方法により、アプリはトークンを暗黙的に要求できます。

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |各値の説明:||
> |---------|---------|
> | `SCOPES` | 要求するスコープを含む (つまり、Microsoft Graph 用の `{ "user.read" }` またはカスタム Web API 用の `{ "<Application ID URL>/scope" }` (つまり、`api://<Application ID>/access_as_user`)) |
> | `getAccounts(...)` | 自動的にトークンの取得が試行されるアカウントを含む |
> | `getAuthSilentCallback()` | 認証後にアプリケーションに制御が戻るときに実行されるコールバック |

## <a name="next-steps"></a>次の手順

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>このクイック スタートで使用されているアプリケーションを作成する手順

アプリケーションや新機能の構築についての完全なステップ バイ ステップ ガイドは、Android チュートリアルをお試しください。このクイック スタートの完全な説明も含まれています。

> [!div class="nextstepaction"]
> [Graph API 呼び出し Android チュートリアル](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Android 用 MSAL ライブラリ Wiki

Android 用 MSAL ライブラリの詳細をご覧ください。

> [!div class="nextstepaction"]
> [Android 用 MSAL ライブラリ Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft ID プラットフォームの改善にご協力ください。 簡単な 2 つの質問からなるアンケートに記入し、ご意見をお聞かせください。

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)