---
title: Xamarin Android での Mobile Apps の認証の使用
description: Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin Android アプリのユーザーを認証する方法について説明します。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a0b2c54c2d2a9daba56ea1d05c18e72a2d7a7a0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447052"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Xamarin.Android アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started-users) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このトピックでは、クライアント アプリケーションから Mobile App のユーザーを認証する方法について説明します。 このチュートリアルでは、Azure Mobile Apps でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。 Mobile App によって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル アプリのクイック スタートに基づいています。 また、先に [Xamarin.Android アプリの作成]に関するチュートリアルを完了している必要があります。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="register"></a>アプリケーションを認証に登録し、App Services を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>許可されている外部リダイレクト URL にアプリを追加する

認証をセキュリティで保護するには、アプリ用の新しい URL スキームの定義が必要になります。 これによって、認証プロセスが完了すると認証システムからアプリにリダイレクトできます。 このチュートリアル全体を通して、URL スキーム _appname_ を使用します。 ただし、選択したあらゆる URL スキームを使用できます。 URL スキームは、モバイル アプリに対して一意である必要があります。 サーバー側でリダイレクトを有効にするには、以下の手順に従います。

1. [Azure Portal] で、App Service を選択します。

2. **[認証/承認]** メニュー オプションをクリックします。

3. **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** に `url_scheme_of_your_app://easyauth.callback` を入力します。  この文字列の **url_scheme_of_your_app** は、モバイル アプリケーションの URL スキームです。  プロトコルの通常の URL 仕様 (文字と数字のみを使用し、文字で始まる) に従う必要があります。  数か所で URL スキームに合わせてモバイル アプリケーション コードを調整する必要があるため、選択した文字列をメモしておく必要があります。

4. Click **OK**.

5. **[Save]** をクリックします。

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターを使用してクライアント プロジェクトを実行します。 アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 これは、認証されないユーザーとして Mobile App バックエンドにアプリがアクセスしようとするために生じます。 *TodoItem* テーブルは今すぐ認証が必要です。

次に、認証されたユーザーで Mobile App のバックエンドからリソースを要求するように、クライアント アプリを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する
ユーザーが **[サインイン]** ボタンをタップし認証されてからデータを表示する必要がある場合は、アプリを更新します。

1. **TodoActivity** クラスに次のコードを追加します。
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    これにより、ユーザーを認証する新しいメソッドと、新しい **[サインイン]** ボタンのメソッド ハンドラーが作成されます。 上記のサンプル コードでは、ユーザーは Facebook ログインを使用して認証されます。 認証されると、ダイアログを使用してユーザー ID が表示されます。
   
   > [!NOTE]
   > Facebook 以外の ID プロバイダーを使用している場合は、上の **LoginAsync** に渡される値を*MicrosoftAccount*、*Twitter*、*Google*、または *WindowsAzureActiveDirectory* のいずれかに変更します。
   > 
   > 
2. **OnCreate** メソッド内の次のコード行を削除またはコメント アウトします。
   
        OnRefreshItemsSelected ();
3. Activity_To_Do.axml ファイル内の既存の *AddItem* ボタンの前に次の *LoginUser* ボタン定義を追加します。
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. 次の要素を Strings.xml リソース ファイルに追加します。
   
        <string name="login_button_text">Sign in</string>
5. AndroidManifest.xml ファイルを開き、`<application>` XML 要素に次のコードを追加します。

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターでクライアント プロジェクトを実行し、選択した ID プロバイダーを使用してサインインします。 ログインに成功すると、アプリケーションにログイン ID と todo 項目の一覧が表示され、データを更新することができます。

## <a name="troubleshooting"></a>トラブルシューティング

**アプリケーションが**  でクラッシュした`Java.Lang.NoSuchMethodError: No static method startActivity`

場合によっては、Visual Studio で単なる警告として表示されるサポート パッケージの競合が実行時にこの例外としてアプリケーション クラッシュとして表示されます。 この場合、プロジェクトで参照されるすべてのサポート パッケージのバージョンが同じであることを確認する必要があります。 [Azure Mobile Apps の NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)には、Android プラットフォームに関して `Xamarin.Android.Support.CustomTabs` の依存関係があるため、プロジェクトで新しいサポート パッケージを使用する場合は、必要なバージョンを使用してこのパッケージを直接インストールし、競合を回避する必要があります。

<!-- URLs. -->
[Xamarin.Android アプリの作成]: app-service-mobile-xamarin-android-get-started.md
