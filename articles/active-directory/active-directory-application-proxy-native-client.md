---
title: "ネイティブ クライアント アプリの発行 - Azure AD | Microsoft Docs"
description: "ネイティブ クライアント アプリケーションが Azure AD Application Proxy Connector と通信して、オンプレミス アプリケーションに対して安全なリモート アクセスを提供する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 164e3b0af47cd98f2d20fce7344230608e9fbe8c
ms.openlocfilehash: 6762a5c88b7a3bbc0424729865ae312d74f9059f


---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする方法
Azure Active Directory アプリケーション プロキシは、SharePoint、Outlook Web Access、カスタムの基幹業務アプリケーションなどのブラウザー アプリケーションを公開するために幅広く使用されています。 また、デバイスにインストールされる点が Web アプリとは異なるネイティブ クライアント アプリケーションを発行する際にも使用できます。 この場合、標準の Authorize HTTP ヘッダーで送信された Azure AD 発行トークンをサポートしている必要があります。

![エンド ユーザー、Azure Active Directory、および発行済みアプリケーション間の関係](./media/active-directory-application-proxy-native-client/richclientflow.png)

このようなアプリケーションを発行するために推奨される方法は、すべての認証ハッスルに対応し、多様なクライアント環境をサポートする Azure AD 認証ライブラリを使用することです。 アプリケーション プロキシは、 [ネイティブ アプリケーションから Web API のシナリオ](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)に適しています。 その手順は次のとおりです。

## <a name="step-1-publish-your-application"></a>手順 1: アプリケーションの発行
他のアプリケーションと同様にプロキシ アプリケーションを発行し、ユーザーを割り当てて Premium または Basic ライセンスを付与します。 詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)」を参照してください。

## <a name="step-2-configure-your-application"></a>手順 2: アプリケーションの構成
ネイティブ アプリケーションを次のように構成します。

1. Azure クラシック ポータルにサインインします。
2. 左側のメニューの [Active Directory] アイコンを選択し、次にディレクトリを選択します。
3. 上部のメニューで、 **[アプリケーション]**をクリックします。 ディレクトリに追加されているアプリケーションがない場合、このページには **[アプリケーションの追加]** リンクだけが表示されます。 このリンクをクリックします。または、コマンド バーの **[追加]** をクリックすることもできます。
4. **[実行する作業を選択してください]** ページで、**[組織で開発中のアプリケーションを追加]** リンクをクリックします。
5. **[アプリケーション情報の指定]** ページで、アプリケーションの名前を指定し、**[ネイティブ クライアント アプリケーション]** を選択します。 矢印アイコンをクリックして続行します。
6. **[アプリケーション情報]** ページで、ネイティブ クライアント アプリケーションの**リダイレクト URI** を指定し、チェックマークをクリックして完了します。

アプリケーションが追加されると、アプリケーションのクイック スタート ページが表示されます。

## <a name="step-3-grant-access-to-other-applications"></a>手順 3: 他のアプリケーションに対するアクセスの許可
ディレクトリ内の他のアプリケーションに公開するネイティブ アプリケーションを有効にします。

1. 上部のメニューから **[アプリケーション]** をクリックし、新しいネイティブ アプリケーションを選択して、**[構成]** をクリックします。
2. **[他のアプリケーションに対するアクセス許可]** セクションまで下にスクロールします。 **[アプリケーションの追加]** をクリックし、ネイティブ アプリケーション アクセス権を付与するプロキシ アプリケーションを選択し、右下のチェックボックスをオンにします。 **[委任されたアクセス許可]** ドロップダウン メニューから、新しいアクセス許可を選択します。

![[他のアプリケーションに対するアクセス許可] のスクリーンショット - アプリケーションの追加](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>手順 4: Active Directory 認証ライブラリの編集
Active Directory Authentication Library (ADAL) の認証コンテキストのネイティブ アプリケーション コードを編集して、次の情報を含めます。

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

変数は次のように置き換えてください。

* **TenantId** は、アプリケーションの **[構成]** ページの URL (“/Directory/” の直後) の GUID に表示されます。
* **Frontend URL** は、プロキシ アプリケーションで入力したフロント エンド URL です。プロキシ アプリケーションの **[構成]** ページに表示されます。
* ネイティブ アプリケーションの **Client Id** は、ネイティブ アプリケーションの **[構成]** ページに表示されます。
* **ネイティブ アプリケーションの Redirect URI** は、ネイティブ アプリケーションの **[構成]** ページに表示されます。

![新しいネイティブ アプリケーションの構成ページのスクリーン ショット](./media/active-directory-application-proxy-native-client/new_native_app.png)

ネイティブ アプリケーション フローの詳細については、「 [ネイティブ アプリケーション対 Web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)」をご覧ください。

## <a name="see-also"></a>関連項目
* [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
* [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)
* [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)
* [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)

最新のニュースと更新情報については、 [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)



<!--HONumber=Jan17_HO4-->


