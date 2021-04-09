---
title: ネイティブ クライアント アプリの発行 - Azure AD | Microsoft Docs
description: ネイティブ クライアント アプリケーションが Azure AD Application Proxy Connector と通信して、オンプレミス アプリケーションに対して安全なリモート アクセスを提供する方法について説明します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e830b52accefa0bcb76cc3434cae2fe94eed97db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258664"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする方法

Azure Active Directory (Azure AD) アプリケーション プロキシは、Web アプリを発行するために使用できますが、Microsoft Authentication Library (MSAL) で構成されたネイティブ クライアント アプリケーションの発行にも使うことができます。 ネイティブ クライアント アプリケーションはデバイスにインストールされる点が Web アプリとは異なり、Web アプリはブラウザーからアクセスされます。

ネイティブ クライアント アプリケーションをサポートするには、Azure AD によって発行されてヘッダーで送信されたトークンを、アプリケーション プロキシで受け入れます。 アプリケーション プロキシ サービスでは、ユーザーに対する認証が行われます。 このソリューションでは、認証のためにアプリケーションのトークンは使われません。

![エンド ユーザー、Azure AD、発行済みアプリケーション間の関係](./media/application-proxy-configure-native-client-application/richclientflow.png)

ネイティブ アプリケーションを発行するには、認証の処理が行われ、多数のクライアント環境がサポートされている、Microsoft Authentication Library を使います。 アプリケーション プロキシは、[サインイン済みのユーザーに代わって Web API を呼び出すデスクトップ アプリ](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user)のシナリオに当てはまります。

この記事では、アプリケーション プロキシと Azure AD Authentication Library を使用してネイティブ アプリケーションを発行するための 4 つの手順を説明します。

## <a name="step-1-publish-your-proxy-application"></a>手順 1:プロキシ アプリケーションを発行する

他のアプリケーションと同様にプロキシ アプリケーションを発行し、アプリケーションにアクセスするユーザーを割り当てます。 詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md)」を参照してください。

## <a name="step-2-register-your-native-application"></a>手順 2:ネイティブ アプリケーションを登録する

ここでは、次のように、Azure AD でアプリケーションを登録する必要があります。

1. [Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。 **Azure Active Directory 管理センター** の **ダッシュボード** が表示されます。
1. サイドバーで **[Azure Active Directory]** を選択します。 **[Azure Active Directory]** 概要ページが表示されます。
1. Azure AD の概要のサイドバーで、 **[アプリの登録]** を選択します。 すべてのアプリ登録の一覧が表示されます。
1. **[新規登録]** を選択します。 **[アプリケーションの登録]** ページが表示されます。

   ![Azure portal でアプリ登録を新規作成します。](./media/application-proxy-configure-native-client-application/create.png)

1. **[名前]** という見出しで、ユーザーに表示されるアプリケーションの表示名を指定します。
1. **[サポートされているアカウントの種類]** という見出しで、次のガイドラインを使ってアクセス レベルを選択します。

   - 自分の組織内のアカウントのみを対象にするには、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
   - 企業および教育機関の顧客のみを対象とするには、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
   - 最も広範な Microsoft ID のセットを対象にするには、 **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント]** を選択します。
1. **[リダイレクト URI]** で、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、アプリケーションのリダイレクト URI `https://login.microsoftonline.com/common/oauth2/nativeclient` を入力します。
1. **Microsoft プラットフォームのポリシー** を選択して読んだ後、 **[登録]** を選択します。 新しいアプリケーション登録の概要ページが作成されて、表示されます。

新しいアプリ登録の作成について詳しくは、[Azure Active Directory とのアプリケーションの統合](../develop/quickstart-register-app.md)に関する記事をご覧ください。

## <a name="step-3-grant-access-to-your-proxy-application"></a>手順 3:プロキシ アプリケーションへのアクセスを許可する

ネイティブ アプリケーションを登録したので、ディレクトリ内の他のアプリケーションに対するアクセス権を付与できます。この場合は、プロキシ アプリケーションへのアクセス権です。 プロキシ アプリケーションへのネイティブ アプリケーションの公開を有効にするには:

1. 新しいアプリケーション登録ページのサイドバーで、 **[API のアクセス許可]** を選択します。 新しいアプリケーション登録に対する **[API のアクセス許可]** ページが表示されます。
1. **[アクセス許可の追加]** を選択します。 **[API アクセス許可の要求]** ページが表示されます。
1. **[API を選択します]** の設定で、 **[所属する組織で使用している API]** を選択します。 API が公開されているディレクトリ内のアプリケーションを含む一覧が表示されます。
1. 検索ボックスに入力するかスクロールしてプロキシ アプリケーションを探し (「[手順 1: プロキシ アプリケーションを発行する](#step-1-publish-your-proxy-application)」で発行したもの)、プロキシ アプリケーションを選択します。
1. **[アプリケーションに必要なアクセス許可の種類]** の見出しで、アクセス許可の種類を選択します。 ネイティブ アプリケーションでサインインしたユーザーとしてプロキシ アプリケーションの API にアクセスする必要がある場合は、 **[委任されたアクセス許可]** を選択します。
1. **[アクセス許可を選択する]** の見出しで、必要なアクセス許可を選択して、 **[アクセス許可の追加]** を選択します。 ネイティブ アプリケーションの **[API のアクセス許可]** ページに、追加したプロキシ アプリケーションとアクセス許可 API が表示されるようになります。

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>手順 4:Microsoft Authentication Library をコードに追加する (.NET C# サンプル)

Microsoft Authentication Library (MSAL) の認証コンテキストのネイティブ アプリケーション コードを編集して、次のテキストを含めます。 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

サンプル コードで必要な情報は、次のように Azure AD ポータルで見つかります。

| 必要な情報 | Azure AD ポータルで見つける方法 |
| --- | --- |
| \<Tenant ID> | **[Azure Active Directory]**  >  **[プロパティ]**  >  **[ディレクトリ ID]** |
| \<App ID of the Native app> | **[アプリケーションの登録]**  > *ネイティブ アプリケーション* >  **[概要]**  >  **[アプリケーション ID]** |
| \<Scope> | **[アプリケーション 登録]**  > *ネイティブ アプリケーション* >  **[API 権限]** > Permisson API (user_impersonation) をクリックします > 右側に、**user_impersonation** というキャプション付きのパネルが表示されます。 > スコープは、編集ボックスの URL です。
| \<Proxy App Url> | API への外部 URL とパス

これらのパラメーターで MSAL コードを編集した後は、会社のネットワークの外部のユーザーであっても、ネイティブ クライアント アプリケーションに対して認証を行うことができるようになります。

## <a name="next-steps"></a>次のステップ

ネイティブ アプリケーション フローの詳細については、[Azure Active Directory でのネイティブ アプリ](../azuread-dev/native-app.md)に関する記事をご覧ください。

[Azure Active Directory でのアプリケーションへのシングル サインオン](sso-options.md#choosing-a-single-sign-on-method)のセットアップについて確認してください。