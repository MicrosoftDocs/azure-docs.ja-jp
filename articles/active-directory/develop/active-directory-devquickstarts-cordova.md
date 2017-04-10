---
title: "Azure AD Cordova の概要 | Microsoft Docs"
description: "OAuth を使用して、Azure AD と連携してサインインし、Azure AD で保護されている API を呼び出す Cordova アプリケーションを構築する方法を説明します。"
services: active-directory
documentationcenter: 
author: vibronet
manager: mbaldwin
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: vittorib
translationtype: Human Translation
ms.sourcegitcommit: 0d7333f9752a9e03c1087a4bc588284b418d8926
ms.openlocfilehash: 4a80252f139d653ff8788b3c1a6a075448cb48e7


---
# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Azure AD と Apache Cordova アプリとの統合
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Apache Cordova を使用して、完全なネイティブ アプリケーションとしてモバイル デバイス上で実行できる HTML5/JavaScript アプリケーションを開発できます。 Azure Active Directory (Azure AD) を使用すると、Cordova アプリケーションにエンタープライズ レベルの認証機能を追加できます。

Cordova プラグインは、iOS、Android、Windows ストア、および Windows Phone 上の Azure AD ネイティブ SDK をラップします。 このプラグインを使用することで、アプリケーションを拡張し、ユーザーの Windows Server Active Directory のアカウントでのサインインのサポート、Office 365 や Azure API へのアクセス、さらに独自のカスタム Web API の呼び出しの保護のサポートが可能になります。

このチュートリアルでは、Active Directory 認証ライブラリ (ADAL) の Apache Cordova プラグインを使用して、次の機能を付加して簡単なアプリケーションを改善します。

* 数行のコードのみを追加して、ユーザーを認証し、トークンを取得します。
* そのトークンを使用して Graph API を呼び出し、そのディレクトリのクエリを実行して結果を表示します。  
* ADAL トークン キャッシュを利用して、ユーザーに対する認証プロンプトを最小限に抑えます。

以上の改善を行うには、次の手順に従う必要があります。

1. アプリケーションを Azure AD に登録する。
2. トークンを要求するコードをアプリケーションに追加する
3. トークンを使用して Graph API のクエリを実行して結果を表示するコードを追加する
4. 対象とするすべてのプラットフォームでの Cordova デプロイ プロジェクトを作成し、Cordova ADAL プラグインを追加して、エミュレーターでソリューションをテストする

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次のものが必要です。

* アプリケーション開発権限を持つアカウントが登録されている Azure AD テナント
* Apache Cordova を使用するように構成された開発環境  

両方とも設定済みの場合は、手順 1. に直接進んでください。

Azure AD テナントがない場合は、[取得方法の手順](active-directory-howto-tenant.md)を使用してください。

Apache Cordova がコンピューターにセットアップされていない場合は、以下をインストールしてください。

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.JS](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/) (NPM パッケージ マネージャー `npm install -g cordova` を使用して簡単にインストールできます)

前述のインストールは、PC と Mac のどちらでも動作します。

各ターゲット プラットフォームには、それぞれ異なる前提条件があります。

* Windows タブレット/PC または Windows Phone 向けアプリをビルドして実行するには
  * [Visual Studio 2013 for Windows Update 2 以降](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express または別のバージョン) または [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community) をインストールします。

