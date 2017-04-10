---
title: "Xamarin iOS での Mobile Apps の認証の使用"
description: "Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin iOS アプリのユーザーを認証する方法について説明します。"
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: cdc8d62c0adb81330353b73be8a0a9db8cef0052
ms.lasthandoff: 03/01/2017


---
# <a name="add-authentication-to-your-xamarinios-app"></a>Xamarin.iOS アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。 このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、Xamarin.iOS クイック スタート プロジェクトに認証を追加します。 モバイル アプリによって正常に認証され、承認されるとユーザー ID 値が表示され、制限付きのテーブル データにアクセスできます。

先に [Xamarin.iOS アプリの作成]に関するチュートリアルを完了している必要があります。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>アプリケーションを認証に登録し、App Services を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="restrict-permissions-to-authenticated-users"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4.Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターを使用してクライアント プロジェクトを実行します。 アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 デバッガーのコンソールに、エラーが記録されます。 Visual Studio では、[出力] ウィンドウでエラーを確認する必要があります。

&nbsp;&nbsp;この許可されない問題は、アプリが認証されていないユーザーとしてモバイル アプリのバックエンドにアクセスしようとするために発生します。 *TodoItem* テーブルは認証が必要になっています。

次に、認証されたユーザーで Mobile App のバックエンドからリソースを要求するように、クライアント アプリを更新します。

## <a name="add-authentication-to-the-app"></a>アプリケーションに認証を追加する
ここでは、データを表示する前にログイン画面を表示するようにアプリケーションを変更します。 アプリケーションが起動したときには、App Service には接続されず、データも表示されません。 ユーザーが最初に更新操作を実行した後で、ログイン画面が表示されます。ログインに成功すると、Todo 項目の一覧が表示されます。

1. クライアント プロジェクトで **QSTodoService.cs** ファイルを開き、次の using ステートメントと、QSTodoService クラスへのアクセサーを持つ `MobileServiceUser` を追加します。
   
    ```
        using UIKit;
    ```
   
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. 次の定義を使用して、**QSTodoService** に **Authenticate** という新しいメソッドを追加します。

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Facebook 以外の ID プロバイダーを使用している場合は、上の例の **LoginAsync** に渡される値を _MicrosoftAccount_、_Twitter_、_Google_、_WindowsAzureActiveDirectory_ のいずれかに変更します。

1. **QSTodoListViewController.cs**を開きます。 **ViewDidLoad** のメソッド定義を変更して、終わり近くにある **RefreshAsync()** の呼び出しを削除します。
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();
   
           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }
2. **User** プロパティが null の場合は認証を行うようにメソッド **RefreshAsync** を変更します。 メソッド定義の最初に次のコードを追加します。
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
3. Mac の Xamarin Build Host に接続しているVisual Studio または Xamarin Studio で、デバイスまたはエミュレーターをターゲットとしているクライアント プロジェクトを実行します。 アプリケーションにデータが表示されないことを確認します。
   
    項目の一覧をプルダウンして更新操作を実行すると、ログイン画面が表示されます。 有効な資格情報を正しく入力すると、Todo 項目の一覧が表示され、データを更新できるようになります。

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS アプリの作成]: app-service-mobile-xamarin-ios-get-started.md

