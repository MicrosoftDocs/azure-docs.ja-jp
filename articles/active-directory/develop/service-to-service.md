---
title: Azure Active Directory のサービス間アプリ
description: サービス間アプリケーションとは何かと、この種のアプリのプロトコル フロー、登録、およびトークンの有効期限の基本について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0ced89ce97d5f22270d9968fdeb0ddb3fad1e4e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194990"
---
# <a name="service-to-service-apps"></a>サービス間アプリ

サービス間アプリケーションとしては、Web API からリソースを取得する必要があるデーモンまたはサーバー アプリケーションが可能です。 このセクションに当てはまるサブシナリオは次の 2 つです。

- OAuth 2.0 クライアント資格情報の付与タイプ上に構築された、web API を呼び出す必要があるデーモン

    このシナリオでは、理解する必要のある事柄がいくつかあります。 まず、デーモン アプリケーションでは、ユーザー操作は使用できません。アプリケーションには独自の ID が必要です。 デーモン アプリケーションの例として、バッチ ジョブ (バックグラウンドで実行されているオペレーティング システム サービス) があります。 この種のアプリケーションは、アプリケーション ID を使用し、アプリケーション ID、資格情報 (パスワードまたは証明書)、アプリケーション ID の URI を Azure AD に提示して、アクセス トークンを要求します。 認証が成功したら、デーモンは Azure AD からアクセス トークンを受信します。このアクセス トークンが Web API の呼び出しに使用されます。

- OAuth 2.0 の On-Behalf-Of ドラフト仕様に基づいて構築された、web API を呼び出す必要がある (web API などの) サーバー アプリケーション

    このシナリオでは、ユーザーがネイティブ アプリケーションで既に認証されており、このネイティブ アプリケーションが Web API を呼び出す必要があるとします。 Azure AD は、Web API を呼び出すために、JWT アクセス トークンを発行します。 Web API が別のダウンストリーム Web API を呼び出す必要がある場合、Web API は代理フローを使用してユーザーの ID を委任し、第 2 層の Web API に対して認証できます。

## <a name="diagram"></a>ダイアグラム

![デーモンまたはサーバー アプリケーション対 Web API のダイアグラム](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol フロー

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>アプリケーション ID と OAuth 2.0 クライアント資格情報付与

1. まず、サーバー アプリケーションは、対話形式のサインオン ダイアログなどのユーザー操作を使用せずに、Azure AD で自身を認証する必要があります。 サーバー アプリケーションは、資格情報、アプリケーション ID、アプリケーション ID の URI を提供して、Azure AD のトークン エンドポイントに要求を送信します。
1. Azure AD がアプリケーションを認証し、Web API の呼び出しに使用する JWT アクセス トークンを返します。
1. Web アプリケーションは、返された JWT アクセス トークンを HTTPS 経由で使用して、Web API への要求の承認ヘッダーに、"Bearer" を指定した JWT 文字列を追加します。 その後、Web API が JWT を検証します。検証が正常に行われると、目的のリソースが返されます。

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>委任ユーザー ID と OAuth 2.0 On-Behalf-Of ドラフト仕様

次に説明するフローは、ユーザーが別のアプリケーション (ネイティブ アプリケーションなど) で認証されていることと、第 1 層の Web API へのアクセス トークンを取得するためにユーザー ID が使用されたことを前提としています。

1. ネイティブ アプリケーションが、第 1 層の Web API にアクセス トークンを送信します。
1. 第 1 層の Web API が、アプリケーション ID と資格情報、およびユーザーのアクセス トークンを提供して、Azure AD のトークン エンドポイントに要求を送信します。 また、この要求は、Web API が元のユーザーに代わってダウンストリーム Web API を呼び出すために新しいトークンを要求していることを示す、on_behalf_of パラメーターと共に送信されます。
1. Azure AD は、第 1 層の Web API に第 2 層の Web API へのアクセス権限があることを確認し、要求を検証して、第 1 層の Web API に JWT アクセス トークンと JWT 更新トークンを返します。
1. 第 1 層の Web API は、要求の承認ヘッダーにトークン文字列を追加して、第 2 層の Web API を HTTPS 経由で呼び出します。 アクセス トークンと更新トークンが有効であれば、第 1 層の Web API は、第 2 層の Web API を引き続き呼び出すことができます。

## <a name="code-samples"></a>コード サンプル

デーモン アプリケーションまたはサーバー アプリケーション対 Web API のシナリオのコード サンプルをご覧ください。 新しいサンプルが頻繁に追加されているので、頻繁に確認してください。 [デーモンまたはサーバー アプリケーション対 Web API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>アプリの登録

* シングル テナント: アプリケーション ID と委任ユーザー ID のどちらの場合も、デーモン アプリケーションまたはサーバー アプリケーションを Azure AD の同じディレクトリに登録する必要があります。 デーモン アプリケーションまたは サーバー アプリケーションによるリソースへのアクセスを制限するために使用する一連のアクセス許可を公開するように Web API を構成できます。 委任ユーザー ID を使用している場合、サーバー アプリケーションで Azure Portal の [他のアプリケーションに対するアクセス許可] ドロップダウン メニューから目的のアクセス許可を選択する必要があります。 アプリケーション ID を使用している場合、この手順は不要です。
* マルチテナント: アプリケーションが機能するために必要なアクセス許可を示すように、デーモンまたはサーバー アプリケーションを構成します。 必要なアクセス許可のこのリストは、アプリケーションを組織で使用できるように、発行先ディレクトリ内のユーザーまたは管理者がアプリケーションに同意するときにダイアログに表示されます。 組織内の任意のユーザーが同意できる、ユーザーレベルのアクセス許可だけを必要とするアプリケーションもあれば、 組織内のユーザーは同意できない、管理者レベルのアクセス許可を必要とするアプリケーションもあります。 このレベルのアクセスを必要とするアプリケーションに同意できるのはディレクトリ管理者だけです。 ユーザーまたは管理者が同意すると、両方の Web API がディレクトリに登録されます。

## <a name="token-expiration"></a>トークンの有効期限

最初のアプリケーションは、認証コードを使用して JWT アクセス トークンを取得するときに、JWT 更新トークンも受信します。 アクセス トークンの有効期限が切れたときは、資格情報を要求しなくても、更新トークンを使用してユーザーを再認証できます。 この更新トークンがユーザーの認証に使用されると、新しいアクセス トークンと更新トークンが取得されます。

## <a name="next-steps"></a>次の手順

- その他の[アプリケーションの種類とシナリオ](app-types.md)について学習する
- Azure AD [認証の基本](authentication-scenarios.md)について学習する
