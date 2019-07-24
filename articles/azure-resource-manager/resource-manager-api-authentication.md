---
title: Azure Active Directory 認証と Resource Manager | Microsoft Docs
description: アプリケーションを他の Azure サブスクリプションと統合するための Azure Resource Manager API と Azure Active Directory を使用した認証の開発者ガイド。
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: ae405d5dd99a0e2acced924ccccab292b4489cde
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791912"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>サブスクリプションにアクセスするための Resource Manager 認証 API の使用

この記事では、顧客の Azure リソースを管理するアプリを作成する必要があるソフトウェア開発者向けに、Azure Resource Manager API を使用して認証し、他のサブスクリプションのリソースにアクセスできるようにする方法について説明します。

アプリケーションは、次の 2 とおりの方法で Resource Manager API にアクセスできます。

1. **ユーザー + アプリケーション アクセス**: サインインしたユーザーのリソースにアクセスするアプリケーションの場合。 この方法は、Web アプリケーションやコマンド ライン ツールなど、Azure リソースの "対話形式の管理" だけに対応するアプリケーションに適しています。
2. **アプリケーション専用アクセス**: デーモン サービスやスケジュールされたジョブを実行するアプリケーションの場合。 アプリケーションの ID にリソースへの直接アクセスを許可します。 この方法は、Azure への長期間のヘッドレス (無人) アクセスを必要とするアプリケーションに適しています。

この記事では、この両方の承認方法を使用するアプリを作成する手順を説明します。 REST API または C# で各手順を実行する方法を紹介します。 完全な ASP.NET MVC アプリケーションは、[https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense) で入手できます。

## <a name="what-the-web-app-does"></a>Web アプリケーションで実行する内容

Web アプリケーションで実行する内容は次のとおりです。

1. Azure ユーザーをサインインさせます。
2. Web アプリケーションに Resource Manager へのアクセスを許可するようユーザーに求めます。
3. Resource Manager にアクセスするためのユーザー + アプリケーション アクセス トークンを取得します。
4. (手順 3 の) トークンを使用して、サブスクリプションのロールにアプリのサービス プリンシパルを割り当てます。 この手順では、サブスクリプションに対する長期的なアクセス権をアプリに付与します。
5. アプリケーション専用アクセス トークンを取得します。
6. (手順 5. の) トークンを使用して、Resource Manager でサブスクリプションのリソースを管理します。

Web アプリケーションのフローを次に示します。

