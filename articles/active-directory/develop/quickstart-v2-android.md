---
title: Microsoft ID プラットフォーム Android のクイック スタート | Azure
description: Android アプリケーションから、Microsoft ID プラットフォーム エンドポイントによるアクセス トークンを必要とする API を呼び出す方法を説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678054"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>クイック スタート:Android アプリからユーザーにサインインし、Microsoft Graph API を呼び出す

このクイックスタートでは、コード サンプルを使用して、Android アプリケーションから個人、仕事、または学校のアカウントへのサインイン、アクセス トークンの取得、Microsoft Graph API の呼び出しを行う方法を示します。

![サンプル アプリのスクリーンショット](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **前提条件**
> * Android Studio 
> * Android 16+ が必須

## <a name="step-1-get-the-sample-app"></a>手順 1:サンプル アプリを入手する

[コードをクローンします](https://github.com/Azure-Samples/ms-identity-android-java.git)。

## <a name="step-2-register-your-application"></a>手順 2:アプリケーションの登録

アプリケーション オブジェクトを登録し、そのアプリケーション オブジェクトの登録情報をサンプル プロジェクトに手動で追加するには、これらの手順を実行します。

1. [Azure ポータル](https://aka.ms/MobileAppReg)にアクセスします。
1. [[アプリの登録] ブレード](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)を開き、 **[+ 新しい登録]** をクリックします。
1. ご自分のアプリ登録の**名前**を入力した後、リダイレクト URI を設定しないで、 **[登録]** をクリックします。
1. **[管理]** セクションで、 **[認証]**  >  **[+ プラットフォームの追加]**  >  **[Android]** の順に選択します (この画面を表示するには、ブレードの上部付近にある **[新しいエクスペリエンスを試す]** を選択することが必要な場合があります)。
1. ご自分のプロジェクトの**パッケージ名** (`com.azuresamples.msalandroidapp`) を入力します。
1. **[Android アプリの構成]** ページの **[署名ハッシュ]** セクションで、 **[Generating a development Signature Hash]\(開発用署名ハッシュの生成\)** をクリックし、ご自分の Android アプリの開発に使うプラットフォームに使用する KeyTool コマンドをコピーします。

   > [!Note]
   > KeyTool.exe は、Java Development Kit (JDK) の一部としてインストールされます。 KeyTool コマンドを実行するには、OpenSSL ツールもインストールする必要があります。  実際のパスには、keytool と OpenSSL\bin ディレクトリが必要になります。

1. ポータルからコピーした KeyTool コマンドをターミナル ウィンドウ内で実行します。
1. 生成された署名ハッシュをポータルの **[署名ハッシュ]** に入力します。
1. [`Configure`] をクリックし、 **[MSAL 構成]** をコピーします。 これをコピーし、次の手順で構成ファイルに貼り付けます。 **[Done]** をクリックします。

## <a name="step-3-add-your-app-registration"></a>手順 3:アプリ登録の追加

1. Android Studio でサンプル プロジェクトを開きます。
1. **[app]\(アプリ\)**  >  **[res]**  >  **[raw]\(生\)** で、**auth_config_multiple_account.json** を開きます。  MSAL 構成の内容を貼り付けます。 これにより、ポータルからクライアント ID、テナント ID、および redirect_uri が追加されます。 次のようになりますが、クライアント ID、テナント ID、および redirect_uri の値が入力されます。

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
    ```

1. **[app]\(アプリ\)**  >  **[res]**  >  **[raw]\(生\)** を開き、**auth_config_single_account.json** を開いて、MSAL 構成の内容を貼り付けます。 これは、上記の **auth_config_multiple_account.json** ファイルのようになります。
1. **[app]\(アプリ\)**  >  **[manifests]\(マニフェスト\)**  >  **[AndroidManifest.xml]** で、`BrowserTabActivity` アクティビティを探します。 このエントリにより、Microsoft は認証の完了後にアプリケーションにコールバックできます。

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. `android:host=` の値は、Azure portal 内で登録したパッケージ名に置き換えます。  この場合は `com.azuresamples.msalandroidapp` です。

    > [!IMPORTANT]
    > **android:path** の値の先頭に "/" 文字を付ける**必要があります**。そうしないと、その値の下に赤い線が表示され、サンプル アプリは実行されません。
     
1. `android:path=` の値は、先ほど KeyTool を実行し、Azure portal 内で入力したキー ハッシュに置き換えます。 署名ハッシュは、URL でエンコードしないでください。

## <a name="step-4-run-the-sample-app"></a>手順 4:サンプル アプリを実行する

Android Studio の **[available devices]\(使用可能なデバイス\)** ドロップダウンからエミュレーターまたはデバイスを選択し、アプリを実行します。

このサンプル アプリは、**単一アカウント モード**画面で開始します。 既定のスコープである **user.read** は既定で指定されます。これは、Microsoft Graph API 呼び出し時にご自分のプロファイル データを読み取るときに使用します。 Microsoft Graph API 呼び出しの URL は、既定で指定されます。 このどちらも必要に応じて変更できます。

![単一および複数アカウントの使用状況を示す MSAL サンプル アプリ](./media/quickstart-v2-android/quickstart-sample-app.png)

単一と複数アカウント モードを切り替えるには、アプリのメニューを使用します。

単一アカウント モードで、職場またはホーム アカウントを使用してサインインします。

1. **[Get graph data interactively]\(グラフ データを対話形式で取得する\)** を選択して、ユーザーに資格情報の入力を求めます。 Microsoft Graph API の呼び出しからの出力が画面の下部に表示されます。
2. サインインしたら、 **[Get graph data silently]\(グラフ データをサイレントで取得する\)** を選択して、ユーザーに資格情報の入力を再度求めることなく、Microsoft Graph API を呼び出します。 Microsoft Graph API の呼び出しからの出力が画面の下部に表示されます。

複数アカウント モードでは、同じ手順を繰り返すことができます。  さらに、サインインしているアカウントを削除することもできます。その場合、そのアカウントのキャッシュされたトークンも削除されます。

## <a name="how-the-sample-works"></a>このサンプルのしくみ

コードは、単一および複数アカウントの MSAL アプリを記述する方法を示すフラグメントに分かれています。 コード ファイルは、次のように整理されています。

| ファイル  | 対象  |
|---------|---------|
| MainActivity | UI を管理します |
| MSGraphRequestWrapper  | MSAL から提供されたトークンを使用して Microsoft Graph API を呼び出します |
| MultipleAccountModeFragment  | 複数アカウント アプリケーションを初期化し、ユーザー アカウントを読み込み、Microsoft Graph API を呼び出すためのトークンを取得します |
| SingleAccountModeFragment | 単一アカウント アプリケーションを初期化し、ユーザー アカウントを読み込み、Microsoft Graph API を呼び出すためのトークンを取得します |
| res/auth_config_multiple_account.json  | 複数アカウントの構成ファイル |
| res/auth_config_single_account.json  | 単一アカウントの構成ファイル |
| Gradle Scripts/build.grade (Module:app) | MSAL ライブラリの依存関係はここに追加します |

この後、これらのファイルについて詳しく説明し、それぞれの MSAL 固有のコードを紹介します。

### <a name="add-msal-to-the-app"></a>アプリへの MSAL の追加

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) はユーザーをサインインし、Microsoft ID プラットフォームによって保護されている API へのアクセス用のトークンを要求するために使用するライブラリです。 Gradle 3.0 以降では、**Gradle Scripts** > **build.gradle (Module: app)** の **Dependencies** に以下を追加すると、ライブラリがインストールされます。

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

これは、サンプル プロジェクトの build.gradle (Module: app) で確認できます。

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

これにより、Gradle に maven central から MSAL をダウンロードしてビルドするよう指示します。

### <a name="msal-imports"></a>MSAL のインポート

MSAL ライブラリに関連するインポートは `com.microsoft.identity.client.*` です。  たとえば、実際のパブリック クライアント アプリケーションを表す `PublicClientApplication` クラスの名前空間である `import com.microsoft.identity.client.PublicClientApplication;` があります。

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

このファイルは、単一アカウントの MSAL アプリを作成し、Microsoft Graph API を呼び出す方法を示しています。

単一アカウント アプリは、1 人のユーザーのみが使用します。  たとえば、お使いのマッピング アプリへのサインインに使用するアカウントは 1 つだけです。

#### <a name="single-account-msal-initialization"></a>単一アカウントの MSAL の初期化

`onCreateView()` では、`auth_config_single_account.json` ファイルに格納されている構成情報を使用して、単一アカウントの `PublicClientApplication` を作成します。  このようにして、単一アカウントの MSAL アプリで使用する MSAL ライブラリを初期化します。

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>ユーザーのサインイン

ユーザーをサインインさせるコードは、`initializeUI()` 内の `signInButton` クリック ハンドラーにあります。

トークンの取得を試行する前に `signIn()` を呼び出します。 `signIn()` は、`acquireToken()` が呼び出された場合と同様に動作し、ユーザーにサインインを求める対話形式のプロンプトを表示します。

ユーザーのサインインは非同期操作です。 ユーザーがサインインすると、コールバックが渡され、Microsoft Graph API が呼び出され、UI が更新されます。

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>ユーザーのサインアウト

ユーザーをサインアウトさせるコードは、`initializeUI()` 内の `signOutButton` クリック ハンドラーにあります。  ユーザーのサインアウトは非同期操作です。 ユーザーがサインアウトすると、そのアカウントのトークン キャッシュもクリアされます。 ユーザー アカウントからサインアウトすると、コールバックが作成され、UI が更新されます。

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>トークンの対話形式またはサイレントでの取得

ユーザーに表示するプロンプトの数を最小限に抑えるには、通常、トークンをサイレントで取得します。 そのときにエラーが発生した場合は、対話形式でトークンの取得を試行します。 アプリが初めて `signIn()` を呼び出すと、実質的には `acquireToken()` の呼び出しとして機能し、ユーザーに資格情報の入力を求めます。

ユーザーは、自分のアカウントの選択、自分の資格情報の入力、またはアプリから要求されたアクセス許可への同意を要求される場合があります。

* ユーザーが初めてアプリケーションにサインインする場合
* ユーザーが自分のパスワードをリセットした場合、ユーザーは自分の資格情報を入力する必要がある
* 同意が取り消された場合
* アプリが明示的に同意を要求する場合
* アプリケーションがリソースへのアクセスを初めて要求している場合
* MFA またはその他の条件付きアクセス ポリシーが必要な場合

UI をユーザーに表示して、トークンを対話形式で取得するコードは、`initializeUI()` 内の `callGraphApiInteractiveButton` クリック ハンドラーにあります。

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

ユーザーが既にサインインしている場合は、`initializeUI()` の `callGraphApiSilentButton` クリック ハンドラーに示すように、`acquireTokenSilentAsync()` を使用することで、アプリはトークンをサイレントで要求できます。

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>アカウントの読み込み

アカウントを読み込むコードは `loadAccount()` にあります。  ユーザーのアカウントの読み込みは非同期操作です。そのため、アカウントの読み込み、変更、またはエラーが発生した場合を処理するコールバックが MSAL に渡されます。  また、次のコードでは `onAccountChanged()` も処理します。これは、アカウントが削除された場合や、ユーザーが別のアカウントに変更した場合などに発生します。

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Microsoft Graph の呼び出し

ユーザーがサインインすると、`callGraphAPI()` によって Microsoft Graph の呼び出しが HTTP 要求を介して行われます。 この関数は、`authenticationResult` からのアクセス トークンの取得、MSGraphRequestWrapper への呼び出しのパッケージ化、呼び出しの結果の表示など、いくつかのタスクを実行することでサンプルを簡素化するラッパーです。

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

これは、単一のアカウントを使用する MSAL アプリの構成ファイルです。

これらのフィールドの説明については、[Android の MSAL 構成ファイルについて](msal-configuration.md)のページを参照してください。

`"account_mode" : "SINGLE"` が存在する点に注意してください。これにより、単一のアカウントを使用するようこのアプリを構成しています。

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

このファイルは、複数アカウントの MSAL アプリを作成し、Microsoft Graph API を呼び出す方法を示しています。 

複数アカウント アプリの例としては、職場アカウントや個人用アカウントなど、複数のユーザー アカウントを使用できるメール アプリがあります。

#### <a name="multiple-account-msal-initialization"></a>複数アカウントの MSAL の初期化

`onCreateView()` では、`auth_config_multiple_account.json file` に格納されている構成情報を使用して、複数アカウントのアプリ オブジェクト (`IMultipleAccountPublicClientApplication`) を作成します。

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

作成された `MultipleAccountPublicClientApplication` オブジェクトはクラス メンバー変数に格納されます。これは、MSAL ライブラリを操作して、トークンを取得したり、ユーザー アカウントを読み込んだり、削除したりする場合に使用できます。

#### <a name="load-an-account"></a>アカウントの読み込み

通常、複数アカウント アプリでは、MSAL 操作に使用するアカウントを選択する `GetAccounts()` を呼び出します。 アカウントを読み込むコードは `loadAccount()` にあります。  ユーザーのアカウントの読み込みは非同期操作です。 そのため、コールバックでは、アカウントの読み込み、変更、またはエラーが発生した場合を処理します。

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>トークンの対話形式またはサイレントでの取得

ユーザーは、自分のアカウントの選択、自分の資格情報の入力、またはアプリから要求されたアクセス許可への同意を要求される場合があります。

* ユーザーが初めてアプリケーションにサインインした場合
* ユーザーが自分のパスワードをリセットした場合、ユーザーは自分の資格情報を入力する必要がある 
* 同意が取り消された場合 
* アプリが明示的に同意を要求する場合 
* アプリケーションがリソースへのアクセスを初めて要求している場合
* MFA またはその他の条件付きアクセス ポリシーが必要な場合

通常、複数アカウント アプリでは、`acquireToken()` を呼び出して、ユーザーに UI を表示し、トークンを対話形式で取得する必要があります。  トークンを対話形式で取得するコードは、`initializeUI()` 内の `callGraphApiInteractiveButton` クリック ハンドラーにあります。

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

アプリは、トークンを要求するたびに、ユーザーにサインインを要求するべきではありません。 ユーザーが既にサインインしている場合は、`initializeUI()` の `callGraphApiSilentButton` クリック ハンドラーに示すように、`acquireTokenSilentAsync()` を使用することで、アプリはユーザーに入力を求めることなくトークンを自動的に要求できます。

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>アカウントの削除

アカウントとそのアカウントのキャッシュされたすべてのトークンを削除するコードは、`initializeUI()` 内のアカウント削除ボタンのハンドラーにあります。 アカウントを削除する前に、アカウント オブジェクトが必要です。これは、`getAccounts()` や `acquireToken()` などの MSAL 関数から取得します。 アカウントの削除は非同期操作であるため、`onRemoved` コールバックを指定して、UI を更新します。

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

これは、複数のアカウントを使用する MSAL アプリの構成ファイルです。

これらのフィールドの説明については、[Android の MSAL 構成ファイルについて](msal-configuration.md)のページを参照してください。

これは複数アカウント アプリであるため、[auth_config_single_account.json](#auth_config_single_accountjson) 構成ファイルとは異なり、この構成ファイルには `"account_mode" : "SINGLE"` の代わりに `"account_mode" : "MULTIPLE"` が含まれています。

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

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
