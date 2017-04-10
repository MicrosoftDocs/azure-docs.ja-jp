---
title: "Xamarin Android での Mobile Apps の認証の使用"
description: "Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin Android アプリのユーザーを認証する方法について説明します。"
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4c0ba82ca010f1ee571424fa3d650718e5acdd8b
ms.lasthandoff: 11/17/2016


---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Xamarin.Android アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

このトピックでは、クライアント アプリケーションから Mobile App のユーザーを認証する方法について説明します。 このチュートリアルでは、Azure Mobile Apps でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。 Mobile App によって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル アプリのクイック スタートに基づいています。 また、先に [Xamarin.Android アプリの作成]に関するチュートリアルを完了している必要があります。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="register"></a>アプリケーションを認証に登録し、App Services を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターを使用してクライアント プロジェクトを実行します。 アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 これは、認証されないユーザーとして Mobile App バックエンドにアプリがアクセスしようとするために生じます。 *TodoItem* テーブルは今すぐ認証が必要です。

次に、認証されたユーザーで Mobile App のバックエンドからリソースを要求するように、クライアント アプリを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する
ユーザーが **[サインイン]** ボタンをタップし認証されてからデータを表示する必要がある場合は、アプリを更新します。

1. **TodoActivity** クラスに次のコードを追加します。
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
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
   > Facebook 以外の ID プロバイダーを使用している場合は、上記の例の **LoginAsync** に渡される値を *MicrosoftAccount*、*Twitter*、*Google*、*WindowsAzureActiveDirectory* のいずれかに変更します。
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
5. Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターでクライアント プロジェクトを実行し、選択した ID プロバイダーを使用してサインインします。
   
       When you are successfully logged-in, the app will display your login ID and the list of todo items, and you can make updates to the data.

<!-- URLs. -->
[Xamarin.Android アプリの作成]: app-service-mobile-xamarin-android-get-started.md

