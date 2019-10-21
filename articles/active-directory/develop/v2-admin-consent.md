---
title: Microsoft ID プラットフォームの管理者の同意のプロトコル | Microsoft Docs
description: スコープ、アクセス許可、同意など、Microsoft ID プラットフォーム エンドポイントでの承認の説明。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e642ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa63b1343fcc981629dd96e2209bf26ec2cc2bd5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326232"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームの管理者の同意

一部のアクセス許可は、テナント内で付与される前に、管理者からの同意を必要とします。  管理者の同意エンドポイントを使用し、テナント全体にアクセス許可を付与することもできます。  

## <a name="recommended-sign-the-user-into-your-app"></a>推奨:ユーザーをアプリにサインインさせる

通常、管理者の同意エンドポイントを使用するアプリケーションを構築する場合は、アプリ側に管理者がアプリのアクセス許可を承認できるページやビューが必要です。 このページは、アプリのサインアップ フローやアプリの設定の一部にするか、専用の "接続" フローにすることができます。 多くの場合、職場または学校の Microsoft アカウントでユーザーがサインインした後にのみ、”接続" ビューが表示されます。

ユーザーをアプリにサインインさせるとき、必要なアクセス許可の承認をユーザーに依頼する前に、管理者が所属する組織を識別できます。 必須ではありませんが、組織のユーザーに向けたより直観的なエクスペリエンスの作成に役立ちます。 ユーザーをサインインさせるには、[Microsoft ID プラットフォーム プロトコルのチュートリアル](active-directory-v2-protocols.md)に従ってください。

## <a name="request-the-permissions-from-a-directory-admin"></a>ディレクトリ管理者にアクセス許可を要求する

組織の管理者にアクセス許可を要求する準備ができたら、Microsoft ID プラットフォームの*管理者の同意エンドポイント*にユーザーをリダイレクトできます。

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| パラメーター     | 条件     | 説明                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | 必須 | アクセス許可を要求するディレクトリ テナント。 GUID またはフレンドリ名の形式で指定できます。または、例で示すように `common` で総称的に参照できます。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた**アプリケーション (クライアント) ID**。 |
| `redirect_uri` | 必須 |処理するアプリの応答の送信先となるリダイレクト URI。 アプリケーション登録ポータルで登録したリダイレクト URI のいずれかと完全に一致させる必要があります。 |
| `state` | 推奨 | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用します。 |
|`scope`        | 必須      | アプリケーションによって要求されるアクセス許可のセットを定義します。 これは静的スコープ (/.default を使用) か動的スコープになります。  これには OIDC スコープ (`openid`、`profile`、`email`) が含まれることもあります。 | 


現在 Azure AD では、テナント管理者がサインインして、要求を完了する必要があります。 管理者は、ユーザーが `scope` パラメーターで要求したすべてのアクセス許可を承認するように求められます。  静的な (`/.default`) 値を使用した場合、それは v1.0 管理者の同意エンドポイントのように機能し、アプリに必要なアクセス許可で見つかったすべてのスコープに対する同意を要求します。

### <a name="successful-response"></a>成功応答

管理者がアプリにアクセス許可を承認すると、成功応答は次のようになります。

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| パラメーター         | 説明                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。|
| `state`           | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。|
| `scope`          | アプリケーションに対するアクセス権を与えた一連のアクセス許可。|
| `admin_consent`   | `True` に設定されます。|

### <a name="error-response"></a>エラー応答

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

成功時の応答に見られるパラメーター以外に、以下のようなエラー パラメーターが表示されます。

| パラメーター          | 説明                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。|
| `error_description`| エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。|
| `tenant`| アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。|
| `state`           | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。|
| `admin_consent`   | `True` に設定されると、管理者の同意フローでこの応答が発生したことを示します。|

## <a name="next-steps"></a>次の手順
- [アプリをマルチテナント アプリに変換する方法](howto-convert-app-to-be-multi-tenant.md)に関する記事をご覧ください。
- 認証コードの付与フロー中、OAuth 2.0 プロトコル層で同意がどのようにサポートされるかについては[こちら](v2-oauth2-auth-code-flow.md#request-an-authorization-code)を参照してください。
- マルチテナント アプリケーションで同意フレームワークを利用し、"user" と "admin" の同意を実装し、より高度な多層アプリケーション パターンをサポートする方法については[こちら](active-directory-devhowto-multi-tenant-overview.md)を参照してください。
- [Azure AD アプリケーションの同意エクスペリエンス](application-consent-experience.md)について
