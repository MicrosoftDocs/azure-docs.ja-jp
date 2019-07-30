---
title: Android の概要 - Microsoft ID プラットフォーム | Azure
description: Android アプリがアクセス トークンを取得し、Microsoft ID プラットフォームからのアクセス トークンを必要とする Microsoft Graph API を呼び出す方法。
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb1e322e0424debc14a29ad8a516c95acea54714
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872108"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Android アプリケーションからユーザーにサインインし、Microsoft Graph を呼び出す

このチュートリアルでは、Android アプリを Microsoft ID プラットフォームと統合する方法を学習します。 お使いのアプリによって、ユーザーがサインインされ、Microsoft Graph API を呼び出すためのアクセス トークンが取得されて、Microsoft Graph API への要求が行われます。  

このガイドを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

## <a name="how-this-tutorial-works"></a>このチュートリアルのしくみ

![このチュートリアルで生成されたサンプル アプリの動作の紹介](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

このチュートリアルのアプリでは、ユーザーがサインインされ、ユーザーに代わってデータが取得されます。  このデータは、承認を要求し、Microsoft ID プラットフォームによって保護される API (Microsoft Graph API) を介してアクセスされます。

具体的には次のとおりです。

* お使いアプリで、ブラウザーまたは Microsoft Authenticator と Intune ポータル サイトを介してユーザーをサインインさせます。
* エンド ユーザーが、アプリケーションから要求されたアクセス許可を受け入れます。
* アプリケーションには、Microsoft Graph API 用のアクセス トークンが発行されます。
* アクセス トークンは、Web API への HTTP 要求に含められます。
* Microsoft Graph の応答を処理します。

このサンプルでは、Android 用の Microsoft Authentication Library (MSAL) の [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal) を使用して認証を実装します。

 MSAL により、トークンが自動的に更新され、デバイス上の他のアプリとの間のシングル サインオン (SSO) が提供されて、アカウントが管理されます。

## <a name="prerequisites"></a>前提条件

* このチュートリアルでは、Android Studio バージョン 16 以降 (19 以降を推奨) が必要です。

## <a name="create-a-project"></a>プロジェクトの作成

このチュートリアルでは新しいプロジェクトを作成します。 代わりに完了したチュートリアルをダウンロードする場合は、[コードをダウンロード](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)してください。

1. Android Studio を開き、 **[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。
2. **[Basic Activity]\(基本アクティビティ\)** を選択し、 **[Next]\(次へ\)** を選択します。
3. アプリケーションに名前を付けます。
4. パッケージ名を保存しておきます。 後ほど、Azure portal でそれを入力します。
5. **[Minimum API level]\(最低 API レベル\)** を **API 19** 以上に設定し、 **[Finish]\(完了\)** をクリックします。
6. プロジェクト ビューのドロップダウンで **[Project]\(プロジェクト\)** を選択して、ソースとソース以外のプロジェクト ファイルを表示し、**app/build.gradle** を開いて、`targetSdkVersion` を `27` に設定します。

## <a name="register-your-application"></a>アプリケーションの登録

1. [Azure ポータル](https://aka.ms/MobileAppReg)にアクセスします。
2. [[アプリの登録] ブレード](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)を開き、 **[+ 新しい登録]** をクリックします。
3. アプリの **[名前]** を入力した後、リダイレクト URI を設定しないで、 **[登録]** をクリックします。
4. 表示されたウィンドウの **[管理]** セクションで、 **[認証]**  >  **[+ プラットフォームの追加]**  >  **[Android]** を選択します。
5. プロジェクトのパッケージ名を入力します。 コードをダウンロードした場合、この値は `com.azuresamples.msalandroidapp` です。
6. **[Android アプリの構成]** ページの **[署名ハッシュ]** セクションで、 **[Generating a development Signature Hash]\(開発用署名ハッシュの生成\)** をクリックします。 そして、お使いのプラットフォームに使用する KeyTool コマンドをコピーします。

   > [!Note]
   > KeyTool.exe は、Java Development Kit (JDK) の一部としてインストールされます。 KeyTool コマンドを実行するには、OpenSSL ツールもインストールする必要があります。

7. KeyTool によって生成された**署名ハッシュ**を入力します。
8. [`Configure`] をクリックし、後でアプリを構成するときに入力できるように、 **[Android の構成]** ページに表示される **[MSAL 構成]** を保存しておきます。  **[Done]** をクリックします。

## <a name="build-your-app"></a>アプリの構築

### <a name="add-your-app-registration"></a>アプリ登録の追加

1. Android Studio のプロジェクト ウィンドウで、**app\src\main\res** に移動します。
2. **res** を右クリックして、 **[New]\(新規\)**  >  **[Directory]\(ディレクトリ\)** を選択します。 新しいディレクトリの名前に「`raw`」と入力し、 **[OK]** をクリックします。
3. **app** > **src** > **res** > **raw** で、`auth_config.json` という名前の新しい JSON ファイルを作成し、先ほど保存した MSAL 構成を貼り付けます。 詳細については、[MSAL の構成](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)に関するページを参照してください。
4. **app** > **src** > **main** > **AndroidManifest.xml** で、以下の `BrowserTabActivity` アクティビティを追加します。 このエントリにより、Microsoft は認証の完了後にアプリケーションにコールバックできます。

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
    `android:path=` は、Azure portal で登録したキー ハッシュに置き換えます。 署名ハッシュは、URL でエンコードしないでください。

5. **AndroidManifest.xml** 内の `<application>` タグのすぐ上に、次のアクセス許可を追加します。

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>アプリケーションの UI を作成する

1. Android Studio のプロジェクト ウィンドウで、**app** > **src** > **main** > **res** > **layout** に移動し、**activity_main.xml** を開いて、 **[Text]\(テキスト\)** ビューを開きます。
2. アクティビティのレイアウトを変更します (たとえば、`<androidx.coordinatorlayout.widget.CoordinatorLayout` を `<androidx.coordinatorlayout.widget.LinearLayout` に)。
3. `android:orientation="vertical"` プロパティを `LinearLayout` ノードに追加します。
4. `LinearLayout` ノードに次のコードを貼り付け、現在の内容を置き換えます。

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>プロジェクトに MSAL を追加する

1. Android Studio のプロジェクト ウィンドウで、**app** > **src** > **build.gradle** に移動します。
2. **[Dependencies]\(依存関係\)** の下に、以下を貼り付けます。

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>MSAL の使用

次に、`MainActivity.java` の内部を変更し、アプリで使用する MSAL を追加します。
Android Studio のプロジェクト ウィンドウで、**app** > **src** > **main** > **java** > **com.example.msal** に移動し、`MainActivity.java` を開きます。

#### <a name="required-imports"></a>必要なインポート

`MainActivity.java` の先頭付近に、次の import を追加します。

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>MSAL をインスタンス化する

`MainActivity` クラス内で、MSAL をインスタンス化するとともに、アクセスしたいスコープや Web API など、アプリが何をするのかについての構成をいくつか行う必要があります。

次の変数を、`MainActivity` クラスの内部にコピーします。

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

`onCreate()` の内容を、MSAL をインスタンス化する次のコードに置き換えます。

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

上記のコードでは、ユーザーがアプリケーションを開いたら、`getAccounts()` が呼び出され、それが成功したら `acquireTokenSilentAsync()` が呼び出されて、サイレントでのユーザーのサインインが試みられます。  次のいくつかのセクションでは、サインインしたアカウントがない場合のためにコールバック ハンドラーを実装します。

#### <a name="use-msal-to-get-tokens"></a>MSAL を使用してトークンを取得する

これで、アプリの UI 処理ロジックを実装し、MSAL を介して対話的にトークンを取得できるようになりました。

MSAL では、トークンを取得するための主要なメソッドとして `acquireTokenSilentAsync()` と `acquireToken()` が公開されています。  

`acquireTokenSilentAsync()` では、ユーザーのサインインが行われ、アカウントが存在すればユーザーとの対話なしにトークンが取得されます。 成功した場合、MSAL はトークンをお使いのアプリに渡します。失敗した場合、`MsalUiRequiredException` を生成します。  この例外が生成される場合、または対話型サインイン エクスペリエンス (資格情報、mfa、または他の条件付きアクセス ポリシーが必要なこともあれば、不要なこともあります) をユーザーに提供する場合は、`acquireToken()` を使用します。  

ユーザーをサインインさせてトークンを取得しようとすると、`acquireToken()` で UI が表示されます。 ただし、ブラウザーでセッション Cookie を使用するか、または Microsoft 認証システムでアカウントを使用して、対話型の SSO エクスペリエンスを提供できます。

`MainActivity` クラス内に、次の 3 つの UI メソッドを作成します。

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

次のメソッドを追加し、現在のアクティビティを取得して、サイレント型および対話型のコールバックを処理します。

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>サインアウトに MSAL を使用する

次に、サインアウトのサポートを追加します。

> [!Important]
> MSAL でサインアウトすると、ユーザーに関する既知の情報がすべてこのアプリケーションから削除されますが、ユーザーのデバイスではアクティブなセッションが維持されます。 ユーザーが再びサインインを試みた場合、サインイン UI が表示されることがありますが、デバイス セッションはまだアクティブであるため、資格情報を再入力する必要はありません。

サインアウト機能を追加するには、`MainActivity` クラスの内部に次のメソッドを追加します。 このメソッドでは、すべてのアカウントが順次削除されます。

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API を呼び出す

トークンを受け取ったら、[Microsoft Graph API](https://graph.microsoft.com) に対して要求を実行できます。アクセス トークンは、認証コールバックの `onSuccess()` メソッド内の `AuthenticationResult` の中にあります。 承認された要求を作成するには、お使いのアプリで、アクセス トークンを HTTP ヘッダーに追加する必要があります。

| ヘッダー キー    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer \<access-token> |

次の 2 つのメソッドを `MainActivity` クラス内に追加し、グラフを呼び出して UI を更新します。

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>マルチアカウント アプリケーション

このアプリは、シングル アカウントのシナリオを対象にビルドされています。 MSAL ではマルチアカウントのシナリオもサポートされますが、その場合はアプリで追加の作業が必要となります。 トークンを必要とする各アクションに使用するアカウントをユーザーが選択するための UI を作成する必要があります。 あるいは、アプリでヒューリスティックを実装して、`getAccounts()` メソッドによって使用するアカウントを選択することもできます。

## <a name="test-your-app"></a>アプリをテストする

### <a name="run-locally"></a>ローカルで実行する

アプリをビルドし、テスト デバイスまたはエミュレーターに展開します。 サインインして、Azure AD または個人用 Microsoft アカウントのトークンを取得できるようになります。

サインインした後、Microsoft Graph の `/me` エンドポイントから返されたデータがアプリに表示されます。

### <a name="consent"></a>同意

ユーザーは、アプリに初めてサインインするときに、Microsoft Identity から、要求されたアクセス許可に同意するよう求められます。  ほとんどのユーザーは同意できますが、一部の Azure AD テナントではユーザーによる同意が無効になっており、全ユーザーに代わって管理者が同意を行う必要があります。 このシナリオをサポートするには、Azure portal でアプリのスコープを登録します。

## <a name="get-help"></a>問い合わせ

このチュートリアルまたは Microsoft ID プラットフォームで問題が発生した場合は、[ヘルプとサポート](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)に関する記事をご覧ください。
