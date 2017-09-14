---
title: "ネイティブ クライアント アプリの発行 - Azure AD | Microsoft Docs"
description: "ネイティブ クライアント アプリケーションが Azure AD Application Proxy Connector と通信して、オンプレミス アプリケーションに対して安全なリモート アクセスを提供する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: bdaa5af6ff5331bc310499586615b48a864c3c5e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする方法

Azure Active Directory アプリケーション プロキシを使用すると、Web アプリケーションに加えて、ネイティブ クライアント アプリを発行することもできます。 ネイティブ クライアント アプリはデバイスにインストールされる点が Web アプリとは異なります。一方、Web アプリはブラウザーからアクセスされます。 

アプリケーション プロキシはネイティブ クライアント アプリをサポートするために、標準の Authorize HTTP ヘッダーで送信された Azure AD 発行トークンを受け入れます。

![エンド ユーザー、Azure Active Directory、および発行済みアプリケーション間の関係](./media/active-directory-application-proxy-native-client/richclientflow.png)

認証を行い、多数のクライアント環境をサポートする Azure AD Authentication Library を使用して、ネイティブ アプリケーションを発行します。 アプリケーション プロキシは、 [ネイティブ アプリケーションから Web API のシナリオ](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)に適しています。 この記事では、アプリケーション プロキシと Azure AD Authentication Library を使用してネイティブ アプリケーションを発行するための 4 つの手順を説明します。 

## <a name="step-1-publish-your-application"></a>手順 1: アプリケーションの発行
他のアプリケーションと同様にプロキシ アプリケーションを発行し、アプリケーションにアクセスするユーザーを割り当てます。 詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)」を参照してください。

## <a name="step-2-configure-your-application"></a>手順 2: アプリケーションの構成
ネイティブ アプリケーションを次のように構成します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[Azure Active Directory]** > **[アプリの登録]** に移動します。
3. **[新しいアプリケーションの登録]** を選択します。
4. アプリケーションの名前を指定し、アプリケーションの種類として **[ネイティブ]** を選択し、アプリケーションのリダイレクト URI を指定します。 

   ![新しいアプリ登録の作成](./media/active-directory-application-proxy-native-client/create.png)
5. **[作成]**を選択します。

新しいアプリ登録の作成について詳しくは、「[Azure Active Directory とアプリケーションの統合](.//develop/active-directory-integrating-applications.md)」をご覧ください。


## <a name="step-3-grant-access-to-other-applications"></a>手順 3: 他のアプリケーションに対するアクセスの許可
ディレクトリ内の他のアプリケーションに公開するネイティブ アプリケーションを有効にします。

1. 引き続き、**[アプリ登録]** で、作成したばかりの新しいネイティブ アプリケーションを選択します。
2. **[必要なアクセス許可]** を選択します。
3. **[追加]**を選択します。
4. 最初の手順 **[API を選択します]** を開きます。
5. 検索バーを使用して、最初のセクションで発行したアプリケーション プロキシ アプリを探します。 アプリを選択して、**[選択]** をクリックします。 

   ![プロキシ アプリの検索](./media/active-directory-application-proxy-native-client/select_api.png)
6. 2 番目の手順 **[アクセス許可を選択します]** を開きます。
7. チェック ボックスを使用して、ネイティブ アプリケーションのアクセスをプロキシ アプリケーションに許可し、**[選択]** をクリックします。

   ![プロキシ アプリにアクセスを許可](./media/active-directory-application-proxy-native-client/select_perms.png)
8. **[完了]**を選択します。


## <a name="step-4-edit-the-active-directory-authentication-library"></a>手順 4: Active Directory 認証ライブラリの編集
Active Directory Authentication Library (ADAL) の認証コンテキストのネイティブ アプリケーション コードを編集して、次のテキストを含めます。

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = authContext.AcquireToken("< External Url of Proxy App >",
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


## <a name="see-also"></a>関連項目

ネイティブ アプリケーション フローの詳細については、「[ネイティブ アプリケーション対 Web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)」をご覧ください。

最新のニュースと更新情報については、 [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)