![Resource Manager の認証フロー](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

ユーザーは、使用するサブスクリプションのサブスクリプション ID を指定します。

![サブスクリプション ID の指定](./media/resource-manager-api-authentication/sample-ux-1.png)

ログインに使用するアカウントを選択します。

![アカウントの選択](./media/resource-manager-api-authentication/sample-ux-2.png)

資格情報を入力します。

![資格情報の入力](./media/resource-manager-api-authentication/sample-ux-3.png)

アプリケーションに Azure サブスクリプションへのアクセスを許可します。

![アクセス権の付与](./media/resource-manager-api-authentication/sample-ux-4.png)

接続されているサブスクリプションを管理します。

![サブスクリプションの接続](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>アプリケーションを登録する
コーディングを開始する前に、Web アプリケーションを Azure Active Directory (AD) に登録します。 アプリケーションの登録により、Azure AD にアプリケーションの主要 ID が作成されます。 この ID には、OAuth クライアント ID、応答 URL、アプリケーションが Azure Resource Manager API に対する認証とアクセスに使用する資格情報など、アプリケーションに関する基本情報が保持されます。 また、アプリケーションの登録により、アプリケーションがユーザーの Microsoft API にアクセスするときに必要となる委任された各種アクセス許可が記録されます。

アプリを登録する場合は、「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)」を参照してください。 アプリに名前を付けて、サポートされているアカウントの種類で **[任意の組織のディレクトリ内のアカウント]** を選択します。 リダイレクト URL で、Azure Active Directory に関連付けられているドメインを指定します。

AD アプリケーションとしてサインインするには、アプリケーション ID とシークレットが必要です。 アプリケーション ID はアプリケーションの概要に表示されます。 シークレットを作成し、API のアクセス許可を要求する場合は、「[クイック スタート: Web API にアクセスするようにクライアント アプリケーションを構成する](../active-directory/develop/quickstart-configure-app-access-web-apis.md)」を参照してください。 新しいクライアント シークレットを指定します。 API のアクセス許可で、**[Azure Service Management]** を選択します。 **[委任されたアクセス許可]** と **[user_impersonation]** を選択します。

### <a name="optional-configuration---certificate-credential"></a>オプションの構成 - 証明書資格情報
Azure AD では、アプリケーションの証明書資格情報もサポートしています。自己署名証明書を作成し、秘密キーを保持して、Azure AD アプリケーションの登録に公開キーを追加します。 認証では、アプリケーションが秘密キーを使用して署名された小さなペイロードを Azure AD に送信すると、Azure AD は登録済みの公開キーを使用して署名を検証します。

証明書を使用した AD アプリケーションの作成方法については、「[リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)」または「[リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](resource-group-authenticate-service-principal-cli.md)」をご覧ください。

## <a name="get-tenant-id-from-subscription-id"></a>サブスクリプション ID を使用してテナント ID を取得する
Resource Manager の呼び出しに使用できるトークンを要求するには、Azure サブスクリプションをホストする Azure AD テナントのテナント ID をアプリケーションが認識している必要があります。 ほとんどの場合、ユーザーは各自のサブスクリプション ID を知っていますが、Azure Active Directory のテナント ID は知らない可能性があります。 ユーザーのテナント ID を取得するには、ユーザーにサブスクリプション ID を要求します。 サブスクリプションに関する要求を送信するときに、そのサブスクリプション ID を指定します。

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

ユーザーはまだログインしていないので、この要求は失敗しますが、その応答からテナント ID を取得できます。 その例外で、**WWW-Authenticate** の応答ヘッダーの値からテナント ID を取得します。 これは、 [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) メソッドで実装されています。

## <a name="get-user--app-access-token"></a>ユーザー + アプリケーション アクセス トークンを取得する
アプリケーションは、ユーザーの資格情報を認証し、認証コードを返すために、OAuth 2.0 承認要求と共にユーザーを Azure AD にリダイレクトします。 アプリケーションは、認証コードを使用して Resource Manager のアクセス トークンを取得します。 [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) メソッドが承認要求を作成します。

この記事では、ユーザーを認証するための REST API 要求を示します。 ヘルパー ライブラリを使用して、コードで認証することもできます。 これらのライブラリの詳細については、「[Azure Active Directory 認証ライブラリ](../active-directory/active-directory-authentication-libraries.md)」をご覧ください。 アプリケーションでの ID 管理の統合に関するガイダンスについては、「[Azure Active Directory 開発者ガイド](../active-directory/develop/v1-overview.md)」をご覧ください。

### <a name="auth-request-oauth-20"></a>承認要求 (OAuth 2.0)
Azure AD 承認エンドポイントに Open ID Connect/OAuth 2.0 承認要求を発行します。

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

この要求で使用できるクエリ文字列パラメーターについては、「[承認コードを要求する](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code)」を参照してください。

次の例は、OAuth 2.0 承認を要求する方法を示しています。

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD がユーザーを認証し、必要に応じて、アプリケーションにアクセス許可を付与するようユーザーに求めます。 アプリケーションの応答 URL に認証コードが返されます。 要求された response_mode に応じて、Azure AD はデータをクエリ文字列で送信するか、POST データとして送信します。

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>承認要求 (Open ID Connect)
ユーザーの Azure Resource Manager にアクセスするだけでなく、ユーザーが各自の Azure AD アカウントを使用して、アプリケーションにサインインできるようにする場合は、Open ID Connect 承認要求を発行します。 Open ID Connect により、アプリケーションはユーザーのサインインに使用できる id_token も Azure AD から受け取ります。

この要求で使用できるクエリ文字列パラメーターについては、「[サインイン要求を送信する](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request)」を参照してください。

Open ID Connect 要求の例を次に示します。

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD がユーザーを認証し、必要に応じて、アプリケーションにアクセス許可を付与するようユーザーに求めます。 アプリケーションの応答 URL に認証コードが返されます。 要求された response_mode に応じて、Azure AD はデータをクエリ文字列で送信するか、POST データとして送信します。

Open ID Connect 応答の例を次に示します。

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>トークン要求 (OAuth 2.0 コード付与フロー)
アプリケーションが Azure AD から認証コードを受け取ったので、次に、Azure Resource Manager のアクセス トークンを取得します。  OAuth 2.0 コード付与トークン要求を Azure AD トークン エンドポイントに送ります。

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

この要求で使用できるクエリ文字列パラメーターについては、「[承認コードを使用してアクセス トークンを要求する](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)」を参照してください。

パスワード資格情報を使用したコード付与トークンの要求の例を次に示します。

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

証明書資格情報を使用する場合は、JSON Web トークン (JWT) を作成し、アプリケーションの証明書資格情報の秘密キーを使用して署名 (RSA SHA256) します。 このトークンのビルドは、[クライアントの視覚情報フロー](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate)に示されます。  クライアント アサーション JWT トークンの署名については、 [Active Directory 認証ライブラリ (.NET) のコード](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) を参照してください。

クライアント認証の詳細については、 [Open ID Connect 仕様](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) をご覧ください。

証明書資格情報を使用したコード付与トークンの要求の例を次に示します。

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

コード付与トークンの応答の例を次に示します。

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>コード付与トークン応答を処理する
正常なトークン応答には、Azure Resource Manager の (ユーザー + アプリケーション) アクセス トークンが含まれています。 アプリケーションは、このアクセス トークンを使用して、ユーザーの Resource Manager にアクセスします。 Azure AD によって発行されたアクセス トークンの有効期間は 1 時間です。 Web アプリケーションが (ユーザー + アプリケーション) アクセス トークンを更新する必要があることはほとんどありません。 アクセス トークンを更新する必要がある場合は、アプリケーションがトークン応答で受け取った更新トークンを使用します。 OAuth 2.0 トークン要求を Azure AD トークン エンドポイントに送ります。

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

更新要求で使用するパラメーターについては、「[アクセス トークンの更新](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens)」をご覧ください。

次の例は、更新トークンの使用方法を示しています。

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

更新トークンを使用して Azure Resource Manager の新しいアクセス トークンを取得できますが、更新トークンはアプリケーションによるオフライン アクセスには適していません。 更新トークンの有効期間は限られています。また、更新トークンはユーザーにバインドされています。 ユーザーが組織を離れた場合、更新トークンを使用するアプリケーションはアクセスできなくなります。 このアプローチは、Azure リソースを管理するためにチームで使用されるアプリケーションには適していません。

## <a name="check-if-user-can-assign-access-to-subscription"></a>ユーザーがサブスクリプションへのアクセスを割り当てることができるかどうかを確認する
アプリケーションは、ユーザーの Azure Resource Manager にアクセスするためのトークンを取得しました。 次に、アプリケーションをサブスクリプションに接続します。 サブスクリプションに接続すると、ユーザーが不在の場合でも、アプリケーションはそのサブスクリプションを管理できます (長期間のオフライン アクセス)。

接続するサブスクリプションごとに、[Resource Manager List Permissions](https://docs.microsoft.com/rest/api/authorization/permissions) API を呼び出して、ユーザーがサブスクリプションのアクセス管理権限を持っているかどうかを確認します。

ASP.NET MVC サンプル アプリケーションの [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) メソッドは、この呼び出しを実装しています。

次の例は、サブスクリプションに対するユーザーのアクセス許可を要求する方法を示しています。 83cfe939-2402-4581-b761-4f59b0a041e4 は、サブスクリプションの ID です。

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

サブスクリプションに対するユーザーのアクセス許可を取得する応答の例を次に示します。

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Permissions API は複数のアクセス許可を返します。 各アクセス許可は、許可されているアクション (**actions**) と許可されていないアクション (**notactions**) で構成されます。 アクションがいずれかのアクセス許可の許可されているアクションに含まれており、かつそのアクセス許可の許可されていないアクションに含まれていない場合、ユーザーはそのアクションを実行できます。 **microsoft.authorization/roleassignments/write** は、アクセス管理権限を付与するアクションです。 アプリケーションでは、アクセス許可の結果を解析して、各アクセス許可の **actions** と **notactions** にあるアクション文字列に対して正規表現の一致を検索する必要があります。

## <a name="get-app-only-access-token"></a>アプリケーション専用アクセス トークンを取得する
これで、ユーザーが Azure サブスクリプションへのアクセスを割り当てることができるかどうかがわかりました。 次に、以下の手順を実行します。

1. サブスクリプションでアプリケーションの ID に適切な RBAC 役割を割り当てます。
2. サブスクリプションに対するアプリケーションのアクセス許可を照会するか、アプリ専用トークンを使用して Resource Manager にアクセスすることで、アクセスの割り当てを検証します。
3. アプリケーションの "接続されているサブスクリプション" のデータ構造に接続を記録して、サブスクリプションの ID を保持します。

最初の手順を詳しく見てみましょう。 アプリケーションの ID に適切な RBAC 役割を割り当てるには、以下を確認する必要があります。

* ユーザーの Azure Active Directory 内でのアプリケーションの ID のオブジェクト ID
* サブスクリプションでアプリケーションに必要な RBAC 役割の識別子

アプリケーションは、Azure AD のユーザーを認証したときに、その Azure AD にアプリケーションのサービス プリンシパル オブジェクトを作成します。 Azure は、サービス プリンシパルに割り当てられている RBAC 役割が、Azure リソースの対応するアプリケーションへの直接アクセスを許可できるようにします。 このアクションは、今まさに行おうとしていることです。 Azure AD Graph API に照会して、サインインしたユーザーの Azure AD でのアプリケーションのサービス プリンシパルの識別子を確認します。

Azure Resource Manager のアクセス トークンしかないため、Azure AD Graph API を呼び出すための新しいアクセス トークンが必要です。 Azure AD の各アプリケーションは、独自のサービス プリンシパル オブジェクトを照会するアクセス許可を持っているので、アプリケーション専用アクセス トークンで十分です。

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Azure AD Graph API のアプリケーション専用アクセス トークンを取得する

アプリケーションを認証し、Azure AD Graph API へのトークンを取得するには、OAuth 2.0 クライアント資格情報付与フロー トークン要求を Azure AD トークン エンドポイント (**https:\//login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**) に発行します。

ASP.NET MVC サンプル アプリケーションの [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) メソッドは、Active Directory Authentication Library for .NET を使用して、Graph API のアプリケーション専用アクセス トークンを取得します。

この要求で使用できるクエリ文字列パラメーターについては、「[アクセス トークンを要求する](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token)」を参照してください。

クライアント資格情報付与トークンの要求の例を次に示します。

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

クライアント資格情報付与トークンの応答の例を次に示します。

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>ユーザーの Azure AD でのアプリケーション サービス プリンシパルの ObjectId を取得する
次に、アプリ専用アクセス トークンを使用して、[Azure AD Graph Service Principals](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API にクエリを実行し、ディレクトリに登録されているアプリケーションのサービス プリンシパルのオブジェクト ID を特定します。

ASP.NET MVC サンプル アプリケーションの [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) メソッドは、この呼び出しを実装しています。

次の例は、アプリケーションのサービス プリンシパルを要求する方法を示しています。 a0448380-c346-4f9f-b897-c18733de9394 は、アプリケーションのクライアント ID です。

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

アプリケーションのサービス プリンシパルの要求に対する応答の例を次に示します。

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Azure RBAC の役割の識別子を取得する
適切な RBAC 役割をサービス プリンシパルに割り当てるには、Azure RBAC 役割の識別子を確認する必要があります。

アプリケーションの適切な RBAC 役割:

* アプリケーションが変更は一切行わず、サブスクリプションを監視するだけの場合は、サブスクリプションに対する閲覧者アクセス許可だけが必要です。 **閲覧者** 役割を割り当てます。
* アプリケーションがエンティティを作成/変更/削除して、Azure サブスクリプションを管理する場合は、共同作成者アクセス許可のいずれかが必要です。
  * 特定の種類のリソースを管理するには、そのリソース固有の共同作成者役割 (仮想マシン共同作成者、仮想ネットワーク共同作成者、ストレージ アカウント共同作成者など) を割り当てます。
  * すべての種類のリソースを管理するには、 **共同作成者** 役割を割り当てます。

アプリケーションの役割の割り当てはユーザーに表示されるので、最低限必要な権限を選択します。

[Resource Manager Role Definition API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) を呼び出してすべての Azure RBAC 役割の一覧を表示し、結果を順に調べて、役割定義を名前で検索します。

ASP.NET MVC サンプル アプリケーションの [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) メソッドは、この呼び出しを実装しています。

次の要求例は、Azure RBAC の役割の識別子を取得する方法を示しています。 09cbd307-aa71-4aca-b346-5f253e6e3ebb は、サブスクリプションの ID です。

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

応答は次の形式になります。

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

この API を継続的に呼び出す必要はありません。 役割定義の既知の GUID を確認したら、次のように役割定義 ID を作成できます。

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

よく使用される組み込みロールの ID を次に示します。

| Role | GUID |
| --- | --- |
| Reader |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Contributor |b24988ac-6180-42a0-ab88-20f7382dd24c |
| 仮想マシンの共同作業者 |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| 仮想ネットワークの共同作業者 |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| ストレージ アカウントの共同作業者 |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Web サイトの共同作業者 |de139f84-1756-47ae-9be6-808fbbe84772 |
| Web Plan の共同作業者 |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| SQL Server の共同作業者 |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| SQL DB の共同作業者 |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>アプリケーションに RBAC の役割を割り当てる
[Resource Manager Create Role Assignment](https://docs.microsoft.com/rest/api/authorization/roleassignments) API を使用して、サービス プリンシパルに適切な RBAC 役割を割り当てるために必要なものがすべて揃いました。

ASP.NET MVC サンプル アプリケーションの [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) メソッドは、この呼び出しを実装しています。

アプリケーションに RBAC の役割を割り当てる要求の例を次に示します。

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

この要求では、次の値が使用されています。

| Guid | 説明 |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |サブスクリプションの ID |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |アプリケーションのサービス プリンシパルのオブジェクト ID |
| b24988ac-6180-42a0-ab88-20f7382dd24c |共同作成者の役割の ID |
| 4f87261d-2816-465d-8311-70a27558df4c |新しい役割の割り当て用に作成された新しい GUID |

応答は次の形式になります。

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Azure Resource Manager のアプリケーション専用アクセス トークンを取得する
アプリケーションがサブスクリプションにアクセスできることを検証するには、アプリケーション専用トークンを使用してサブスクリプションでテスト タスクを実行します。

アプリケーション専用アクセス トークンを取得するには、「[Azure AD Graph API のアプリケーション専用アクセス トークンを取得する](#app-azure-ad-graph)」の手順に従います。リソース パラメーターには別の値を指定します。

    https://management.core.windows.net/

ASP.NET MVC サンプル アプリケーションの [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) メソッドは、Active Directory Authentication Library for .Net を使用して、Azure Resource Manager のアプリケーション専用アクセス トークンを取得します。

#### <a name="get-applications-permissions-on-subscription"></a>サブスクリプションに対するアプリケーションのアクセス許可を取得する
アプリケーションが Azure サブスクリプションにアクセスできることを確認するには、[Resource Manager Permissions](https://docs.microsoft.com/rest/api/authorization/permissions) API を呼び出すこともできます。 これは、ユーザーがサブスクリプションのアクセス管理権限を持っているかどうかを確認したときと同様の方法です。 ただし、今回は、前の手順で取得したアプリケーション専用アクセス トークンを使用して Permissions API を呼び出します。

ASP.NET MVC サンプル アプリケーションの [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) メソッドは、この呼び出しを実装しています。

## <a name="manage-connected-subscriptions"></a>接続されているサブスクリプションを管理する
サブスクリプションでアプリケーションのサービス プリンシパルに適切な RBAC 役割を割り当てると、アプリケーションは Azure Resource Manager のアプリケーション専用アクセス トークンを使用して、サブスクリプションを常に監視/管理することができます。

サブスクリプション所有者が、ポータルまたはコマンド ライン ツールを使用して、アプリケーションのロールの割り当てを削除した場合、アプリケーションはそのサブスクリプションにアクセスできなくなります。 その場合、サブスクリプションとの接続がアプリケーションの外部から提供されていたことをユーザーに通知し、接続を "修復" するオプションを提供する必要があります。 "修復" により、オフラインで削除されたロールの割り当てが再作成されます。

ユーザーがサブスクリプションをアプリケーションに接続できるようにしたときと同様に、ユーザーがサブスクリプションを切断することもできるようにする必要があります。 アクセス管理の観点から言うと、切断はアプリケーションのサービス プリンシパルがサブスクリプションで持つ役割の割り当てを削除することを意味します。 必要に応じて、サブスクリプションでのアプリケーションの状態も削除される場合があります。
サブスクリプションへのアクセス管理アクセス許可を持つユーザーだけがサブスクリプションを切断できます。

ASP.NET MVC サンプル アプリケーションの [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) メソッドは、この呼び出しを実装しています。

これで完了です。ユーザーは、Azure サブスクリプションをアプリケーションに簡単に接続して管理できるようになりました。
