簡単なケースでは、トークン キャッシュは動作しますが、トークンが期限切れになった場合、または取り消された場合はどのようになるでしょうか。 トークンが期限切れになる可能性があるのは、アプリケーションが実行されていないときです。 つまり、このときにトークン キャッシュは無効になることがあります。 アプリケーションの実行中も、トークンが期限切れになる場合があります。 その結果、HTTP ステータス コード 401 "認証エラー" が発生します。 

期限切れのトークンは、検出して更新できるようにする必要があります。 そのためには、[Android クライアント ライブラリ](http://dl.windowsazure.com/androiddocs/)の [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html) を使用します。

このセクションでは、HTTP ステータス コード 401 の応答を検出し、トークンとトークン キャッシュの更新を開始するように ServiceFilter を定義します。 さらに、この ServiceFilter では認証中に他の送信要求をブロックして、これらの要求が更新されたトークンを使用できるようにします。

1. ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。
   
        import java.util.concurrent.atomic.AtomicBoolean;
        import java.util.concurrent.ExecutionException;
   
        import com.microsoft.windowsazure.mobileservices.MobileServiceException;
2. 次のメンバーをクラス `ToDoActivity` クラスに追加します。 
   
        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();
   
    これらは、ユーザーの認証を同期しやすくするために使用します。 認証が必要なのは、一度だけです。 認証中の呼び出しがすべて待機し、進行中の認証から新しいトークンを取得して使用する必要があります。
3. ToDoActivity.java ファイルで、次のメソッドを ToDoActivity クラスに追加します。このメソッドは、認証の進行中に他のスレッドでの送信呼び出しをブロックするために使用します。
   
        /**
         * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
         */
        public boolean detectAndWaitForAuthentication()
        {
            boolean detected = false;
            synchronized(mAuthenticationLock)
            {
                do
                {
                    if (bAuthenticating == true)
                        detected = true;
                    try
                    {
                        mAuthenticationLock.wait(1000);
                    }
                    catch(InterruptedException e)
                    {}
                }
                while(bAuthenticating == true);
            }
            if (bAuthenticating == true)
                return true;
   
            return detected;
        }
4. ToDoActivity.java ファイル内で、次のメソッドを ToDoActivity クラスに追加します。 このメソッドは、待機を開始し、認証の完了時に送信呼び出しでトークンを更新します。 

        /**
         * Waits for authentication to complete then adds or updates the token 
         * in the X-ZUMO-AUTH request header.
         * 
         * @param request
         *            The request that receives the updated token.
         */
        private void waitAndUpdateRequestToken(ServiceFilterRequest request)
        {
            MobileServiceUser user = null;
            if (detectAndWaitForAuthentication())
            {
                user = mClient.getCurrentUser();
                if (user != null)
                {
                    request.removeHeader("X-ZUMO-AUTH");
                    request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
                }
            }
        }


1. ToDoActivity.java ファイルで、ToDoActivity クラスの `authenticate` メソッドを更新し、トークンとトークン キャッシュを強制的に更新するブール型パラメーターを受け入れるようにします。 また、ブロックされたスレッドが新しいトークンを取得できるように、認証が完了したらこれらにスレッドに通知する必要があります。
   
        /**
         * Authenticates with the desired login provider. Also caches the token. 
         * 
         * If a local token cache is detected, the token cache is used instead of an actual 
         * login unless bRefresh is set to true forcing a refresh.
         * 
         * @param bRefreshCache
         *            Indicates whether to force a token refresh. 
         */
        private void authenticate(boolean bRefreshCache) {
   
            bAuthenticating = true;
   
            if (bRefreshCache || !loadUserTokenCache(mClient))
            {
                // New login using the provider and update the token cache.
                mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                        new UserAuthenticationCallback() {
                            @Override
                            public void onCompleted(MobileServiceUser user,
                                    Exception exception, ServiceFilterResponse response) {
   
                                synchronized(mAuthenticationLock)
                                {
                                    if (exception == null) {
                                        cacheUserToken(mClient.getCurrentUser());
                                        createTable();
                                    } else {
                                        createAndShowDialog(exception.getMessage(), "Login Error");
                                    }
                                    bAuthenticating = false;
                                    mAuthenticationLock.notifyAll();
                                }
                            }
                        });
            }
            else
            {
                // Other threads may be blocked waiting to be notified when 
                // authentication is complete.
                synchronized(mAuthenticationLock)
                {
                    bAuthenticating = false;
                    mAuthenticationLock.notifyAll();
                }
                createTable();
            }
        }   
