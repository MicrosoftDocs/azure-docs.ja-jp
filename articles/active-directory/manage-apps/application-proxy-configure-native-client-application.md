---
title: ネイティブ クライアント アプリの発行 - Azure AD | Microsoft Docs
description: ネイティブ クライアント アプリケーションが Azure AD Application Proxy Connector と通信して、オンプレミス アプリケーションに対して安全なリモート アクセスを提供する方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21e101dee878a48cce1005d51ad5e59166b0cfa1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193120"
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>プロキシ アプリケーションからネイティブ クライアント アプリケーションを操作する方法

Azure Active Directory アプリケーション プロキシを使用すると、Web アプリケーションに加えて、Azure AD Authentication Library (ADAL) を使用して構成されているネイティブ クライアント アプリを発行することもできます。 ネイティブ クライアント アプリはデバイスにインストールされる点が Web アプリとは異なります。一方、Web アプリはブラウザーからアクセスされます。 

アプリケーション プロキシは、ヘッダーで送信される Azure AD 発行トークンを受け入れることで、ネイティブ クライアント アプリをサポートします。 アプリケーション プロキシ サービスは、ユーザーに代わって認証を実行します。 このソリューションでは、認証するためにアプリケーションのトークンは使用しません。 

![エンド ユーザー、Azure Active Directory、および発行済みアプリケーション間の関係](./media/application-proxy-configure-native-client-application/richclientflow.png)

認証を行い、多数のクライアント環境をサポートする Azure AD Authentication Library を使用して、ネイティブ アプリケーションを発行します。 アプリケーション プロキシは、 [ネイティブ アプリケーションから Web API のシナリオ](../develop/native-app.md)に適しています。 

この記事では、アプリケーション プロキシと Azure AD Authentication Library を使用してネイティブ アプリケーションを発行するための 4 つの手順を説明します。 

## <a name="step-1-publish-your-application"></a>手順 1:アプリケーションの発行
他のアプリケーションと同様にプロキシ アプリケーションを発行し、アプリケーションにアクセスするユーザーを割り当てます。 詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md)」を参照してください。

## <a name="step-2-configure-your-application"></a>手順 2:アプリケーションの作成
ネイティブ アプリケーションを次のように構成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** > **[アプリの登録]** に移動します。
3. **[新しいアプリケーションの登録]** を選択します。
4. アプリケーションの名前を指定し、アプリケーションの種類として **[ネイティブ]** を選択し、アプリケーションのリダイレクト URI を指定します。 

   ![新しいアプリ登録の作成](./media/application-proxy-configure-native-client-application/create.png)
5. **作成**を選択します。

新しいアプリ登録の作成について詳しくは、「[Azure Active Directory とアプリケーションの統合](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)」をご覧ください。


## <a name="step-3-grant-access-to-other-applications"></a>手順 3:他のアプリケーションに対するアクセスの許可
ディレクトリ内の他のアプリケーションに公開するネイティブ アプリケーションを有効にします。

1. 引き続き、**[アプリ登録]** で、作成したばかりの新しいネイティブ アプリケーションを選択します。
2. **[API のアクセス許可]** を選択します。
3. **[アクセス許可の追加]** を選択します。
4. 最初の手順 **[API を選択します]** を開きます。
5. 検索バーを使用して、最初のセクションで発行したアプリケーション プロキシ アプリを探します。 アプリを選択して、**[選択]** をクリックします。 

   ![プロキシ アプリの検索](./media/application-proxy-configure-native-client-application/select_api.png)
6. 2 番目の手順 **[アクセス許可を選択します]** を開きます。
7. チェック ボックスを使用して、ネイティブ アプリケーションのアクセスをプロキシ アプリケーションに許可し、**[選択]** をクリックします。

   ![プロキシ アプリにアクセスを許可](./media/application-proxy-configure-native-client-application/select_perms.png)
8. **[完了]** を選択します。


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

サンプル コードの変数は次のように置き換えてください。

* **Tenant ID** は Azure Portal で確認できます。 **[Azure Active Directory]** > **[プロパティ]** に移動し、ディレクトリ ID をコピーします。 
* **External URL** は、プロキシ アプリケーションに入力したフロントエンド URL です。 この値を検索するには、プロキシ アプリの **Application proxy** セクションに移動します。
* ネイティブ アプリケーションの **App Id** は、ネイティブ アプリケーションの **[プロパティ]** ページに表示されます。
* **Redirect URI of the native app** は、ネイティブ アプリケーションの **[リダイレクト URI]** ページに表示されます。

これらのパラメーターを使用して ADAL を編集すると、企業ネットワークの外部ユーザーであっても、ネイティブ クライアント アプリを認証できるようになります。 

## <a name="next-steps"></a>次の手順

ネイティブ アプリケーション フローの詳細については、「[ネイティブ アプリケーション対 Web API](../develop/native-app.md)」をご覧ください。

[アプリケーション プロキシのシングル サインオン](what-is-single-sign-on.md#single- sign-on-methods)の設定に関する記事を参照します。
