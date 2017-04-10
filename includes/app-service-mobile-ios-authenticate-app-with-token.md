
前の例は、アプリが起動するたびに ID プロバイダーとモバイル サービスの両方と通信します。 代わりに認証トークンをキャッシュし、先に認証トークンの使用を試みる方法もあります。

* iOS クライアントで認証トークンを暗号化し、格納する場合は、iOS Keychain の使用をお勧めします。 このチュートリアルでは、 [SSKeychain](https://github.com/soffes/sskeychain) （iOS Keychain の単純なラッパー） を使用します。 SSKeychain ページの指示に従って、それをプロジェクトに追加します。 プロジェクトの **[ビルド設定]** で、**[Enable Modules (モジュールを有効にする)]** 設定が有効になっていることを確認します (**[Apple LLVM - Languages - Modules (Apple LLVM - 言語 - モジュール)]** セクション)。
* **QSTodoListViewController.m** を開き、次のコードを追加します。

```
        - (void) saveAuthInfo {
                [SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
        }


        - (void)loadAuthInfo {
                NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
            if (userid) {
                NSLog(@"userid: %@", userid);
                self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
                 self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

            }
        }
```

* `loginAndGetData` で、`loginWithProvider:controller:animated:completion:` の完了ブロックを変更します。 `[self refresh]` の直前に次の行を追加し、ユーザー ID とトークンのプロパティを格納します。

```
                [self saveAuthInfo];
```

* アプリの起動時にユーザー ID とトークンを読み込むようにします。 **QSTodoListViewController.m** の `viewDidLoad` で、`self.todoService` が初期化された直後に、次の行を追加します。

```
                [self loadAuthInfo];
```


<!--HONumber=Jan17_HO3-->


