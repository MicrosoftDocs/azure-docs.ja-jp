---
title: 単一および複数アカウントのパブリック クライアント アプリ | Azure
description: 単一および複数アカウントのパブリック クライアント アプリの概要。
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 5bea1753c87c11094e78f95a1bbadb02fb0b95e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752982"
---
# <a name="single-and-multiple-account-public-client-apps"></a>単一および複数アカウントのパブリック クライアント アプリ

この記事は、単一のアカウントのパブリック クライアント アプリに重点を置いて、単一および複数アカウントのパブリック クライアント アプリで使用される型を理解するための助けとなります。 

Azure Active Directory 認証ライブラリ (ADAL) によりサーバーがモデル化されます。  Microsoft Authentication Library (MSAL) では、代わりに、クライアント アプリケーションがモデル化されます。  多くの Android アプリは、パブリック クライアントと見なされます。 パブリック クライアントは、安全にシークレットを保持できないアプリです。  

`PublicClientApplication` の API サーフェイスは、一度に 1 つのアカウントしか使用できないアプリの開発エクスペリエンスを簡素化および明確にするために MSAL によって特殊化されます。 `PublicClientApplication` は `SingleAccountPublicClientApplication` および `MultipleAccountPublicClientApplication` でサブクラス化されます。  次の図に、これらのクラスの関係を示します。

![SingleAccountPublicClientApplication UML クラス図](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>単一アカウントのパブリック クライアント アプリケーション

`SingleAccountPublicClientApplication` クラスを使用すると、一度に 1 つのアカウントにだけサインインを許可する MSAL ベースのアプリを作成できます。 `SingleAccountPublicClientApplication` は、次の点が `PublicClientApplication` とは異なります。

- MSAL により、現在サインインしているアカウントが追跡されます。
  - ご使用のアプリでブローカー (Azure portal アプリの登録時の既定) を使用していて、ブローカーが存在するデバイスにインストールされている場合、アカウントがデバイスで引き続き使用可能であるかどうかが MSAL によって確認されます。
- `signIn` では、スコープの要求とは別に、アカウントを明示的にサインインすることができます。
- `acquireTokenSilent` にはアカウント パラメーターは必要ありません。  アカウントを指定し、指定したアカウントが MSAL によって追跡されている現在のアカウントと一致しない場合は、`MsalClientException` がスローされます。
- `acquireToken` では、ユーザーがアカウントを切り替えることが許可されていません。 ユーザーが別のアカウントに切り替えようとすると、例外がスローされます。
- `getCurrentAccount` では、次のものを提供する結果オブジェクトが返されます。
  - アカウントが変更されたかどうかを示すブール値。 たとえば、デバイスから削除された結果としてアカウントが変更される可能性があります。
  - 前のアカウント。 これは、アカウントがデバイスから削除されたとき、または新しいアカウントがサインインしたときに、ローカル データのクリーンアップを行う必要がある場合に便利です。
  - currentAccount。
- `signOut` によりクライアントに関連付けられているすべてのトークンがデバイスから削除されます。  

Microsoft Authenticator や Intune ポータル サイトなどの Android 認証ブローカーがデバイスにインストールされていて、アプリがそのブローカーを使用するように構成されている場合、`signOut` によってデバイスからアカウントが削除されることはありません。

## <a name="single-account-scenario"></a>単一アカウントのシナリオ

次の擬似コードは、`SingleAccountPublicClientApplication` の使用方法を示しています。

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>複数アカウントのパブリック クライアント アプリケーション

`MultipleAccountPublicClientApplication` クラスは、複数のアカウントが同時にサインインできるようにする MSAL ベースのアプリを作成するために使用されます。 これにより、次のようにアカウントを取得、追加、削除することができます。

### <a name="add-an-account"></a>[アカウントの追加]

ご使用のアプリケーションで 1 つ以上のアカウントを使用するには、`acquireToken` を 1 回以上呼び出します。  

### <a name="get-accounts"></a>アカウントの取得

- 特定のアカウントを取得するには、`getAccount` を呼び出します。
- アプリで現在認識されているアカウントの一覧を取得するには、`getAccounts` を呼び出します。

アプリで、ブローカー アプリに認識されているデバイス上のすべての Microsoft ID プラットフォーム アカウントを列挙することはできません。 列挙できるのは、アプリで使用されているアカウントだけです。  デバイスから削除されたアカウントは、これらの関数で返されることはありません。

### <a name="remove-an-account"></a>アカウントの削除

アカウントを削除するには、アカウント識別子を使用して `removeAccount` を呼び出します。

ブローカーを使用するようにアプリが構成されていて、ブローカーがデバイスにインストールされている場合、`removeAccount` を呼び出したときにそのアカウントがブローカーから削除されることはありません。  クライアントに関連付けられているトークンのみが削除されます。

## <a name="multiple-account-scenario"></a>複数アカウントのシナリオ

次の擬似コードは、複数アカウントのアプリを作成し、そのデバイス上のアカウントを一覧表示し、トークンを取得する方法を示しています。

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
