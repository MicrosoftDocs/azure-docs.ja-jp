---
title: ユニバーサル Windows プラットフォーム (UWP) アプリに認証を追加する | Microsoft Docs
description: Azure App Service Mobile Apps を使用して、ユニバーサル Windows プラットフォーム (UWP) アプリのユーザーを認証する方法を説明します。その際に、さまざまな ID プロバイダーを使います。AAD、Google、Facebook、Twitter、Microsoft などです。
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9a3a6a8d063836094c1285a5ab4c17ee403fc347
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440231"
---
# <a name="add-authentication-to-your-windows-app"></a>Windows アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーとかかわることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-users) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このトピックでは、モバイル アプリにクラウド ベースの認証を追加する方法を説明します。 このチュートリアルでは、Azure App Service でサポートされている ID プロバイダーを使用して、Mobile Apps のユニバーサル Windows プラットフォーム (UWP) のクイック スタート プロジェクトに認証を追加します。 Mobile Apps のバックエンドによって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、Mobile Apps のクイック スタートに基づいています。 事前に [Mobile Apps の使用](app-service-mobile-windows-store-dotnet-get-started.md)に関するチュートリアルを完了している必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、App Service を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>許可されている外部リダイレクト URL にアプリを追加する

認証をセキュリティで保護するには、アプリ用の新しい URL スキームの定義が必要になります。 これによって、認証プロセスが完了すると認証システムからアプリにリダイレクトできます。 このチュートリアル全体を通して、URL スキーム _appname_ を使用します。 ただし、選択したあらゆる URL スキームを使用できます。 URL スキームは、モバイル アプリに対して一意である必要があります。 サーバー側でリダイレクトを有効にするには、以下の手順に従います。

1. [Azure Portal](https://ms.portal.azure.com) で、App Service を選択します。

2. **[認証/承認]** メニュー オプションをクリックします。

3. **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** に `url_scheme_of_your_app://easyauth.callback` を入力します。  この文字列の **url_scheme_of_your_app** は、モバイル アプリケーションの URL スキームです。  プロトコルの通常の URL 仕様 (文字と数字のみを使用し、文字で始まる) に従う必要があります。  数か所で URL スキームに合わせてモバイル アプリケーション コードを調整する必要があるため、選択した文字列をメモしておく必要があります。

4. **[Save]** をクリックします。

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

これで、バックエンドへの匿名アクセスが無効になっていることを確認できます。 スタートアップ プロジェクトとして設定された UWP アプリ プロジェクトを使用し、アプリをデプロイして実行します。アプリケーションの開始後、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 この問題は、認証されていないユーザーとしてアプリケーションがモバイル アプリ コードにアクセスしようとしても、現在の *TodoItem* テーブルでは認証が要求されるために発生します。

次に、App Service のリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する
1. UWP アプリ プロジェクトの MainPage.xaml.cs ファイルを開き、次のコード スニペットを追加します。
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    このコードでは、Facebook ログインを使用してユーザーを認証します。 Facebook 以外の ID プロバイダーを使用している場合は、上の **MobileServiceAuthenticationProvider** の値をプロバイダーに対応する値に変更してください。
2. MainPage.xaml.cs の **OnNavigatedTo()** メソッドを置き換えます。 次は、認証をトリガーするアプリに **サインイン** ボタンを追加します。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. MainPage.xaml.cs に、次のコード スニペットを追加します。
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. MainPage.xaml プロジェクト ファイルを開き、 **[保存]** ボタンを定義している要素を探して、次のコードに置き換えます。
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. App.xaml.cs に、次のコード スニペットを追加します。

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Package.appxmanifest ファイルを開き、 **[宣言]** に移動して、 **[使用可能な宣言]** ドロップダウン リストで **[プロトコル]** を選択し、 **[追加]** ボタンをクリックします。 次に、 **[プロトコル]** 宣言の **[プロパティ]** を構成します。 **[表示名]** で、アプリケーションのユーザーに表示する名前を追加します。 **[名前]** に、自分の {url_scheme_of_your_app} を追加します。
7. F5 キーを押してアプリを実行します。 **[サインイン]** ボタンをクリックして、選択した ID プロバイダーでアプリにサインインします。 サインインに成功すると、アプリはエラーなしで実行し、バックエンドに対してクエリを行ってデータを更新できるようになります。

## <a name="tokens"></a>クライアント側で認証トークンを保存する
前の例では、標準のサインインを示しました。標準のサインインでは、アプリケーションが開始するたびに、クライアントは ID プロバイダーと App Service の両方にアクセスする必要があります。 この方法は非効率であるだけでなく、多くの顧客が同時にアプリケーションを開始すると、使用率に関連した問題が発生する場合があります。 よって、App Service から返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用するほうが効果的です。

> [!NOTE]
> クライアントによって管理される認証とサービスによって管理される認証のどちらを使用する場合でも、App Services が発行したトークンをキャッシュできます。 このチュートリアルでは、サービスによって管理される認証を使用します。
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>次の手順
これで基本的な認証チュートリアルは完了しましたので、引き続き次のいずれかのチュートリアルのご利用を検討してください。

* [アプリへのプッシュ通知の追加](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するようにモバイル アプリ バックエンドを構成する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  モバイル アプリ バックエンドを使用して、オフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
