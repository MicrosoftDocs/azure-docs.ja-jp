---
title: Azure Active Directory と API Management で OAuth 2.0 を使用して API を保護する | Microsoft Docs
description: Azure Active Directory と API Management で Web API バックエンドを保護する方法について説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 72cffea3e5d42210bffbdbeef94c475cc8bdebf4
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312101"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Azure Active Directory と API Management で OAuth 2.0 を使用して API を保護する

このガイドでは、Azure API Management インスタンスを構成して、Azure Active Directory (Azure AD) で OAuth 2.0 プロトコルを使用して API を保護する方法について説明します。 

## <a name="prerequisites"></a>前提条件
この記事の手順を実行するには、以下が必要です。
* API Management インスタンス
* API Management インスタンスを使用する API が公開されている
* Azure AD テナント

## <a name="overview"></a>概要

手順の概要は次のとおりです。

1. API を表すアプリケーション (バックエンドアプリ) を Azure AD に登録します。
2. API を呼び出す必要があるクライアント アプリケーションを表す別のアプリケーション (クライアントアプリ) を Azure AD に登録します。
3. Azure AD で、クライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与します。
4. OAuth 2.0 のユーザー承認を使用するように Developer Console を設定します。
5. **validate-jwt** ポリシーを追加して、すべての着信要求に対して OAuth トークンを検証します。

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>API を表すアプリケーションを Azure AD に登録する

Azure AD で API を保護するには、まず API を表すアプリケーションを Azure AD に登録します。 

1. [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) に移動します。 

2. **[新規登録]** を選択します。 

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。 
    - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `backend-app`)。 
    - **[サポートされているアカウントの種類]** セクションで、 **[任意の組織のディレクトリ内のアカウント]** を選択します。 

1. **[リダイレクト URI]** セクションは今のところ空のままにします。

1. **[登録]** を選択して、アプリケーションを作成します。 

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。

アプリケーションが作成されたら、次の手順で使用する **[アプリケーション ID]** をメモします。 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>クライアント アプリケーションを表す別のアプリケーションを Azure AD に登録する

API を呼び出すすべてのクライアント アプリケーションも、Azure AD にアプリケーションとして登録する必要があります。 この例のサンプル クライアント アプリケーションは、API Management 開発者ポータルの Developer Console です。 Developer Console を表す別のアプリケーションを Azure AD に登録する方法は次のとおりです。

1. [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) に移動します。 

1. **[新規登録]** を選択します。

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。 
    - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `client-app`)。 
    - **[サポートされているアカウントの種類]** セクションで、 **[任意の組織のディレクトリ内のアカウント]** を選択します。 

1. **[リダイレクト URI]** セクションで、`Web` を選択し、URL `https://contoso5.portal.azure-api.net/signin` を入力します。

1. **[登録]** を選択して、アプリケーションを作成します。 

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。

次に、以降の手順で使用するこのアプリケーションのクライアント シークレットを作成します。

1. クライアント アプリのページの一覧から、 **[証明書とシークレット]** を選択し、 **[新しいクライアント シークレット]** を選択します。

2. **[クライアント シークレットの追加]** に **[説明]** を入力します。 キーの有効期限を選択し、 **[追加]** を選択します。

キーの値をメモします。 

## <a name="grant-permissions-in-azure-ad"></a>Azure AD でアクセス許可を付与する

API と Developer Console を表す 2 つのアプリケーションを登録したら、次はクライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与する必要があります。  

1. **[アプリの登録]** に移動します。 

2. `client-app` を選択し、アプリのページの一覧で **[API のアクセス許可]** に移動します。

3. **[アクセス許可の追加]** を選択します。

4. **[API の選択]** で `backend-app` を見つけて選択します。

5. **[委任されたアクセス許可]** で、`backend-app` に最適なアクセス許可を選択します。

6. **[アクセス許可の追加]** を選択する 

> [!NOTE]
> **Azure Active Directory** が他のアプリケーションに対するアクセス許可の下に表示されない場合、 **[追加]** を選択して、その一覧から追加します。

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Developer Console で OAuth 2.0 のユーザー承認を有効にする

これまでの手順では、Azure AD でアプリケーションを作成し、クライアントアプリがバックエンドアプリを呼び出すことができる適切なアクセス許可を付与しました。 

この例では、Developer Console はクライアントアプリです。 次の手順では、Developer Console で OAuth 2.0 のユーザー承認を有効にする方法について説明します。 

1. Azure portal で API Management インスタンスに移動します。

2. **[OAuth 2.0]**  >  **[追加]** の順に選択します。

3. **[表示名]** と **[説明]** を入力します。

4. **[クライアント登録ページの URL]** に、「`http://localhost`」などのプレースホルダーの値を入力します。 **[クライアント登録ページ URL]** では、この機能をサポートする OAuth 2.0 プロバイダーについて、ユーザーが自身のアカウントを作成および構成するために使用できるページを指定します。 この例では、ユーザーは自身のアカウントを作成も構成もしないため、代わりにプレースホルダーを使用します。

5. **[承認許可の種類]** として **[承認コード]** を選択します。

