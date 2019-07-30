---
title: SaaS アプリケーションを登録する | Azure Marketplace
description: Azure portal を使用して SaaS アプリケーションを登録する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 1f644dca7a057667fb37f5a79a4683c592059e7a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331597"
---
# <a name="register-a-saas-application"></a>SaaS アプリケーションを登録する

この記事では、Microsoft [Azure portal](https://portal.azure.com/) を使用して SaaS アプリケーションを登録する方法について説明します。  登録が完了すると、SaaS Fulfillment API にアクセスするために使用できる Azure Active Directory (Azure AD) セキュリティ トークンが届きます。  Azure AD の詳細については、「[認証とは](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)」を参照してください。


## <a name="service-to-service-authentication-flow"></a>サービス間認証のフロー

次の図は、新しい顧客のサブスクリプションのフローと、これらの API をいつ使用するかを示しています。

![SaaS オファーの API フロー](./media/saas-offer-publish-api-flow-v1.png)

Azure では、SaaS サービスがそのエンド ユーザーに公開する認証に対して、制約がありません。 ただし、SaaS Fulfillment API での認証は、通常は Azure portal を使用して SaaS アプリを登録することで取得される Azure AD セキュリティ トークンを使用して実行されます。 


## <a name="register-an-azure-ad-secured-app"></a>Azure AD で保護されるアプリを登録する

アプリケーションで Azure AD の機能を使用するには、まず Azure AD テナントにそのアプリケーションを登録する必要があります。 この登録プロセスでは、アプリケーションが配置されている URL、ユーザーが認証された後の応答の送信先となる URL、アプリを識別する URI など、アプリケーションの詳細を Azure AD に提供します。  Azure portal を使用して新しいアプリケーションを登録するには、次の手順を実行します。

1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントをクリックし、ポータルのセッションを目的のテナントに設定します。
3.  左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスをクリックし、 **[アプリの登録]** 、 **[新しいアプリケーションの登録]** の順にクリックします。

    ![SaaS AD のアプリ登録](./media/saas-offer-app-registration-v1.png)

4.  作成\' ページで、アプリケーションの登録情報を入力します。
    -   **[名前]** :わかりやすいアプリケーション名を入力します
    -   **アプリケーションの種類**: 
        - デバイスのローカルにインストールされている[クライアント アプリケーション](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)については、 **[ネイティブ]** を選択します。 OAuth の public [ネイティブ クライアント](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)の場合には、この設定を使用します。
        - セキュリティで保護されたサーバーにインストールされている[クライアント アプリケーション](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)と[リソース/API アプリケーション](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)については、 **[Web アプリ/API]** を選択します。 OAuth のコンフィデンシャル [Web クライアント](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client)と、パブリック [ユーザーエージェントベース クライアント](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)の場合には、この設定を使用します。
        同じアプリケーションでクライアントとリソース/API を両方とも公開することもできます。
    -   **サインオン URL**:Web アプリまたは API アプリケーションの場合は、アプリのベース URL を指定します。 ローカル コンピューターで実行されている Web アプリの URL であれば、たとえば **http://localhost:31544** のようになります。 ユーザーはこの URL を使用して、Web クライアント アプリケーションにサインインすることになります。
    -   **リダイレクト URI**:ネイティブ アプリケーションの場合は、トークン応答を返すために Azure AD に使用される URI を指定します。 **http://MyFirstAADApp** など、ご自分のアプリケーションに固有の値を入力してください。

        ![SaaS AD のアプリ登録](./media/saas-offer-app-registration-v1-2.png)

        Web アプリケーションまたはネイティブ アプリケーションの具体的な例については、[Azure AD 開発者向けガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)に関する記事の*開始*セクションで利用できるクイック スタート ガイド付きセットアップを確認してください。

5.  完了したら、 **[作成]** をクリックします。 Azure AD によってアプリケーションに一意の "*アプリケーション ID*" が割り当てられ、アプリケーションのメイン登録ページが表示されます。 アプリケーションが Web アプリケーションとネイティブ アプリケーションのどちらであるかに応じて、アプリケーションに機能を追加するためのさまざまなオプションが表示されます。

>[!Note]
>既定では、新しく登録されたアプリケーションは、同じテナントのユーザーのみサインインできる構成になります。


## <a name="using-the-azure-ad-security-token"></a>Azure AD セキュリティ トークンの使用

アプリケーションを登録したら、Azure AD セキュリティ トークンをプログラムで要求できます。  パブリッシャーは、このトークンを使用すること、およびそれを解決するための要求を行うことを期待されています。  さまざまな Fulfillment API を使用するときは、ユーザーが Azure から SaaS の Web サイトにリダイレクトされる時点で URL の中にトークン クエリ パラメーターが配置されます。  このトークンは 1 時間だけ有効です。  また、ブラウザーからのトークン値は、使用前に URL デコードする必要があります。

これらのトークンの詳細については、「[Azure Active Directory アクセス トークン](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)」を参照してください。


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD アプリに基づいたトークンの取得

HTTP メソッド

`POST`

*要求 URL*

**https://login.microsoftonline.com/ *{tenantId}* /oauth2/token**

*URI パラメーター*

|  **パラメーター名**  | **必須**  | **説明**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | 登録された AAD アプリケーションのテナント ID   |
|  |  |  |


*要求ヘッダー*

|  **ヘッダー名**  | **必須** |  **説明**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | 要求に関連付けられたコンテンツの種類。 既定値は `application/x-www-form-urlencoded` です。  |
|  |  |  |


*要求本文*

| **プロパティ名**   | **必須** |  **説明**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | 付与タイプ。 既定値は `client_credentials` です。                    |
|  Client_id          | True         |  Azure AD アプリに関連付けられているクライアントまたはアプリの識別子。                  |
|  client_secret      | True         |  Azure AD アプリに関連付けられているパスワード。                               |
|  リソース           | True         |  トークンを要求されたターゲット リソース。 既定値は `62d94f6c-d599-489b-a797-3e10e42fbe22` です。 |
|  |  |  |


*応答*

|  **名前**  | **種類**       |  **説明**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | 要求成功   |
|  |  |  |

*TokenResponse*

サンプル応答トークン:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>次の手順

Azure AD で保護されたアプリで、[SaaS Fulfillment API バージョン 2](./pc-saas-fulfillment-api-v2.md) を使用できるようになりました。
