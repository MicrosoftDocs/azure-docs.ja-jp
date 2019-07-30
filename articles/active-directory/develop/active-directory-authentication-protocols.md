---
title: Azure Active Directory 認証プロトコル | Microsoft Docs
description: Azure Active Directory (AD) でサポートされる認証プロトコルの概要
documentationcenter: dev-center-name
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964c5882432ae0637039e32ca961008e8223b6b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612578"
---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory の認証プロトコル
Azure Active Directory (Azure AD) では、最も広く使用されている認証および承認のプロトコルを複数サポートしています。 このセクションの各トピックでは、Azure AD でサポートされているプロトコルとその実装について説明します。 これらのトピックには、サポートされている要求の種類の確認、フェデレーション メタデータの使用の概要、OAuth 2.0 および SAML 2.0 プロトコルの詳細なリファレンス ドキュメント、トラブルシューティング セクションが含まれます。

## <a name="authentication-protocols-articles-and-reference"></a>認証プロトコルの記事とリファレンス
* [Azure AD での署名キーのロールオーバーに関する重要な情報](active-directory-signing-key-rollover.md) - Azure AD の署名キーのロールオーバーの周期、キーを自動的に更新するために可能な変更、最も一般的なアプリケーション シナリオを更新する方法について説明します。
* [サポートされているトークンと要求の種類](v1-id-and-access-tokens.md) - Azure AD が発行するトークン内の要求について説明します。
* [フェデレーション メタデータ](azure-ad-federation-metadata.md) - Azure AD が生成するメタデータ ドキュメントの検索と解釈の方法について説明します。
* [Azure AD での OAuth 2.0](v1-protocols-oauth-code.md) - Azure AD での OAuth 2.0 の実装について説明します。
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) - 認証に OAuth 2.0 (承認プロトコル) を使用する方法について説明します。
* [クライアント資格情報を使用したサービス間の呼び出し](v1-oauth2-client-creds-grant-flow.md) - サービス間の呼び出しに OAuth 2.0 クライアント資格情報許可フローを使用する方法について説明します。
* [On-Behalf-Of フローを使用したサービス間の呼び出し](v1-oauth2-on-behalf-of-flow.md) - サービス間の呼び出しに OAuth 2.0 On-Behalf-Of フローを使用する方法について説明します。
* [SAML プロトコル リファレンス](active-directory-saml-protocol-reference.md) - Azure AD のシングル サインオンおよびシングル サインアウト SAML プロファイルについて説明します。

## <a name="see-also"></a>関連項目
[Azure Active Directory 開発者ガイド](v1-overview.md)

[Active Directory のコード例](sample-v1-code.md)
