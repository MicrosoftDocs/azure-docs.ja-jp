---
title: Microsoft ID プラットフォームの管理者の同意のプロトコル
description: スコープ、アクセス許可、同意など、Microsoft ID プラットフォームでの承認の説明。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b6fb5f680dfa5e2c87533083e3df4c2bae1ed12a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097025"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームの管理者の同意

一部のアクセス許可は、テナント内で付与される前に、管理者からの同意を必要とします。  管理者の同意エンドポイントを使用し、テナント全体にアクセス許可を付与することもできます。

## <a name="recommended-sign-the-user-into-your-app"></a>推奨:ユーザーをアプリにサインインさせる

通常、管理者の同意エンドポイントを使用するアプリケーションを構築する場合は、アプリ側に管理者がアプリのアクセス許可を承認できるページやビューが必要です。 このページは、アプリのサインアップ フローやアプリの設定の一部にするか、専用の "接続" フローにすることができます。 多くの場合、職場または学校の Microsoft アカウントでユーザーがサインインした後にのみ、”接続" ビューが表示されます。

ユーザーをアプリにサインインさせるとき、必要なアクセス許可の承認をユーザーに依頼する前に、管理者が所属する組織を識別できます。 必須ではありませんが、組織のユーザーに向けたより直観的なエクスペリエンスの作成に役立ちます。 ユーザーをサインインさせるには、[Microsoft ID プラットフォーム プロトコルのチュートリアル](active-directory-v2-protocols.md)に従ってください。

## <a name="request-the-permissions-from-a-directory-admin"></a>ディレクトリ管理者にアクセス許可を要求する

組織の管理者にアクセス許可を要求する準備ができたら、Microsoft ID プラットフォームの *管理者の同意エンドポイント* にユーザーをリダイレクトできます。

```none
https://login.microsoftonline.com/{tenant}/v2.0/adminconsent
        ?client_id=6731de76-14a6-49ae-97bc-6eba6914391e
        &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
        &redirect_uri=http://localhost/myapp/permissions
        &state=12345
```

| パラメーター | 条件 | 説明 |
| :--- | :--- | :--- |
| `tenant` | 必須 | アクセス許可を要求するディレクトリ テナント。 GUID またはフレンドリ名の形式で指定できます。または、例で示すように `organizations` で総称的に参照できます。 'Common' は使用しないでください。個人のアカウントは、テナントのコンテキスト以外で管理者の同意を提供することはできません。 テナントを管理する個人用アカウントとの最善の互換性を確保するには、可能であればテナント ID を使用します。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた **アプリケーション (クライアント) ID**。 |
| `redirect_uri` | 必須 |処理するアプリの応答の送信先となるリダイレクト URI。 アプリケーション登録ポータルで登録したリダイレクト URI のいずれかと完全に一致させる必要があります。 |
| `state` | 推奨 | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用します。 |
|`scope` | 必須 | アプリケーションによって要求されるアクセス許可のセットを定義します。 これは静的スコープ (`/.default` を使用) か動的スコープになります。 これには OIDC スコープ (`openid`、`profile`、`email`) が含まれることもあります。 |

現在 Azure AD では、テナント管理者がサインインして、要求を完了する必要があります。 管理者は、ユーザーが `scope` パラメーターで要求したすべてのアクセス許可を承認するように求められます。  静的な (`/.default`) 値を使用した場合、それは v1.0 管理者の同意エンドポイントのように機能し、(ユーザーとアプリの両方に) 必要なアクセス許可で見つかったすべてのスコープに対する同意を要求します。 アプリのアクセス許可を要求するには、`/.default` 値を使用する必要があります。 `/.default` を使用しているときに、管理者の同意画面に特定のアクセス許可を管理者に常に表示しないようにする場合、ベスト プラクティスは、必要なアクセス許可セクションにアクセス許可を配置しないことです。 代わりに、`/.default` を使用するのではなく、動的同意を使用して、実行時に同意画面に表示するアクセス許可を追加できます。

### <a name="successful-response"></a>成功応答

管理者がアプリにアクセス許可を承認すると、成功応答は次のようになります。

```none
http://localhost/myapp/permissions
    ?admin_consent=True
    &tenant=fa00d692-e9c7-4460-a743-29f2956fd429
    &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
    &state=12345
```

| パラメーター | 説明 |
| :--- | :--- |
| `tenant`| アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。|
| `state` | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。|
| `scope` | アプリケーションに対するアクセス権を与えた一連のアクセス許可。|
| `admin_consent` | `True` に設定されます。|

### <a name="error-response"></a>エラー応答

```none
http://localhost/myapp/permissions
        ?admin_consent=True
        &tenant=fa15d692-e9c7-4460-a743-29f2956fd429
        &error=consent_required
        &error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z
        &state=12345
```

成功時の応答に見られるパラメーター以外に、以下のようなエラー パラメーターが表示されます。

| パラメーター | 説明 |
|:-------------------|:-------------------------------------------------------------------------------------------------|
| `error` | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。|
| `error_description` | エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。|
| `tenant`| アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。|
| `state` | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。|
| `admin_consent` | `True` に設定されると、管理者の同意フローでこの応答が発生したことを示します。|

## <a name="next-steps"></a>次のステップ
- [アプリをマルチテナント アプリに変換する方法](howto-convert-app-to-be-multi-tenant.md)に関する記事をご覧ください。
- 認証コードの付与フロー中、OAuth 2.0 プロトコル層で同意がどのようにサポートされるかについては[こちら](v2-oauth2-auth-code-flow.md#request-an-authorization-code)を参照してください。
- マルチテナント アプリケーションで同意フレームワークを利用し、"user" と "admin" の同意を実装し、より高度な多層アプリケーション パターンをサポートする方法については[こちら](./howto-convert-app-to-be-multi-tenant.md)を参照してください。
- [Azure AD アプリケーションの同意エクスペリエンス](application-consent-experience.md)について
