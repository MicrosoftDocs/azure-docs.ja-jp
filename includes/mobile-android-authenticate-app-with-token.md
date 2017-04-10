
前の例では、標準のサインインを示しました。標準のサインインでは、アプリケーションが開始されるたびに、クライアントは ID プロバイダーとバックエンド Azure サービスの両方にアクセスする必要があります。 この方法は非効率であり、多くの顧客が同時にアプリケーションを開始しようとした場合に使用率に関連する問題が発生する場合があります。 よって、Azure サービスから返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用する方が効果的です。

> [!NOTE]
> クライアントによって管理される認証とサービスによって管理される認証のどちらを使用する場合でも、バックエンド Azure サービスが発行したトークンをキャッシュできます。 このチュートリアルでは、サービスによって管理される認証を使用します。
>
>

1. ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;
2. 次のメンバーをクラス `ToDoActivity` クラスに追加します。

        public static final String SHAREDPREFFILE = "temp";    
        public static final String USERIDPREF = "uid";    
        public static final String TOKENPREF = "tkn";    
3. ToDoActivity.java ファイル内で、次の `cacheUserToken` メソッドの定義を追加します。

        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }    

    このメソッドでは、プライベートとマークされた設定ファイルに、ユーザー ID とトークンを格納します。 これにより、キャッシュへのアクセスが保護され、デバイスの他のアプリケーションはトークンにアクセスできなくなります。 設定は、アプリケーション用にサンドボックス化されます。 ただし、他のユーザーは、デバイスにアクセスできるようになると、他の手段によってトークン キャッシュにアクセスできる可能性があります。

   > [!NOTE]
   > トークンによるデータへのアクセスの秘密性が高く、他のユーザーがそのデバイスにアクセスする可能性がある場合は、暗号化によりトークンの保護を強化できます。 ただし、完全に安全なソリューションはこのチュートリアルの範囲を超えており、またセキュリティの要件によって異なります。
   >
   >
4. ToDoActivity.java ファイル内で、次の `loadUserTokenCache` メソッドの定義を追加します。

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null);
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null);
            if (token == null)
                return false;

            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);

            return true;
        }
5. *ToDoActivity.java* ファイルで、`authenticate` メソッドを、トークン キャッシュを使用する次のメソッドで置き換えます。 Google 以外のアカウントを使用する場合は、ログイン プロバイダーを変更します。

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();    
                    }
                });
            }
        }
6. アプリケーションをビルドし、有効なアカウントを使用して認証をテストします。 最低&2; 回アプリケーションを実行します。 最初の実行で、サインインとトークン キャッシュの作成を求めるプロンプトが表示されます。 それ以降、実行ごとに、認証のためのトークン キャッシュの読み込みが試行されます。 サインインする必要がなくなります。


<!--HONumber=Dec16_HO2-->


