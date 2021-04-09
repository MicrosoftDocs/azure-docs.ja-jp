---
title: Azure Active Directory での SAML と OIDC または OAuth を使用した Microsoft ID プラットフォームのトークン交換のシナリオ
description: Azure Active Directory での SAML と OIDC または OAuth を使用する場合の一般的なトークン交換シナリオについて説明します。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 812bf7bd68362667fcd1b636d0d28fdbb21c1409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582351"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>SAML と OIDC または OAuth を使用した Microsoft ID プラットフォーム のトークン交換のシナリオ

SAML と OpenID Connect (OIDC) または OAuth は、シングル サインオン (SSO) を実装するために使用される一般的なプロトコルです。 一部のアプリでは SAML のみが実装され、その他のアプリでは OIDC または OAuth のみが実装されている場合があります。 どちらのプロトコルも、トークンを使用してシークレットを通信します。 SAML の詳細については、「[シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)」を参照してください。 OIDC と OAuth の詳細については、「[Microsoft ID プラットフォームにおける OAuth 2.0 プロトコルと OpenID Connect プロトコル](active-directory-v2-protocols.md)」を参照してください。

この記事では、アプリで SAML が実装されているが、OIDC または OAuth を使用する Graph API を呼び出す一般的なシナリオの概要について説明します。 このシナリオを使用するユーザー向けの基本的なガイダンスが提供されます。

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>シナリオ:SAML トークンがあり、Graph API を呼び出す必要がある
多くのアプリが、SAML を使用して実装されています。 ただし、Graph API では、OIDC および OAuth のプロトコルが使用されます。 OIDC または OAuth の機能を SAML アプリに追加することは簡単ではありませんが、可能です。 OAuth 機能がアプリで使用できるようになると、Graph API を使用できます。

一般的な方法は、OIDC/OAuth スタックをアプリに追加することです。 両方の標準が実装されているアプリでは、セッション Cookie を使用できます。 トークンを明示的に交換しません。 SAML を使用してユーザーをログインさせます。これにより、セッション Cookie が生成されます。 Graph API により OAuth フローが呼び出されたときに、そのセッション Cookie を使用して認証を行います。 この方法で条件付きアクセスチェックに合格したと見なされ、ユーザーが承認されます。

> [!NOTE]
> OIDC または OAuth 動作の追加に推奨されるライブラリは、Microsoft Authentication Library (MSAL) です。 MSAL の詳細については、[Microsoft Authentication Library (MSAL) の概要](msal-overview.md)に関するページを参照してください。 以前のライブラリは Active Directory 認証ライブラリ (ADAL) と呼ばれていましたが、MSAL がその後継であるため、推奨されません。

## <a name="next-steps"></a>次のステップ
- [認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)
