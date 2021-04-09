---
title: Experian を使用する Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: 不正防止のためにユーザー属性に基づいて ID の検証と証明を行えるように、Azure AD B2C 認証を Experian と統合する方法について説明します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2058e5362a65b6cd5f3e5b7cb9c20ce32d020d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928700"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Azure Active Directory B2C で Experian を構成するためのチュートリアル

このサンプル チュートリアルでは、Azure AD B2C を [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) と統合する方法についてのガイダンスを提供します。 Experian にはさまざまなソリューションが用意されています。[こちら](https://www.experian.com/)を参照してください。

このサンプルでは、Experian の統合されたデジタル ID と詐欺のリスク プラットフォームである **CrossCore** を使用します。 CrossCore は、ユーザー ID の確認に使用される ID 検証サービスです。 サインアップ フロー中にユーザーが提供するいくつかの情報に基づいて、リスク分析を行います。 CrossCore は、ユーザーのログイン続行を許可すべきかどうかを決定するために使用されます。 CrossCore のリスク分析で使用できる属性は次のとおりです。

- Email
- IP アドレス
- 名
- Middle Name
- Surname
- 番地
- City
- 都道府県
- 郵便番号
- 国/リージョン
- 電話番号

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

## <a name="scenario-description"></a>シナリオの説明

Experian 統合には、次のコンポーネントが含まれています。

- Azure AD B2C – ユーザーの資格情報の検証を受け持つ承認サーバー。ID プロバイダーとも呼ばれます。

- Experian – Experian サービスによって、ユーザーが提供した入力が受け取られ、ユーザーの ID が検証されます。

- カスタム REST API – この API によって、Azure AD B2C と Experian サービスとの間の統合が実装されます。

次のアーキテクチャの図に、この実装を示します。

![Experian アーキテクチャ図のスクリーンショット](media/partner-experian/experian-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーがサインアップを選択して新しいアカウントを作成し、そのページに情報を入力します。 Azure AD B2C でユーザー属性が収集されます。
| 2. | Azure AD B2C で中間層 API が呼び出され、ユーザー属性が渡されます。
| 3. | 中間層 API により、ユーザー属性が収集され、Experian API が使用できる形式に変換されます。 次に、それが Experian に送信されます。
| 4. | この情報は Experian によって使用され、リスク分析に基づいてユーザー ID を検証するために処理されます。 次に、結果が中間層 API に返されます。
| 5. | 中間層 API により情報が処理され、関連情報が正しい JSON 形式で Azure AD B2C に送り返されます。
| 6. | Azure AD B2C が、中間層 API から情報を受信します。 失敗応答が表示された場合は、ユーザーにエラー メッセージが表示されます。 成功応答が表示された場合、ユーザーは認証され、ディレクトリに書き込まれます。

## <a name="onboard-with-experian"></a>Experian を使用してオンボードする

1. Experian アカウントを作成するために、[Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) に連絡します。

2. アカウントが作成されると、API の構成に必要な情報が送信されます。 以下のセクションでは、このプロセスについて説明します。

## <a name="configure-azure-ad-b2c-with-experian"></a>Experian を使用する Azure AD B2C を構成する

### <a name="part-1---deploy-the-api"></a>パート 1 - API をデプロイする

提供された [API コード](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln)を Azure サービスにデプロイします。 このコードは、次の[手順](/visualstudio/deployment/quickstart-deploy-to-azure)に従って、Visual Studio から発行することができます。

>[!NOTE]
>必要な設定で Azure AD を構成するには、デプロイされたサービスの URL が必要です。

### <a name="part-2---deploy-the-client-certificate"></a>パート 2 - クライアント証明書をデプロイする

Experian API の呼び出しは、クライアント証明書によって保護されます。 このクライアント証明書は Experian によって提供されます。 この[ドキュメント](../app-service/environment/certificates.md#private-client-certificate)に記載されている手順に従って、証明書を Azure App Service にアップロードする必要があります。 サンプル ポリシーでは、そのプロセスのうち次の主要なステップを使用します。

- 証明書をアップロードする

- 証明書のサムプリントを使用して `WEBSITE_LOAD_ROOT_CERTIFICATES` キーを設定する

### <a name="part-3---configure-the-api"></a>パート 3 - API を構成する

アプリケーション設定は、[Azure の App Service で構成](../app-service/configure-common.md#configure-app-settings)できます。 この方法では、設定をリポジトリにチェックインすることなく、安全に構成できます。 REST API に次の設定を指定する必要があります。

| アプリケーションの設定 | source | Notes |
| :-------- | :------------| :-----------|
|CrossCoreConfig:TenantId | Experian アカウントの構成 |     |
|CrossCoreConfig:OrgCode | Experian アカウントの構成 |     |
|CrossCore:ApiEndpoint |Experian アカウントの構成|  |
|CrossCore:ClientReference | Experian アカウントの構成 | |
| CrossCore:ModelCode |Experian アカウントの構成|
| CrossCore:OrgCode | Experian アカウントの構成 |
| CrossCore:SignatureKey  | Experian アカウントの構成 |
| CrossCore:TenantId  | Experian アカウントの構成 |
| CrossCore:CertificateThumbprint | Experian の証明書 |
| BasicAuth:ApiUsername | API のユーザー名を定義します | ExtId 構成で使用されます |
| BasicAuth:ApiPassword | API のパスワードを定義します | ExtId 構成で使用されます

### <a name="part-4---create-api-policy-keys"></a>パート 4 - API ポリシー キーを作成する

この[ドキュメント](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys)を参照して、2 つのポリシー キー (API ユーザー名用に 1 つと、HTTP 基本認証のために前に定義した API パスワード用に 1 つ) を作成します。

>[!NOTE]
>キーは後でポリシーを構成するために必要になります。

### <a name="part-5---replace-the-configuration-values"></a>パート 5 - 構成値を置き換える

提供された[カスタム ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)内にある次のプレースホルダーを見つけて、自分のインスタンスの対応する値に置き換えます

|                      プレースホルダー                       |                                   値で置換                                 |                   例                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | テナントの短い名前                                                           | yourtenant.onmicrosoft.com の "yourtenant" |
| {your_trustframeworkbase_policy}                       | TrustFrameworkBase ポリシーの Azure AD B2C の名前                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C テナントに構成されている IdentityExperienceFramework アプリのアプリ ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C テナントに構成されている ProxyIdentityExperienceFramework アプリのアプリ ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | テナントのストレージ アプリケーションのアプリ ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | テナントのストレージ アプリケーションのオブジェクト ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | [ここ](#part-4---create-api-policy-keys)で作成したユーザー名キーの名前             | B2C\_1A\_RestApiUsername                     |
| {your_api_password_key_name}                           | [ここ](#part-4---create-api-policy-keys)で作成したパスワード キーの名前             | B2C\_1A\_RestApiPassword                     |
| {your_app_service_URL}                                 | 設定したアプリ サービスの URL                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>パート 6 - Azure AD B2C ポリシーを構成する

Azure AD B2C テナントを設定してポリシーを構成する方法については、この[ドキュメント](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)を参照してください。

>[!NOTE]
>このサンプル ポリシーは [Local Accounts スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)に基づいています。

>[!NOTE]
> ベスト プラクティスとして、属性コレクション ページで同意通知を追加することをお勧めします。 本人確認のために情報がサード パーティのサービスに送信されることをユーザーに通知します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[ユーザー フロー]** を選択します。

2. 以前に作成した **ユーザー フロー** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

   b. **[応答 URL]** : **リダイレクト URL** を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. ログアウトします

6. サインイン フローを実行します  

7. **[続行]** を選択すると、CrossCore パズルが表示されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)
