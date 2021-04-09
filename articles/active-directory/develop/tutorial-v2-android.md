---
title: チュートリアル:認証に Microsoft ID プラットフォームを使用する Android アプリを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、ユーザーのサインインに Microsoft ID プラットフォームを使用し、そのユーザーに代わって Microsoft Graph API を呼び出すためのアクセス トークンを取得する Android アプリを作成します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7d297d96ba764c812a3d4db6d9383122c73cfe31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103143"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>チュートリアル:Android アプリケーションからユーザーをサインインさせて Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを処理し、Microsoft Graph API を呼び出すアクセス トークンを取得するために Microsoft ID プラットフォームと連携する Android アプリを作成します。

このチュートリアルを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

このチュートリアルの内容: 

> [!div class="checklist"]
> * *Android Studio* で Android アプリ プロジェクトを作成する
> * Azure portal でアプリを登録する
> * ユーザーのサインインとサインアウトをサポートするコードを追加する
> * Microsoft Graph API を呼び出すコードを追加する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

* Android Studio 3.5 以上

## <a name="how-this-tutorial-works"></a>このチュートリアルのしくみ

![このチュートリアルで生成されたサンプル アプリの動作の紹介](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

このチュートリアルのアプリでは、ユーザーがサインインされ、ユーザーに代わってデータが取得されます。 このデータは、承認を要求し、Microsoft ID プラットフォームによって保護される API (Microsoft Graph API) を介してアクセスされます。

具体的には次のとおりです。

* お使いアプリで、ブラウザーまたは Microsoft Authenticator と Intune ポータル サイトを介してユーザーをサインインさせます。
* エンド ユーザーが、アプリケーションから要求されたアクセス許可を受け入れます。
* アプリケーションには、Microsoft Graph API 用のアクセス トークンが発行されます。
* アクセス トークンは、Web API への HTTP 要求に含められます。
* Microsoft Graph の応答を処理します。

このサンプルでは、Microsoft Authentication Library for Android (MSAL) の [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal) を使用して認証を実装します。

MSAL により、トークンが自動的に更新され、デバイス上の他のアプリとの間のシングル サインオン (SSO) が提供されて、アカウントが管理されます。

このチュートリアルでは、Android 用の MSAL を使用する簡単な例を紹介します。 わかりやすくするため、単一アカウント モードのみを使用します。 さらに複雑なシナリオを試したい方は、GitHub で完成版の[コード サンプル](https://github.com/Azure-Samples/ms-identity-android-java/)を参照してください。

## <a name="create-a-project"></a>プロジェクトの作成
Android アプリケーションがまだない場合は、次の手順に従って新しいプロジェクトを設定します。

1. Android Studio を開き、 **[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。
2. **[Basic Activity]\(基本アクティビティ\)** を選択し、**[Next]\(次へ\)** を選択します。
3. アプリケーションに名前を付けます。
4. パッケージ名を保存しておきます。 後ほど、Azure portal でそれを入力します。
5. 言語を **[Kotlin]** から **[Java]** に変更します。
6. **[Minimum API level]\(最低 API レベル\)** を **API 19** 以上に設定し、**[Finish]\(完了\)** をクリックします。
7. プロジェクト ビューのドロップダウンで **[Project]\(プロジェクト\)** を選択して、ソースとソース以外のプロジェクト ファイルを表示し、**app/build.gradle** を開いて、`targetSdkVersion` を `28` に設定します。

## <a name="integrate-with-the-microsoft-authentication-library"></a>Microsoft Authentication Library と統合する

### <a name="register-your-application"></a>アプリケーションの登録

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **[名前]** を入力します。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. **[登録]** を選択します。
1. **[管理]** で、 **[認証]**  >  **[プラットフォームの追加]**  >  **[Android]** の順に選択します。
1. プロジェクトのパッケージ名を入力します。 コードをダウンロードした場合、この値は `com.azuresamples.msalandroidapp` です。
1. **[Android アプリの構成]** ページの **[署名ハッシュ]** セクションで、 **[Generating a development Signature Hash]\(開発用署名ハッシュの生成\)** を選択します。 そして、お使いのプラットフォームに使用する KeyTool コマンドをコピーします。


     KeyTool.exe は、Java Development Kit (JDK) の一部としてインストールされます。 KeyTool コマンドを実行するには、OpenSSL ツールもインストールする必要があります。 詳細については、[キーの生成に関する Android のドキュメント](https://developer.android.com/studio/publish/app-signing#generate-key)を参照してください。

1. KeyTool によって生成された **署名ハッシュ** を入力します。
1. **[構成]** をクリックし、後でアプリを構成するときに入力できるように、 **[Android の構成]** ページに表示される **[MSAL 構成]** を保存しておきます。  
1. **[完了]** を選択します。

### <a name="configure-your-application"></a>アプリケーションの作成

1. Android Studio のプロジェクト ウィンドウで、**app\src\main\res** に移動します。
1. **res** を右クリックして、**[New]\(新規\)** > **[Directory]\(ディレクトリ\)** を選択します。 新しいディレクトリの名前に「`raw`」と入力し、**[OK]** をクリックします。
1. **app** > **src** > **main** > **res** > **raw** で、`auth_config_single_account.json` という名前の新しい JSON ファイルを作成し、先ほど保存した MSAL 構成を貼り付けます。

    リダイレクト URI の下に、以下を貼り付けます。
    ```json
      "account_mode" : "SINGLE",
    ```
    構成ファイルは次の例のようになります。
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
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

     このチュートリアルでは、単一アカウント モードでアプリを構成する方法についてのみ説明します。 [単一アカウント モードと複数アカウント モードの比較](./single-multi-account.md)および[アプリの構成](./msal-configuration.md)の詳細については、ドキュメントを参照してください

4. **app** > **src** > **main** > **AndroidManifest.xml** で、以下の `BrowserTabActivity` アクティビティをアプリケーション本文に追加します。 このエントリにより、Microsoft は認証の完了後にアプリケーションにコールバックできます。

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    `android:host=` は、Azure portal で登録したパッケージ名に置き換えます。
    `android:path=` は、Azure portal で登録したキー ハッシュに置き換えます。 署名ハッシュは、URL で **エンコードしない** でください。 署名ハッシュの先頭に `/` があることを確認します。
    
    `android:host` 値を置き換える "パッケージ名" は、"com.azuresamples.msalandroidapp" のようになります。
    `android:path` 値を置き換える "署名ハッシュ" は、"/1wIqXSqBj7w+h11ZifsnqwgyKrY=" のようになります。
    
    これらの値は、アプリの登録の [認証] ブレードでも確認できます。 リダイレクト URI は次のようになります: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D"。 この値の最後にある署名ハッシュは URL でエンコードされていますが、`android:path` の値では署名ハッシュは URL でエンコードされていては **なりません**。

## <a name="use-msal"></a>MSAL の使用

### <a name="add-msal-to-your-project"></a>プロジェクトに MSAL を追加する

1. Android Studio のプロジェクト ウィンドウで、**app** > **src** > **build.gradle** に移動し、以下を追加します。

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Microsoft Graph SDK の詳細](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>必要なインポート

**app** > **src** > **main** > **java** > **com.example(yourapp)**  > **MainActivity.java** の先頭に、以下を追加します

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>PublicClientApplication をインスタンス化する
#### <a name="initialize-variables"></a>変数を初期化する
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
`MainActivity` クラス内で、次の onCreate() メソッドを参照し、`SingleAccountPublicClientApplication` を使用して MSAL をインスタンス化します。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>initializeUI
ボタンをリッスンし、それに応じて、メソッドを呼び出すか、エラーをログに記録します。
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> MSAL でサインアウトすると、ユーザーに関する既知の情報がすべてこのアプリケーションから削除されますが、ユーザーのデバイスではアクティブなセッションが維持されます。 ユーザーが再びサインインを試みた場合、サインイン UI が表示されることがありますが、デバイス セッションはまだアクティブであるため、資格情報を再入力する必要はありません。

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
対話型要求に使用されるコールバック。

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
サイレント要求に使用されるコールバック
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Microsoft Graph API を呼び出す

次のコードでは、Graph SDK を使用して GraphAPI を呼び出す方法を示します。

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>UI の追加
### <a name="activity"></a>アクティビティ
このチュートリアルで UI をモデル化する場合は、次のメソッドを使用して、テキストの更新とボタンのリッスンを行うことができます。

#### <a name="updateui"></a>updateUI
サインインの状態に基づいてボタンを有効または無効にし、テキストを設定します。
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
サインアウトを反映して UI 内のテキストを更新するメソッド。

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>レイアウト

ボタンとテキスト ボックスを表示するサンプルの `activity_main.xml` ファイル。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>アプリをテストする

### <a name="run-locally"></a>ローカルで実行する

アプリをビルドし、テスト デバイスまたはエミュレーターに展開します。 サインインして、Azure AD または個人用 Microsoft アカウントのトークンを取得できるようになります。

サインインした後、Microsoft Graph の `/me` エンドポイントから返されたデータがアプリに表示されます。
PR 4
### <a name="consent"></a>同意

ユーザーは、アプリに初めてサインインするときに、Microsoft Identity から、要求されたアクセス許可に同意するよう求められます。 一部の Azure AD テナントではユーザーによる同意が無効になっており、全ユーザーに代わって管理者が同意を行う必要があります。 このシナリオをサポートするには、独自のテナントを作成するか、管理者の同意を受け取る必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、「[アプリケーションの登録](#register-your-application)」の手順で作成したアプリ オブジェクトを削除します。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

保護された Web API を呼び出すモバイル アプリを作成するには、複数のパートで構成される次のシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ:Web API を呼び出すモバイル アプリケーション](scenario-mobile-overview.md)