6. **[承認エンドポイントの URL]** と **[トークン エンドポイントの URL]** を指定します。 これらの値は、Azure AD テナントの **[エンドポイント]** ページから取得します。 **[アプリの登録]** ページにもう一度移動し、 **[エンドポイント]** を選択します。

7. **[OAuth 2.0 承認エンドポイント]** をコピーし、 **[承認エンドポイントの URL]** テキストボックスに貼り付けます。

8. **[OAuth 2.0 トークン エンドポイント]** をコピーし、 **[トークン エンドポイントの URL]** テキストボックスに貼り付けます。 トークン エンドポイントに貼り付けるだけでなく、**resource** という body パラメーターを追加します。 このパラメーターの値には、バックエンド アプリの**アプリケーション ID** を使用します。

9. 次に、クライアントの資格情報を指定します。 これらはクライアントアプリの資格情報です。

10. **[クライアント ID]** には、クライアントアプリの**アプリケーション ID** を使用します。

11. **[クライアント シークレット]** には、先ほどクライアントアプリ用に作成したキーを使用します。 

12. クライアント シークレットの直後には、承認コード付与タイプの **redirect_url** があります。 この URL をメモします。

13. **作成** を選択します。

14. クライアントアプリの **[設定]** ページに戻ります。

15. **[応答 URL]** を選択し、最初の行に **redirect_url** を貼り付けます。 この例では、最初の行の `https://localhost` を URL に置き換えました。  

OAuth 2.0 承認サーバーを設定したので、Developer Console で Azure AD からアクセス トークンを取得できるようになりました。 

次の手順では、API について OAuth 2.0 のユーザー承認を有効にします。 こうすることで、Developer Console は API を呼び出す前に、ユーザーの代わりにアクセス トークンを取得する必要があることを把握できるようになります。

1. API Management インスタンスに移動し、 **[API]** に移動します。

2. 保護する API を選択します。 この例では、`Echo API` を使用します。

3. **[設定]** に移動します。

4. **[セキュリティ]** で **[OAuth 2.0]** を選択し、先ほど構成した OAuth 2.0 サーバーを選択します。 

5. **[保存]** を選択します。

## <a name="successfully-call-the-api-from-the-developer-portal"></a>開発者ポータルから適切に API を呼び出す

> [!NOTE]
> このセクションは、開発者ポータルをサポートしていない**従量課金**レベルには適用されません。

`Echo API` で OAuth 2.0 ユーザー承認が有効にすると、Developer Console は API を呼び出す前にユーザーの代理でアクセス トークンを取得するようになります。

1. 開発者ポータルの `Echo API` の下にある操作に移動し、**試してみてください**。 この操作で Developer Console が表示されます。

2. **[承認]** セクションの新しい項目は、先ほど追加した承認サーバーに対応しています。

3. 承認ドロップダウン リストから **[承認コード]** を選択します。Azure AD テナントにサインインするように求められます。 既にアカウントでサインインしている場合、確認メッセージは表示されません。

4. サインインに成功すると、Azure AD のアクセス トークンを使用する要求に `Authorization` ヘッダーが追加されます。 以下はサンプル トークン (Base64 エンコード) です。

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. **[送信]** を選択すると、API を適切に呼び出すことができます。


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>要求を事前承認する JWT 検証ポリシーを構成する

この時点でユーザーが Developer Console から電話をかけようとすると、ユーザーにはログインが求められます。 Developer Console は、ユーザーに代わってアクセス トークンを取得します。

ただし、誰かがトークンを使用せず、または無効なトークンを使用して API を呼び出すとどうなるでしょうか。 たとえば、`Authorization` ヘッダーを削除しても API を呼び出すことができます。 理由は、API Management がこの時点でアクセス トークンを検証していないためです。 `Authorization` ヘッダーはバックエンド API に渡されます。

[JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT)ポリシーを使用して、各受信要求のアクセス トークンを検証することで API Management で要求を事前承認できます。 要求に有効なトークンがない場合、API Management はその要求をブロックします。 たとえば、以下のポリシーを `Echo API` の `<inbound>` ポリシー セクションに追加できます。 これは、アクセス トークンの受信者要求をチェックし、トークンが有効でない場合にエラー メッセージを返します。 ポリシーの構成方法については、[ポリシーの設定と編集](set-edit-policies.md)に関する記事をご覧ください。

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

## <a name="build-an-application-to-call-the-api"></a>アプリケーションをビルドして API を呼び出す

このガイドでは、サンプル クライアント アプリケーションとして API Management で Developer Console を使用して、OAuth 2.0 によって保護された `Echo API` を呼び出します。 アプリケーションのビルドと OAuth 2.0 の実装の詳細については、「[Azure Active Directory のコード例](../active-directory/develop/sample-v1-code.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* [Azure Active Directory と OAuth2.0](../active-directory/develop/authentication-scenarios.md) の詳細を確認します。
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
* バックエンド サービスを保護するその他の方法については、「[相互証明書認証](api-management-howto-mutual-certificates.md)」を参照してください。

* [API Management サービス インスタンスの作成](get-started-create-service-instance.md)。

* [Azure API Management での最初の API の管理](import-and-publish.md)。
