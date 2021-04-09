---
title: Azure Active Directory を使用した OpenID Connect 認証
description: Azure Active Directory を使用した OpenID Connect 認証の取得に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168663"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>Azure Active Directory を使用した OpenID Connect 認証

OpenID Connect (OIDC) は、(認可に使用される) OAuth2 プロトコルに基づく認証プロトコルです。 OIDC では、標準化された OAuth2 からのメッセージ フローを使用して ID サービスが提供されます。 

OIDC の設計目標は、"簡単なことは簡単に、複雑なことも可能にする" ことです。 OIDC を使用すると、開発者はパスワード ファイルを所有して管理しなくても、Web サイトやアプリ全体でユーザーを認証できます。 これにより、アプリケーションに接続されているブラウザーやネイティブ アプリを現在使用しているユーザーの ID を安全に確認する方法がアプリケーション ビルダーに提供されます。

ユーザーの認証は、ID プロバイダーで行う必要があります。ここで、ユーザーのセッションまたは資格情報がチェックされます。 そのためには、信頼されたエージェントが必要です。 通常は、そのためにネイティブ アプリでシステム ブラウザーが起動されます。 埋め込みビューは、アプリによるユーザー パスワードのスヌーピングを防ぐ方法がないため、信頼されていないと見なされます。 

認証に加えて、ユーザーに同意を求めることもできます。 同意は、アプリケーションから保護されたリソースにアクセスできるようにするためのユーザーの明示的なアクセス許可です。 同意はリソースごとに 1 回しか入力する必要がないため、認証とは異なります。 ユーザーまたは管理者が手動で許可を取り消さない限り、同意は有効です。 

## <a name="use-when"></a>使用する状況

ユーザーの同意と Web サインインが必要です。

![アーキテクチャの図](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :アプリケーションからサービスを要求します。

* **信頼されたエージェント**:ユーザーが操作するコンポーネントです。 通常、信頼されたエージェントは Web ブラウザーです。

* **アプリケーション**:アプリケーション (またはリソース サーバー) は、リソースまたはデータが存在する場所です。 ID プロバイダーが信頼され、信頼されたエージェントが安全に認証、認可されます。 

* **Azure AD**:OIDC プロバイダーは、ID プロバイダーと呼ばれることもあります。ユーザーの情報とそのアクセス、そしてフロー内の当事者間の信頼関係に関するすべてのことは、OIDC プロバイダーによって安全に管理されます。 ここで、ユーザーの ID が認証され、リソースへのアクセス許可と取り消しが行われ、トークンが発行されます。 

## <a name="implement-oidc-with-azure-ad"></a>Azure AD を使用して OIDC を実装する

* [Azure AD とアプリケーションの統合](../saas-apps/tutorial-list.md) 

* [Microsoft ID プラットフォームにおける OAuth 2.0 プロトコルと OpenID Connect プロトコル](../develop/active-directory-v2-protocols.md) 

* [Microsoft ID プラットフォームと OpenID Connect プロトコル](../develop/v2-protocols-oidc.md) 

* [Azure Active Directory B2C での OpenID Connect による Web サインイン](../../active-directory-b2c/openid-connect.md) 

* [OpenID Connect と Azure AD を使用してアプリケーションをセキュリティで保護する](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