* iOS 向けアプリをビルドして実行するには

  * Xcode 6.x 以降をインストールします。 これは [Apple の開発者向けサイト](http://developer.apple.com/downloads)または [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12) からダウンロードします。
  * [ios-sim](https://www.npmjs.org/package/ios-sim) をインストールします。 これを使用して、コマンドラインから iOS シミュレーターで iOS アプリを起動できます  (ターミナルで `npm install -g ios-sim` を実行して簡単にインストールできます)。
* Android 向けアプリをビルドして実行するには

  * [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 以上をインストールします。 `JAVA_HOME` (環境変数) が JDK インストール パス (たとえば、C:\Program Files\Java\jdk1.7.0_75) に従って正しく設定されていることを確認します。
  * [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) をインストールし、`<android-sdk-location>\tools` の場所 (たとえば、C:\tools\Android\android-sdk\tools) を `PATH` 環境変数に追加します。
  * Android SDK Manager を開き (たとえば、ターミナル `android` 経由で)、以下をインストールします。
    * *Android 5.0.1 (API 21)* プラットフォーム SDK
    * "*Android SDK ビルド ツール*" バージョン 19.1.0 以降
    * *Android Support Repository* (追加)

  Android SDK では、既定のエミュレーター インスタンスは提供されていません。 エミュレーターで Android アプリを実行する場合は、ターミナルから `android avd` を実行し、次に **[作成...]** を選択してエミュレーター インスタンスを作成します。 API レベル 19 以降をお勧めします。 Android エミュレーターおよび作成のオプションの詳細については、Android サイトの [AVD Manager](http://developer.android.com/tools/help/avd-manager.html) に関するページを参照してください。

## <a name="step-1-register-an-application-with-azure-ad"></a>ステップ 1. アプリケーションを Azure AD に登録する
この手順は省略可能です。 このチュートリアルでは、事前にプロビジョニングされた値を用意しています。これにより独自のテナントで何もプロビジョニングしなくても、実行するサンプルを参照できます。 ただし、この手順を必ず実行し、プロセスをよく理解することをお勧めします。このプロセスは、独自のアプリケーションを作成する際に必要になります。

Azure AD では、既知のアプリケーションにのみトークンを発行します。 アプリケーションから Azure AD を使用するには、アプリケーション用のエントリをテナントで事前に作成しておく必要があります。 新しいアプリケーションをテナントに登録するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部のバーで、自分のアカウントをクリックします。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Azure AD テナントを選択します。
3. 左側のウィンドウで **[その他のサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. 画面の指示に従い、**ネイティブ クライアント アプリケーション**を作成します  (Cordova アプリは HTML ベースですが、ここではネイティブ クライアント アプリケーションを作成します。 **[ネイティブ クライアント アプリケーション]** オプションを選択する必要があります。選択しない場合はアプリケーションが動作しません)。
  * **[名前]** を使用して、アプリケーションについてユーザーに説明します。
  * **[リダイレクト URI]** は、トークンをアプリに返すために使用される URI です。 「**http://MyDirectorySearcherApp**」を入力します。

登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。 この値は次のセクションで必要になります。 新しく作成されたアプリの [アプリケーション] タブでこの値を確認できます。

`DirSearchClient Sample` を実行するには、新しく作成されたアプリに Azure AD Graph API をクエリするためのアクセス許可を付与します。

1. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。  
2. Azure Active Directory アプリケーションの場合は、API として **[Microsoft Graph] ** を選択し、**[デリゲートされたアクセス許可]** の下に **[サインイン ユーザーとしてディレクトリにアクセスする]** アクセス許可を追加します。  これにより、アプリケーションが Graph API を使用してユーザーをクエリできるようになります。

## <a name="step-2-clone-the-sample-app-repository"></a>ステップ 2. サンプル アプリ リポジトリを複製する
シェルまたはコマンド ラインから、次のコマンドを入力します。

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>ステップ 3. Cordova アプリを作成する
Cordova アプリケーションを作成するには、複数の方法を使用できます。 このチュートリアルでは、Cordova コマンド ライン インターフェイス (CLI) を使用します。

1. シェルまたはコマンド ラインから、次のコマンドを入力します。

        cordova create DirSearchClient

   このコマンドによって、Cordova プロジェクト用のフォルダー構造とスキャフォールディングが作成されます。

2. 新しい DirSearchClient フォルダーに移動します。

        cd .\DirSearchClient

3. ファイル マネージャーまたはシェルで次のコマンドを使用して、スタート プロジェクトの内容を www サブフォルダーにコピーします。

  * Windows: `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac: `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. ホワイトリスト プラグインを追加します。 これは、Graph API を呼び出す際に必要になります。

        cordova plugin add cordova-plugin-whitelist

5. サポートが必要なすべてのプラットフォームを追加します。 実稼働するサンプルにするために、少なくとも次の&1; つのコマンドを実行する必要があります。 iOS を Windows 上で、または Windows を Mac 上でエミュレートすることはできないことに注意してください。

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Cordova プラグイン用の ADAL をプロジェクトに追加します。

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>ステップ 4. ユーザーを認証して Azure AD からトークンを取得するコードを追加する
このチュートリアルで開発しているアプリケーションでは、単純なディレクトリ検索機能を提供します。 ユーザーは、ディレクトリに任意のユーザーのエイリアスを入力して、いくつかの基本的な属性を表示することができます。 スタート プロジェクトには、アプリケーションの基本的なユーザー インターフェイスの定義が (www/index.html に) 含まれています。さらに基本的なアプリケーションのイベント サイクル、ユーザー インターフェイスのバインド、結果表示ロジックを関連付けるスキャフォールディングが (www/js/index.js に) 含まれています。 残されている作業は、ID タスクを実装するロジックを追加することだけです。

最初に、アプリを識別するために Azure AD が使用するプロトコル値と対象のリソースをコードに設定する必要があります。 これらの値は、後でトークン要求を作成するために使用されます。 index.js ファイルの最上部に、次のスニペットを挿入します。

```javascript
var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` と `clientId` の値は、Azure AD でアプリを記述する値と一致している必要があります。 これらの値は、このチュートリアルの手順 1 で説明しているように、Azure Portal の **[構成]** タブに表示されます。

> [!NOTE]
> 独自のテナントに新しいアプリを登録しないことにした場合は、構成済みの値をそのまま単純に貼り付けることができます。 このサンプルが実行されるのを確認できます。ただし、運用環境のために用意したアプリの場合は独自のエントリを常に作成する必要があります。

次に、トークン要求コードを追加します。 `search` と `renderData` の定義の間に次のスニペットを挿入します。

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
この関数を、2 つの主要な部分に分解することによって調べてみましょう。
このサンプルは、特定の&1; テナントに関連付けるのではなく、どのテナントでも機能するように設計されています。 これは "/common" エンドポイントを使用します。これによってユーザーは、認証時に任意のアカウントを入力することができ、認証要求は、そのアカウントが所属しているテナントに渡されます。

メソッドの最初の部分で、トークンが ADAL キャッシュ内に既に格納されているかどうかを検査します。 格納されていた場合、このメソッドでは、トークンを取得したテナントを使用して ADAL の再初期化が行われます。 これは追加のプロンプトを回避するために必要です。"/common" を使用すると、必ずユーザーに新しいアカウントの入力が求められるからです。

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
メソッドの&2; 番目の部分では、適切なトークン要求を実行します。 `acquireTokenSilentAsync` メソッドは、ADAL に、UX を表示せずに指定されたリソースのトークンを返すように要求します。 これが起きる可能性があるのは、キャッシュに既に適切なアクセス トークンが保管されているか、またはプロンプトを表示せずに新しいアクセス トークンを取得するために使用できる更新トークンがある場合です。 試行に失敗する場合は、`acquireTokenAsync` に戻ります。これはユーザーにはっきりと認証を求めるプロンプトを表示します。

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
トークンを取得したので、最後に Graph API を呼び出し、必要な検索クエリを実行することができます。 次のスニペットを、`authenticate` 定義の下に挿入します。

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
開始ポイント ファイルにより、テキスト ボックスにユーザーのエイリアスを入力するためのサンプル UX が提供されています。 このメソッドではその値を使用して、クエリを作成し、アクセス トークンと組み合わせ、Microsoft Graph に送信し、結果を解析します。 開始ポイント ファイル内に事前に存在している `renderData` メソッドによって、結果を表示する処理が行われます。

## <a name="step-5-run-the-app"></a>ステップ 5. アプリを実行する
これでアプリの実行準備が完了しました。 操作は非常に簡単です。アプリが起動したら、表示したいユーザーのエイリアスを入力して、ボタンをクリックするだけです。 認証情報が求められます。 認証と検索に成功すると、検索対象のユーザーの属性が表示されます。

その後の実行では、前に取得したトークンがキャッシュ内に存在しているので、プロンプトが表示されずに検索が行われます。

アプリを実行するための具体的な手順は、プラットフォームごとに異なります。

### <a name="windows-10"></a>Windows 10
   タブレット/PC: `cordova run windows --archs=x64 -- --appx=uap`

   モバイル (PC に接続されている Windows 10 モバイル デバイスが必要): `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > 最初の実行時に、開発者用ライセンスでサインインするように求められることがあります。 詳細については、[開発者用ライセンス](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)に関するページを参照してください。

### <a name="windows-81-tabletpc"></a>Windows 8.1 タブレット/PC
   `cordova run windows`

   > [!NOTE]
   > 最初の実行時に、開発者用ライセンスでサインインするように求められることがあります。 詳細については、[開発者用ライセンス](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)に関するページを参照してください。

### <a name="windows-phone-81"></a>Windows Phone 8.1
   コネクテッド デバイスで実行するには、`cordova run windows --device -- --phone` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate windows -- --phone` を実行します。

   `cordova run windows --list -- --phone` を使用すると、選択可能なすべての対象を表示でき、`cordova run windows --target=<target_name> -- --phone` を使用すると、特定のデバイスまたはエミュレーターでアプリケーションを実行できます (たとえば、`cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`)。

### <a name="android"></a>Android
   コネクテッド デバイスで実行するには、`cordova run android --device` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate android` を実行します。

   前述の「前提条件」セクションの説明に従って、AVD Manager を使用してエミュレーター インスタンスを作成したことを確認します。

   `cordova run android --list` を使用すると、選択可能なすべての対象を表示でき、`cordova run android --target=<target_name>` を使用すると、特定のデバイスまたはエミュレーターでアプリケーションを実行できます (たとえば、`cordova run android --target="Nexus4_emulator"`)。

### <a name="ios"></a>iOS
   コネクテッド デバイスで実行するには、`cordova run ios --device` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate ios` を実行します。

   > [!NOTE]
   > エミュレーターで実行する `ios-sim` パッケージがインストールされていることを確認してください。 詳細については、「前提条件」セクションを参照してください。

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>次のステップ
完全なサンプル (構成値を除く) については、[GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient) で入手できます。

ここからは上級のさらに興味深いシナリオに移動することができます。 「[Azure AD による Node.js Web API のセキュリティ保護](active-directory-devquickstarts-webapi-nodejs.md)」も試してみてください。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]



<!--HONumber=Feb17_HO2-->


