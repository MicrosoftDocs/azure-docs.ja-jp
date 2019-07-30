---
title: Xamarin iOS での Mobile Apps の認証の使用
description: Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin iOS アプリのユーザーを認証する方法について説明します。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: fa1f4bae314025a71568e1e04cbf950ebbe26dbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446238"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Xamarin.iOS アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-users) を今すぐチェックしてください。
>

## <a name="overview"></a>概要

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。 このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、Xamarin.iOS クイック スタート プロジェクトに認証を追加します。 モバイル アプリによって正常に認証され、承認されるとユーザー ID 値が表示され、制限付きのテーブル データにアクセスできます。

先に [Xamarin.iOS アプリの作成]に関するチュートリアルを完了している必要があります。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>アプリケーションを認証に登録し、App Services を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>許可されている外部リダイレクト URL にアプリを追加する

認証をセキュリティで保護するには、アプリ用の新しい URL スキームの定義が必要になります。 これによって、認証プロセスが完了すると認証システムからアプリにリダイレクトできます。 このチュートリアル全体を通して、URL スキーム _appname_ を使用します。 ただし、選択したあらゆる URL スキームを使用できます。 URL スキームは、モバイル アプリに対して一意である必要があります。 サーバー側でリダイレクトを有効にするには、以下の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で、App Service を選択します。

2. **[認証/承認]** メニュー オプションをクリックします。

3. **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** に `url_scheme_of_your_app://easyauth.callback` を入力します。  この文字列の **url_scheme_of_your_app** は、モバイル アプリケーションの URL スキームです。  プロトコルの通常の URL 仕様 (文字と数字のみを使用し、文字で始まる) に従う必要があります。  数か所で URL スキームに合わせてモバイル アプリケーション コードを調整する必要があるため、選択した文字列をメモしておく必要があります。

4. Click **OK**.

5. **[Save]** をクリックします。

## <a name="restrict-permissions-to-authenticated-users"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターを使用してクライアント プロジェクトを実行します。 アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 デバッガーのコンソールに、エラーが記録されます。 Visual Studio では、[出力] ウィンドウでエラーを確認する必要があります。

    この許可されない問題は、認証されていないユーザーとしてモバイル アプリのバックエンドにアプリがアクセスしようとするために発生します。 *TodoItem* テーブルは今すぐ認証が必要です。

次に、認証されたユーザーで Mobile App のバックエンドからリソースを要求するように、クライアント アプリを更新します。

## <a name="add-authentication-to-the-app"></a>アプリケーションに認証を追加する
ここでは、データを表示する前にログイン画面を表示するようにアプリケーションを変更します。 アプリケーションが起動したときには、App Service には接続されず、データも表示されません。 ユーザーが最初に更新操作を実行した後で、ログイン画面が表示されます。ログインに成功すると、Todo 項目の一覧が表示されます。

1. クライアント プロジェクトで **QSTodoService.cs** ファイルを開き、次の using ステートメントと、QSTodoService クラスへのアクセサーを持つ `MobileServiceUser` を追加します。

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. 次の定義を使用して、**QSTodoService** に **Authenticate** という新しいメソッドを追加します。

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Facebook 以外の ID プロバイダーを使用している場合は、上の **LoginAsync** に渡される値を_MicrosoftAccount_、_Twitter_、_Google_、または _WindowsAzureActiveDirectory_ のいずれかに変更します。

3. **QSTodoListViewController.cs**を開きます。 **ViewDidLoad** のメソッド定義を変更して、終わり近くにある **RefreshAsync()** の呼び出しを削除します。

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. **User** プロパティが null の場合は認証を行うようにメソッド **RefreshAsync** を変更します。 メソッド定義の最初に次のコードを追加します。

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. **AppDelegate.cs** を開き、次のメソッドを追加します。

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. **Info.plist** ファイルを開き、 **[詳細設定]** セクションの **[URL の種類]** に移動します。 次に、URL の種類の **[識別子]** および **[URL スキーマ]** を構成して、 **[Add URL Type]\(URL の種類の追加\)** をクリックします。 **[URL スキーマ]** は {url_scheme_of_your_app} と同じにする必要があります。
7. Mac ホストまたは Visual Studio for Mac に接続している Visual Studio で、デバイスまたはエミュレーターをターゲットとしているクライアント プロジェクトを実行します。 アプリケーションにデータが表示されないことを確認します。

    項目の一覧をプルダウンして更新操作を実行すると、ログイン画面が表示されます。 有効な資格情報を正しく入力すると、Todo 項目の一覧が表示され、データを更新できるようになります。

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS アプリの作成]: app-service-mobile-xamarin-ios-get-started.md
