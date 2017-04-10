---
title: "ユニバーサル Windows プラットフォーム (UWP) アプリに認証を追加する | Microsoft Docs"
description: "Azure App Service Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを使ってユニバーサル Windows プラットフォーム (UWP) アプリのユーザーを認証する方法を説明します。"
services: app-service\mobile
documentationcenter: windows
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 96b87d4d6cc1adbc9700102ffd4a989451676d81
ms.lasthandoff: 03/09/2017


---
# <a name="add-authentication-to-your-windows-app"></a>Windows アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

このトピックでは、モバイル アプリにクラウド ベースの認証を追加する方法を説明します。 このチュートリアルでは、Azure App Service でサポートされている ID プロバイダーを使用して、Mobile Apps のユニバーサル Windows プラットフォーム (UWP) のクイック スタート プロジェクトに認証を追加します。 Mobile Apps のバックエンドによって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、Mobile Apps のクイック スタートに基づいています。 事前に [Mobile Apps の使用](app-service-mobile-windows-store-dotnet-get-started.md)に関するチュートリアルを完了している必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、App Service を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

これで、バックエンドへの匿名アクセスが無効になっていることを確認できます。 スタートアップ プロジェクトとして設定された UWP アプリ プロジェクトを使用し、アプリをデプロイして実行します。アプリケーションの開始後、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 この問題は、認証されていないユーザーとしてアプリケーションがモバイル アプリ コードにアクセスしようとしても、現在の *TodoItem* テーブルでは認証が要求されるために発生します。

次に、App Service のリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する
1. UWP アプリ プロジェクトの MainPage.cs ファイルを開き、次のコード スニペットを MainPage クラスに追加します。
   
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
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
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
2. 既存の **OnNavigatedTo** メソッドのオーバーライドで、**ButtonRefresh_Click** メソッド (または **InitLocalStoreAsync** メソッド) の呼び出しをコメントにするか、削除します。 これを行うと、ユーザーが認証されるまでデータが読み込まれなくなります。 次は、認証をトリガーするアプリに **サインイン** ボタンを追加します。
3. MainPage クラスに、次のコード スニペットを追加します。
   
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
5. F5 キーを押してアプリを実行します。**[サインイン]** ボタンをクリックして、選択した ID プロバイダーでアプリにサインインします。 サインインに成功すると、アプリはエラーなしで実行し、バックエンドに対してクエリを行ってデータを更新できるようになります。

## <a name="tokens"></a>クライアント側で認証トークンを保存する
前の例では、標準のサインインを示しました。標準のサインインでは、アプリケーションが開始するたびに、クライアントは ID プロバイダーと App Service の両方にアクセスする必要があります。 この方法は非効率であるだけでなく、多くの顧客が同時にアプリケーションを開始すると、使用率に関連した問題が発生する場合があります。 よって、App Service から返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用するほうが効果的です。

> [!NOTE]
> クライアントによって管理される認証とサービスによって管理される認証のどちらを使用する場合でも、App Services が発行したトークンをキャッシュできます。 このチュートリアルでは、サービスによって管理される認証を使用します。
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>次のステップ
これで基本的な認証チュートリアルは完了しましたので、引き続き次のいずれかのチュートリアルのご利用を検討してください。

* [アプリへのプッシュ通知の追加](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するようにモバイル アプリ バックエンドを構成する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  モバイル アプリ バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md