2. ToDoActivity.java ファイルで、ToDoActivity クラス内に次の新しい `RefreshTokenCacheFilter` クラスのコードを追加します。
   
        /**
        * The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
         * When 401 is encountered, the filter calls the authenticate method on the 
         * UI thread. Out going requests and retries are blocked during authentication. 
         * Once authentication is complete, the token cache is updated and 
         * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
         * that request.   
         */
        private class RefreshTokenCacheFilter implements ServiceFilter {
   
            AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();                     
   
            @Override
            public ListenableFuture<ServiceFilterResponse> handleRequest(
                    final ServiceFilterRequest request, 
                    final NextServiceFilterCallback nextServiceFilterCallback
                    )
            {
                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);
   
                // Send the request down the filter chain
                // retrying up to 5 times on 401 response codes.
                ListenableFuture<ServiceFilterResponse> future = null;
                ServiceFilterResponse response = null;
                int responseCode = 401;
                for (int i = 0; (i < 5 ) && (responseCode == 401); i++)
                {
                    future = nextServiceFilterCallback.onNext(request);
                    try {
                        response = future.get();
                        responseCode = response.getStatus().getStatusCode();
                    } catch (InterruptedException e) {
                       e.printStackTrace();
                    } catch (ExecutionException e) {
                        if (e.getCause().getClass() == MobileServiceException.class)
                        {
                            MobileServiceException mEx = (MobileServiceException) e.getCause();
                            responseCode = mEx.getResponse().getStatus().getStatusCode();
                            if (responseCode == 401)
                            {
                                // Two simultaneous requests from independent threads could get HTTP status 401. 
                                // Protecting against that right here so multiple authentication requests are
                                // not setup to run on the UI thread.
                                // We only want to authenticate once. Requests should just wait and retry 
                                // with the new token.
                                if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                                                                                                      
                                {
                                    // Authenticate on UI thread
                                    runOnUiThread(new Runnable() {
                                        @Override
                                        public void run() {
                                            // Force a token refresh during authentication.
                                            authenticate(true);
                                        }
                                    });
                                }
   
                                // Wait for authentication to complete then update the token in the request. 
                                waitAndUpdateRequestToken(request);
                                mAtomicAuthenticatingFlag.set(false);                                                  
                            }
                        }
                    }
                }
                return future;
            }
        }

    このサービス フィルターでは、各応答で HTTP ステータス コード 401 "認証エラー" が発生していないかどうかを確認します。 401 が発生した場合は、新しいトークンを取得する新しいログイン要求が、UI スレッドで設定されます。 その他の呼び出しは、ログインが完了するか、5 回失敗するまでブロックされます。 新しいトークンが取得されると、401 を引き起こした要求は新しいトークンを使用して再試行されます。またブロックされた呼び出しも、新しいトークンを使用して再試行されます。 

1. ToDoActivity.java ファイルで、ToDoActivity クラス内に次の新しい `ProgressFilter` クラスのコードを追加します。
   
        /**
        * The ProgressFilter class renders a progress bar on the screen during the time the App is waiting for the response of a previous request.
        * the filter shows the progress bar on the beginning of the request, and hides it when the response arrived.
        */
        private class ProgressFilter implements ServiceFilter {
            @Override
            public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback nextServiceFilterCallback) {
   
                final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
   
                runOnUiThread(new Runnable() {
   
                    @Override
                    public void run() {
                        if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
                    }
                });
   
                ListenableFuture<ServiceFilterResponse> future = nextServiceFilterCallback.onNext(request);
   
                Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
                    @Override
                    public void onFailure(Throwable e) {
                        resultFuture.setException(e);
                    }
   
                    @Override
                    public void onSuccess(ServiceFilterResponse response) {
                        runOnUiThread(new Runnable() {
   
                            @Override
                            public void run() {
                                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.GONE);
                            }
                        });
   
                        resultFuture.set(response);
                    }
                });
   
                return resultFuture;
            }
        }
   
    このフィルターでは、要求の開始時に進行状況バーが表示され、応答が到着すると非表示になります。
2. ToDoActivity.java ファイルで、 `onCreate` メソッドを次のように更新します。
   
        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
   
            setContentView(R.layout.activity_to_do);
            mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);
   
            // Initialize the progress bar
            mProgressBar.setVisibility(ProgressBar.GONE);
   
            try {
                // Create the Mobile Service Client instance, using the provided
                // Mobile Service URL and key
                mClient = new MobileServiceClient(
                        "https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
                        "<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());
   
                // Authenticate passing false to load the current token cache if available.
                authenticate(false);
   
            } catch (MalformedURLException e) {
                createAndShowDialog(new Exception("Error creating the Mobile Service. " +
                    "Verify the URL"), "Error");
            }
        }

       In this code, `RefreshTokenCacheFilter` is used in addition to `ProgressFilter`. Also during `onCreate` we want to load the token cache. So `false` is passed in to the `authenticate` method.




<!--HONumber=Jan17_HO3-->


