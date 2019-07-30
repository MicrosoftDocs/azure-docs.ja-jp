---
title: ネイティブ クライアント アプリの発行 - Azure AD | Microsoft Docs
description: ネイティブ クライアント アプリケーションが Azure AD Application Proxy Connector と通信して、オンプレミス アプリケーションに対して安全なリモート アクセスを提供する方法について説明します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdc46ea3a45d04e6e837d0b7ad52ed8bf565cd2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702423"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする方法

Azure Active Directory (Azure AD) アプリケーション プロキシは、Web アプリを発行するために使用できますが、Azure AD Authentication Library (ADAL) で構成されたネイティブ クライアント アプリケーションの発行にも使うことができます。 ネイティブ クライアント アプリケーションはデバイスにインストールされる点が Web アプリとは異なり、Web アプリはブラウザーからアクセスされます。

ネイティブ クライアント アプリケーションをサポートするには、Azure AD によって発行されてヘッダーで送信されたトークンを、アプリケーション プロキシで受け入れます。 アプリケーション プロキシ サービスでは、ユーザーに対する認証が行われます。 このソリューションでは、認証のためにアプリケーションのトークンは使われません。

![エンド ユーザー、Azure AD、発行済みアプリケーション間の関係](./media/application-proxy-configure-native-client-application/richclientflow.png)

ネイティブ アプリケーションを発行するには、認証の処理が行われ、多数のクライアント環境がサポートされている、Azure AD Authentication Library を使います。 アプリケーション プロキシは、 [ネイティブ アプリケーションから Web API のシナリオ](../develop/native-app.md)に適しています。

この記事では、アプリケーション プロキシと Azure AD Authentication Library を使用してネイティブ アプリケーションを発行するための 4 つの手順を説明します。

## <a name="step-1-publish-your-proxy-application"></a>手順 1:プロキシ アプリケーションを発行する

他のアプリケーションと同様にプロキシ アプリケーションを発行し、アプリケーションにアクセスするユーザーを割り当てます。 詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md)」を参照してください。

## <a name="step-2-register-your-native-application"></a>手順 2:ネイティブ アプリケーションを登録する

ここでは、次のように、Azure AD でアプリケーションを登録する必要があります。

1. [Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。 **Azure Active Directory 管理センター**の**ダッシュボード**が表示されます。
1. サイドバーで **[Azure Active Directory]** を選択します。 **[Azure Active Directory]** 概要ページが表示されます。
1. Azure AD の概要のサイドバーで、 **[アプリの登録]** を選択します。 すべてのアプリ登録の一覧が表示されます。
1. **[新規登録]** を選択します。 **[アプリケーションの登録]** ページが表示されます。

   ![Azure portal でアプリ登録を新規作成します。](./media/application-proxy-configure-native-client-application/create.png)

1. **[名前]** という見出しで、ユーザーに表示されるアプリケーションの表示名を指定します。
1. **[サポートされているアカウントの種類]** という見出しで、次のガイドラインを使ってアクセス レベルを選択します。

   - 自分の組織内のアカウントのみを対象にするには、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
   - 企業および教育機関の顧客のみを対象とするには、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
   - 最も広範な Microsoft ID のセットを対象にするには、 **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント]** を選択します。

1. **[リダイレクト URI]** という見出しで、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、アプリケーションのリダイレクト URI を入力します。
1. **Microsoft プラットフォームのポリシー**を選択して読んだ後、 **[登録]** を選択します。 新しいアプリケーション登録の概要ページが作成されて、表示されます。

新しいアプリ登録の作成について詳しくは、[Azure Active Directory とのアプリケーションの統合](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)に関する記事をご覧ください。

## <a name="step-3-grant-access-to-your-proxy-application"></a>手順 3:プロキシ アプリケーションへのアクセスを許可する

ネイティブ アプリケーションを登録したので、ディレクトリ内の他のアプリケーションに対するアクセス権を付与できます。この場合は、プロキシ アプリケーションへのアクセス権です。 プロキシ アプリケーションへのネイティブ アプリケーションの公開を有効にするには:

1. 新しいアプリケーション登録ページのサイドバーで、 **[API のアクセス許可]** を選択します。 新しいアプリケーション登録に対する **[API のアクセス許可]** ページが表示されます。
1. **[アクセス許可の追加]** を選択します。 **[API アクセス許可の要求]** ページが表示されます。
1. **[API を選択します]** の設定で、 **[所属する組織で使用している API]** を選択します。 API が公開されているディレクトリ内のアプリケーションを含む一覧が表示されます。
1. 検索ボックスに入力するかスクロールしてプロキシ アプリケーションを探し (「[手順 1: プロキシ アプリケーションを発行する](#step-1-publish-your-proxy-application)」で発行したもの)、プロキシ アプリケーションを選択します。
1. **[アプリケーションに必要なアクセス許可の種類]** の見出しで、アクセス許可の種類を選択します。 ネイティブ アプリケーションでサインインしたユーザーとしてプロキシ アプリケーションの API にアクセスする必要がある場合は、 **[委任されたアクセス許可]** を選択します。 サインインしたユーザーを使わずにバックグラウンド サービスまたはデーモンとしてネイティブ アプリケーションを実行する場合は、 **[アプリケーションのアクセス許可]** を選択します。
1. **[アクセス許可を選択する]** の見出しで、必要なアクセス許可を選択して、 **[アクセス許可の追加]** を選択します。 ネイティブ アプリケーションの **[API のアクセス許可]** ページに、追加したプロキシ アプリケーションとアクセス許可 API が表示されるようになります。

## <a name="step-4-edit-the-active-directory-authentication-library"></a>手順 4:Active Directory 認証ライブラリの編集

Active Directory Authentication Library (ADAL) の認証コンテキストのネイティブ アプリケーション コードを編集して、次のテキストを含めます。

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

サンプル コードで必要な情報は、次のように Azure AD ポータルで見つかります。

| 必要な情報 | Azure AD ポータルで見つける方法 |
| --- | --- |
| \<Tenant ID> | **[Azure Active Directory]**  >  **[プロパティ]**  >  **[ディレクトリ ID]** |
| \<External Url of Proxy App> | **[エンタープライズ アプリケーション]**  > <*自分のプロキシ アプリケーション*> >  **[アプリケーション プロキシ]**  >  **[外部 URL]** |
| \<App ID of the Native app> | **[エンタープライズ アプリケーション]**  > <*自分のネイティブ アプリケーション*> >  **[プロパティ]**  >  **[アプリケーション ID]** |
| \<Redirect URI of the Native App> | **[Azure Active Directory]**  >  **[アプリの登録]**  > <*自分のアプリケーション*> >  **[リダイレクト URI]** |
| \<Proxy App API Url> | **[Azure Active Directory]**  >  **[アプリの登録]**  > <*自分のネイティブ アプリケーション*> >  **[API のアクセス許可]**  >  **[API/アクセス許可の名前]** |

これらのパラメーターで ADAL を編集した後は、会社のネットワークの外部のユーザーであっても、ネイティブ クライアント アプリケーションに対して認証を行うことができるようになります。

## <a name="next-steps"></a>次の手順

ネイティブ アプリケーション フローの詳細については、[Azure Active Directory でのネイティブ アプリ](../develop/native-app.md)に関する記事をご覧ください。

[Azure Active Directory でのアプリケーションへのシングル サインオン](what-is-single-sign-on.md#choosing-a-single-sign-on-method)のセットアップについて確認してください。
