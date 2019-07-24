---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804462"
---
1. MainPage.xaml.cs プロジェクト ファイルを開き、次の **using** ステートメントを追加します。
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. **AuthenticateAsync** メソッドを次のコードに置き換えます。
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    この **AuthenticateAsync** バージョンで、アプリはサービスにアクセスするために、**PasswordVault** に格納された資格情報の使用を試みます。 保存された資格情報がないときも通常のサインインが実行されます。
   
   > [!NOTE]
   > キャッシュされたトークンは有効期限が切れている場合があります。また、認証後にアプリケーションを使用している際にトークンの期限切れが発生する場合があります。 トークンの期限切れを確認する方法については、[有効期限が切れた認証トークンのチェック](https://aka.ms/jww5vp)に関するページを参照してください。 トークンの期限切れに関連する認証エラーを処理するためのソリューションについては、投稿「 [Azure Mobile Services マネージド SDK での有効期限切れトークンのキャッシュと処理](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx)」を参照してください。 
   > 
   > 
3. アプリケーションを 2 回再起動します。
   
    最初の再起動では、プロバイダーによるサインインが再度必要になります。 ただし、2 回目の再起動ではキャッシュされた資格情報が使用され、サインインは回避されます。 

