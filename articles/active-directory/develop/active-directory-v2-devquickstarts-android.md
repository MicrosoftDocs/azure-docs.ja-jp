---
title: "Azure Active Directory v2.0 Android アプリ | Microsoft Docs"
description: "サインインに個人の Microsoft アカウントと会社/学校アカウントの両方を使用し、サード パーティのライブラリを使用して Graph API を呼び出す Android アプリの構築方法を説明します。"
services: active-directory
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: 2dfe58c032888ca34e905f4e549fcc4d6e36d324


---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>v2.0 エンドポイントを使用した Graph API を使って、サード パーティのライブラリを使用している Android アプリへのサインインを追加する
Microsoft の ID プラットフォームには、OAuth2 や OpenID Connect といったオープンな標準が使用されています。 開発者は、任意のライブラリを使用して Microsoft のサービスと連携させることができます。 Microsoft では、そのプラットフォームを他のライブラリから使用する開発者のために、サード パーティのライブラリから Microsoft の ID プラットフォームに接続するための構成方法を紹介するチュートリアルを作成しています。この記事もそうしたチュートリアルの一つです。 Microsoft の ID プラットフォームには、[RFC6749 OAuth2 仕様](https://tools.ietf.org/html/rfc6749)を実装するほとんどのライブラリから接続できます。

このチュートリアルで作成するアプリケーションで、ユーザーは所属組織にサインインした後、Graph API を使用して組織内のユーザー自身を検索できます。

ここで紹介する構成サンプルは OAuth2 や OpenID Connect に精通している読者を想定しており、それ以外の方にとっては、あまり参考にならない可能性があります。 背景については、[2.0 プロトコル - OAuth 2.0 承認コード フロー](active-directory-v2-protocols-oauth-code.md)に関するページに目を通すことをお勧めします。

> [!NOTE]
> Microsoft のプラットフォーム機能のうち、OAuth2 や OpenID Connect の標準に含まれている式が使われているいくつかの機能 (条件付きアクセスや Intune ポリシー管理) については、オープン ソースの Microsoft Azure Identity Libraries を使用する必要があります。
> 
> 

Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。

> [!NOTE]
> v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
> 
> 

## <a name="download-the-code-from-github"></a>GitHub からコードをダウンロードする
このチュートリアルのコードは、 [GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2)で管理されています。  追加の参考資料として、 [アプリのスケルトン (.zip) をダウンロード](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) したり、スケルトンを複製したりすることができます:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

単にサンプルをダウンロードして作業してもかまいません。

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>アプリを登録します
[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)で新しいアプリを作成するか、「[v2.0 エンドポイントを使用してアプリケーションを登録する方法](active-directory-v2-app-registration.md)」の詳細な手順に従ってください。  次のことを確認します。

* アプリに割り当てられた**アプリケーション ID** をコピーしておきます。これは後で必要になります。
* アプリ用の **モバイル** プラットフォームを追加します。

> 注: アプリケーション登録ポータルで **リダイレクト URI** の値を取得できます。 ただし、今回の例では既定の値 ( `https://login.microsoftonline.com/common/oauth2/nativeclient`) を使用する必要があります。
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>NXOAuth2 サード パーティ製ライブラリのダウンロードとワークスペースの作成
このチュートリアルでは、Google の OpenID Connect コードに基づく OAuth2 ライブラリ OIDCAndroidLib を GitHub からクローンして使用します。 このライブラリは、ネイティブ アプリケーション プロファイルを実装し、ユーザーの承認エンドポイントをサポートしています。 これらの条件さえ整えば、Microsoft の ID プラットフォームと連携させることができます。

OIDCAndroidLib リポジトリをローカル コンピューターにクローンします。

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Android Studio 環境の設定
1. 新しい Android Studio プロジェクトを作成し、ウィザードの既定をそのまま使用します。
   
    ![Android Studio で新しいプロジェクトを作成する](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![ターゲットの Android デバイス](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![モバイルにアクティビティを追加する](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. プロジェクト モジュールをセットアップするには、クローンしたリポジトリをプロジェクトの場所に移動します。 プロジェクトを作成してから直接プロジェクトの場所にクローンしてもかまいません。
   
    ![プロジェクト モジュール](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. コンテキスト メニューを使用するか、Ctrl + Alt + Maj + S ショートカットを使用して、プロジェクト モジュールの設定を開きます。
   
    ![プロジェクト モジュールの設定](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. 既定のアプリ モジュールを削除します。必要なのはプロジェクト コンテナーの設定だけです。
   
    ![既定のアプリ モジュール](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. クローンしたリポジトリのモジュールを現在のプロジェクトにインポートします。
   
    ![gradle プロジェクトのインポート](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG)
    ![新しいモジュール ページの作成](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. 以上の手順を `oidlib-sample` モジュールについても行います。
7. oidclib の `oidlib-sample` モジュールへの依存関係を確認します。
   
    ![oidclib の oidlib-sample モジュールへの依存関係](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. **[OK]** をクリックして gradle sync の完了を待ちます。
   
    この時点での settings.gradle の内容は次のとおりです。
   
    ![settings.gradle のスクリーン ショット](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. サンプル アプリをビルドして、サンプルが正しく実行されていることを確認します。
   
    これをそのまま Azure Active Directory で使用することはできません。 先にエンドポイントの構成を行う必要があります。 こうすることで Android Studio の問題を確実に回避したうえで、サンプル アプリのカスタマイズに着手することができます。
10. Android Studio で `oidlib-sample` をターゲットとしてビルド、実行します。
    
    ![oidlib-sample ビルドの進行状況](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. プロジェクトからモジュールを削除したときに残った `app ` ディレクトリを削除します。これは、Android Studio では安全のために残されます。
    
    ![アプリ ディレクトリを含むファイル構造](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. **[Edit Configurations (構成の編集)]** メニューを開いて、プロジェクトからモジュールを削除するときに残った実行構成を削除してください。
    
    ![[構成の編集] メニュー](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![アプリの実行構成](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>サンプルのエンドポイントの構成
`oidlib-sample` が正常に実行されることを確認したら、いくつかのエンドポイントを編集して、Azure Active Directory と連携させましょう。

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>oidc_clientconf.xml ファイルを編集してクライアントを構成する
1. OAuth2 のフローのみを使用してトークンを取得し、Graph API を呼び出すため、クライアントは OAuth2 のみに設定します。 OIDC の例については、また別の機会に紹介します。
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. 登録ポータルから取得したクライアント ID を構成します。
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. 次のいずれかでリダイレクト URI を構成します。
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Graph API にアクセスするために必要なスコープを構成します。
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

`oidc_scopes` 内の `User.Read` 値によって、サインインしているユーザーの基本プロファイルを読み取ることができます。
使用可能なすべてのスコープの詳細については、「 [Microsoft Graph のアクセス許可スコープ](https://graph.microsoft.io/docs/authorization/permission_scopes)」を参照してください。

OpenID Connect のスコープとしての `openid` または `offline_access` について詳しい解説が必要な場合は、[2.0 プロトコル - OAuth 2.0 承認コード フロー](active-directory-v2-protocols-oauth-code.md)に関するページを参照してください。

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>oidc_endpoints.xml ファイルを編集して、クライアント エンドポイントを構成する
* `oidc_endpoints.xml` ファイルを開いて次の変更を加えます。
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

プロトコルとして OAuth2 を使用している場合、これらのエンドポイントは決して変わりません。

> [!NOTE]
> `userInfoEndpoint` と `revocationEndpoint` のエンドポイントは、Azure Active Directory では現在サポートされていません。 既定の example.com 値のままにしている場合、サンプルで使用可能でないことが通知されます。
> 
> 

## <a name="configure-a-graph-api-call"></a>Graph API 呼び出しの構成
* `HomeActivity.java` ファイルを開いて次の変更を加えます。
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

これは、単純な Graph API 呼び出しによって情報を取得する例です。

必要な変更は、これですべて完了です。 `oidlib-sample` アプリケーションを実行し、 **[サインイン]**をクリックします。

本人確認に成功したら、 **[Request Protected Resource (保護されたリソースの要求)]** ボタンを選択して、Graph API 呼び出しをテストしてください。

## <a name="get-security-updates-for-our-product"></a>Microsoft 製品のセキュリティ更新プログラムの取得
セキュリティの問題に関する通知を受け取ることをお勧めします。[Security TechCenter](https://technet.microsoft.com/security/dd252948) にアクセスし、セキュリティ アドバイザリ通知の受信登録を行ってください。




<!--HONumber=Jan17_HO3-->


