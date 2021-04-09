---
title: OAuth 2.0 および Azure AD を使用して API Management の API バックエンドを保護する
titleSuffix: Azure API Management
description: OAuth 2.0 ユーザー承認および Azure Active Directory を使用して、Azure API Management で Web API バックエンドへのアクセスをセキュリティで保護する方法について説明します
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: article
ms.date: 09/23/2020
ms.author: apimpm
ms.custom: contperf-fy21q1
ms.openlocfilehash: face4beab450e92be76b2bb90e45625e025de6ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97027919"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-by-using-oauth-20-authorization-with-azure-ad"></a>Azure AD で OAuth 2.0 認証を使用して、Azure API Management で Web API バックエンドを保護する 

このガイドでは、[Azure API Management](api-management-key-concepts.md) インスタンスを構成して、[Azure Active Directory (Azure AD) で OAuth 2.0 プロトコル](../active-directory/develop/active-directory-v2-protocols.md)を使用して API を保護する方法について説明します。 

> [!NOTE]
> この機能は、API Management の **Developer**、**Basic**、**Standard**、**Premium** の各レベルで使用できます。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

- API Management インスタンス
- API Management インスタンスを使用する API が公開されている
- Azure AD テナント

## <a name="overview"></a>概要

手順の概要は次のとおりです。

1. API を表すアプリケーション (バックエンドアプリ) を Azure AD に登録します。
1. API を呼び出す必要があるクライアント アプリケーションを表す別のアプリケーション (クライアントアプリ) を Azure AD に登録します。
1. Azure AD で、クライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与します。
1. OAuth 2.0 のユーザー承認を使用する API を呼び出すように Developer Console を設定します。
1. **validate-jwt** ポリシーを追加して、すべての着信要求に対して OAuth トークンを検証します。

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>API を表すアプリケーションを Azure AD に登録する

Azure AD で API を保護するには、まず API を表すアプリケーションを Azure AD に登録します。 次の手順では、Azure portal を使用してアプリケーションを登録します。 アプリの登録の詳細については、次を参照してください。「[クイックスタート:Web API を公開するようにアプリケーションを構成する](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)」。

1. [Azure portal](https://portal.azure.com) にアクセスして、アプリケーションを登録します。 **[アプリの登録]** を検索して選択します。

1. **[新規登録]** を選択します。 

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。

   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: *backend-app*)。 
   - **[サポートされているアカウントの種類]** セクションで、シナリオに適したオプションを選択します。 

1. **[リダイレクト URI]** セクションは空のままにします。

1. **[登録]** を選択して、アプリケーションを作成します。 

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。

1. **[API の公開]** を選択し、 **[アプリケーション ID URI]** に既定値を設定します。 この値は、後で使用するために記録しておきます。

1. **[Scope の追加]** ボタンをクリックして、 **[Scope の追加]** ページを表示します。 次に、API でサポートされている新しいスコープを作成します (たとえば、`Files.Read`)。

1. **[Scope の追加]** ボタンを選択してスコープを作成します。 この手順を繰り返して、API でサポートされるすべてのスコープを追加します。

1. スコープが作成されたら、後の手順で使用するためにそれらを書き留めておきます。 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>クライアント アプリケーションを表す別のアプリケーションを Azure AD に登録する

API を呼び出すすべてのクライアント アプリケーションは、Azure AD にアプリケーションとして登録する必要があります。 この例のクライアント アプリケーションは、API Management 開発者ポータルの **Developer Console** です。 

Developer Console を表すように別のアプリケーションを Azure AD に登録するには、次を実行します。

1. [Azure portal](https://portal.azure.com) にアクセスして、アプリケーションを登録します。

1. **[アプリの登録]** を検索して選択します。

1. **[新規登録]** を選択します。

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。

   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: *client-app*)。 
   - **[サポートされているアカウントの種類]** で、 **[任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント)]** を選択します。 

1. **[リダイレクト URI]** セクションで、[`Web`] を選択し、[URL] フィールドは今は空のままにします。

1. **[登録]** を選択して、アプリケーションを作成します。 

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。

