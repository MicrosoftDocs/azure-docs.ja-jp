
1. MainPage.cs 共有プロジェクト ファイルを開き、次のコード スニペットを MainPage クラスに追加します。
   
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
2. 既存の **OnNavigatedTo** メソッドのオーバーライドで、**RefreshTodoItems** メソッドの呼び出しをコメントアウトするか、削除します。
   
    これを行うと、ユーザーが認証されるまでデータが読み込まれなくなります。 次は、認証をトリガーするアプリに **サインイン** ボタンを追加します。
3. MainPage クラスに、次のコード スニペットを追加します。
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Windows ストア アプリ プロジェクトで、MainPage.xaml プロジェクト ファイルを開き、**[保存]** ボタンを定義する要素の直前に次の **Button** 要素を追加します。
   
        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
5. Windows Phone ストア アプリ プロジェクトで、**ContentPanel** 内の **TextBox** 要素の後に、次の **Button** 要素を追加します。
   
        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>
6. 共有の App.xaml.cs プロジェクト ファイルを開き、次のコードを追加します。
   
        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif
   
            base.OnActivated(args);
        }
   
    **OnActivated** メソッドが既に存在する場合は、単に `#if...#endif` コード ブロックを追加します。
7. F5 キーを押して Windows ストア アプリを実行します。**[サインイン]** ボタンをクリックして、選択した ID プロバイダーでアプリにサインインします。 
   
       When you are successfully logged-in, the app should run without errors, and you should be able to query your backend and make updates to data.
8. Windows Phone ストア アプリ プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]**をクリックしてから、Windows Phone ストア アプリも正常に実行していることを確認する前の手順を繰り返します。  



<!--HONumber=Jan17_HO3-->