1. 以降の手順で使用するこのアプリケーションのクライアント シークレットを作成します。

   1. クライアント アプリのページの一覧から、 **[証明書とシークレット]** を選択し、 **[新しいクライアント シークレット]** を選択します。

   1. **[クライアント シークレットの追加]** に **[説明]** を入力します。 キーの有効期限を選択し、 **[追加]** を選択します。

シークレットが作成されたら、後の手順で使用するために、そのキー値を書き留めておきます。 

## <a name="grant-permissions-in-azure-ad"></a>Azure AD でアクセス許可を付与する

API と Developer Console を表す 2 つのアプリケーションを登録したら、クライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与します。  

1. [Azure portal](https://portal.azure.com) にアクセスして、クライアント アプリケーションにアクセス許可を付与します。 **[アプリの登録]** を検索して選択します。

1. 対象のクライアント アプリを選択します。 アプリのページの一覧から **[API のアクセス許可]** を選択します。

1. **[アクセス許可の追加]** を選択します。

1. **[API を選択します]** の下で、 **[自分の API]** を選択し、バックエンド アプリを検索して選択します。

1. **[委任されたアクセス許可]** で、バックエンド アプリに最適なアクセス許可を選択し、 **[アクセス許可の追加]** を選択します。

1. 必要に応じて、 **[API のアクセス許可]** ページで **[\<your-tenant-name> に管理者の同意を与えます]** を選択し、このディレクトリのすべてのユーザーに代わって同意を付与します。 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Developer Console で OAuth 2.0 のユーザー承認を有効にする

これまでの手順では、Azure AD でアプリケーションを作成し、クライアントアプリがバックエンドアプリを呼び出すことができる適切なアクセス許可を付与しました。 

この例では、Developer Console はクライアントアプリです。 次の手順では、Developer Console で OAuth 2.0 のユーザー承認を有効にする方法について説明します。 

1. Azure portal で API Management インスタンスに移動します。

1. **[OAuth 2.0]**  >  **[追加]** の順に選択します。

1. **[表示名]** と **[説明]** を入力します。

1. **[クライアント登録ページの URL]** に、「`http://localhost`」などのプレースホルダーの値を入力します。 **[クライアント登録ページ URL]** では、この機能をサポートする OAuth 2.0 プロバイダーについて、ユーザーが自身のアカウントを作成および構成するために使用できるページを指定します。 この例では、ユーザーは自身のアカウントを作成も構成もしないため、代わりにプレースホルダーを使用します。

1. **[承認許可の種類]** として **[承認コード]** を選択します。

1. **[承認エンドポイントの URL]** と **[トークン エンドポイントの URL]** を指定します。 これらの値は、Azure AD テナントの **[エンドポイント]** ページから取得します。 **[アプリの登録]** ページにもう一度移動し、 **[エンドポイント]** を選択します。


1. **[OAuth 2.0 承認エンドポイント]** をコピーし、 **[承認エンドポイントの URL]** テキストボックスに貼り付けます。 [承認要求方法] の下で **[POST]** を選択します。

1. **[OAuth 2.0 トークン エンドポイント]** をコピーし、 **[トークン エンドポイントの URL]** テキストボックスに貼り付けます。 

   >[!IMPORTANT]
   > **v1** エンドポイント、または **v2** エンドポイントを使用します。 ただし、選択するバージョンによって以下の手順は異なります。 v2 エンドポイントの使用をお勧めします。 

1. **v1** エンドポイントを使用する場合は、**resource** という名前の本文パラメーターを追加します。 このパラメーターの値には、バックエンド アプリの **アプリケーション ID** を使用します。 

1. **v2** エンドポイントを使用する場合は、 **[既定のスコープ]** フィールドでバックエンド アプリ用に作成したスコープを使用します。 また、必ず [アプリケーション マニフェスト](../active-directory/develop/reference-app-manifest.md)で [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) プロパティの値を `2` に設定します。

1. 次に、クライアントの資格情報を指定します。 これらはクライアントアプリの資格情報です。

1. **[クライアント ID]** には、クライアントアプリの **アプリケーション ID** を使用します。

1. **[クライアント シークレット]** には、先ほどクライアントアプリ用に作成したキーを使用します。 

1. クライアント シークレットの直後には、承認コード付与タイプの **redirect_url** があります。 この URL をメモします。

1. **［作成］** を選択します

1. Azure Active Directory でクライアントアプリの登録に戻り、 **[認証]** を選択します。

1. **[プラットフォーム構成]** で、 **[プラットフォームを追加]** をクリックし、種類に **[Web]** を選択し、 **[リダイレクト URI]** の下に **redirect_url** を貼り付けてから、 **[構成]** ボタンをクリックして保存します。

OAuth 2.0 承認サーバーを設定したので、Developer Console で Azure AD からアクセス トークンを取得できるようになりました。 

次の手順では、API について OAuth 2.0 のユーザー承認を有効にします。 こうすることで、Developer Console は API を呼び出す前に、ユーザーの代わりにアクセス トークンを取得する必要があることを把握できるようになります。

1. API Management インスタンスに移動し、 **[API]** に移動します。

1. 保護する API を選択します。 たとえば、「 `Echo API` 」のように入力します。

1. **[設定]** に移動します。

1. **[セキュリティ]** で **[OAuth 2.0]** を選択し、先ほど構成した OAuth 2.0 サーバーを選択します。 

1. **[保存]** を選択します。

## <a name="successfully-call-the-api-from-the-developer-portal"></a>開発者ポータルから適切に API を呼び出す

> [!NOTE]
> このセクションは、開発者ポータルをサポートしていない **従量課金** レベルには適用されません。

ご使用の API で OAuth 2.0 ユーザー承認が有効になると、Developer Console は API を呼び出す前にユーザーの代理でアクセス トークンを取得するようになります。

1. 開発者ポータルの API の下にある任意の操作に移動し、**試してみてください**。 この操作で Developer Console が表示されます。

1. **[承認]** セクションの新しい項目は、先ほど追加した承認サーバーに対応しています。

1. 承認ドロップダウン リストから **[承認コード]** を選択します。Azure AD テナントにサインインするように求められます。 既にアカウントでサインインしている場合、確認メッセージは表示されません。

1. サインインに成功すると、Azure AD のアクセス トークンを使用する要求に `Authorization` ヘッダーが追加されます。 以下はサンプル トークン (Base64 エンコード) です。

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. **[送信]** を選択して、API を適切に呼び出します。

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>要求を事前承認する JWT 検証ポリシーを構成する

この時点でユーザーが Developer Console から API を呼び出そうとすると、ユーザーにはログインが求められます。 Developer Console は、ユーザーに代わってアクセス トークンを取得し、API に対して行う要求にトークンを含めます。

ただし、誰かがトークンを使用せず、または無効なトークンを使用して API を呼び出すとどうなるでしょうか。 たとえば、API を `Authorization` ヘッダーなしで呼び出してみても、呼び出しは処理されます。 理由は、API Management がこの時点でアクセス トークンを検証していないためです。 `Authorization` ヘッダーはバックエンド API に渡されます。

[JWT 検証](./api-management-access-restriction-policies.md#ValidateJWT)ポリシーを使用して、各受信要求のアクセス トークンを検証することで API Management で要求を事前承認します。 要求に有効なトークンがない場合、API Management はその要求をブロックします。 たとえば、以下のポリシーを `Echo API` の `<inbound>` ポリシー セクションに追加します。 これは、アクセス トークンの受信者要求をチェックし、トークンが有効でない場合にエラー メッセージを返します。 ポリシーの構成方法については、[ポリシーの設定と編集](./set-edit-policies.md)に関する記事をご覧ください。


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> この `openid-config` URL は、v1 エンドポイントに対応しています。 v2 `openid-config` エンドポイントについては、次の URL を使用します。
>
> `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>アプリケーションをビルドして API を呼び出す

このガイドでは、サンプル クライアント アプリケーションとして API Management で Developer Console を使用して、OAuth 2.0 によって保護された `Echo API` を呼び出します。 アプリケーションのビルドと OAuth 2.0 の実装の詳細については、「[Azure Active Directory のコード例](../active-directory/develop/sample-v2-code.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory と OAuth2.0](../active-directory/develop/authentication-vs-authorization.md) の詳細を確認します。
- API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
- バックエンド サービスを保護するその他の方法については、「[相互証明書認証](./api-management-howto-mutual-certificates.md)」を参照してください。
- [API Management サービス インスタンスの作成](./get-started-create-service-instance.md)。
- [Azure API Management での最初の API の管理](./import-and-publish.md)。
