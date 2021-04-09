---
title: Azure Active Directory での外部ユーザー認証の回復性を強化する
description: IT 管理者とアーキテクトが外部ユーザーに対する回復力のある認証を構築するためのガイド
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724961"
---
# <a name="build-resilience-in-external-user-authentication"></a>外部ユーザー認証の回復性を強化する

[Azure Active Directory B2B コラボレーション](../external-identities/what-is-b2b.md) (Azure AD B2B) は、他の組織や個人との共同作業を可能にする[外部 ID](../external-identities/delegate-invitations.md) の機能です。 これにより、資格情報を管理することなく、ゲスト ユーザーを Azure AD テナントに安全にオンボードすることができます。 外部ユーザーは、外部 ID プロバイダー (IdP) から ID と資格情報を取得するため、新しい資格情報を覚える必要がありません。 

## <a name="ways-to-authenticate-external-users"></a>外部ユーザーを認証する方法

お使いのディレクトリに対する外部ユーザー認証の方法を選択できます。 Microsoft IdP または他の IdP を使用できます。

どの外部 IdP を使用しても、その IdP の可用性に依存することになります。 IdP に接続するいくつかの方法を使用して、回復性を向上させるためにできることがあります。

> [!NOTE] 
> Azure AD B2B には、任意の [Azure Active Directory](../index.yml) テナントから、または個人用 [Microsoft アカウント](https://account.microsoft.com/account)を使用して、任意のユーザーを認証する組み込みの機能があります。 これらの組み込みオプションを使用して構成を行う必要はありません。

### <a name="considerations-for-resilience-with-other-idps"></a>他の IdP を使用した回復性に関する考慮事項

ゲスト ユーザーの認証に外部 IdP を使用する場合、中断を防ぐために管理する必要がある特定の構成があります。

| 認証方法| 回復性に関する考慮事項 |
| - | - |
| [Facebook](../external-identities/facebook-federation.md) や [Google](../external-identities/google-federation.md) などのソーシャル IDP とのフェデレーション。| その IdP でアカウントを管理し、クライアント ID とクライアント シークレットを構成する必要があります。 |
| [SAML および WS-Federation ID プロバイダーとの直接フェデレーション](../external-identities/direct-federation.md)| 自分が依存している IdP 所有者のエンドポイントにアクセスするために、その所有者と連携する必要があります。 <br>証明書とエンドポイントを含むメタデータを管理する必要があります。 |
| [ワンタイム パスコードの電子メール送信](../external-identities/one-time-passcode.md)| この方法では、Microsoft の電子メール システム、ユーザーの電子メール システム、およびユーザーの電子メール クライアントに依存します。 |


 

## <a name="self-service-sign-up-preview"></a>セルフサービス サインアップ (プレビュー)

招待またはリンクを送信する代わりに、[セルフサービス サインアップ](../external-identities/self-service-sign-up-overview.md)を有効にすることができます。  これにより、外部ユーザーがアプリケーションへのアクセスをリクエストできるようになります。 [API コネクタ](../external-identities/self-service-sign-up-add-api-connector.md)を作成し、それをユーザー フローに関連付ける必要があります。 ユーザー エクスペリエンスを定義するユーザー フローを 1 つ以上のアプリケーションに関連付けます。 

[API コネクタ](../external-identities/api-connectors-overview.md)を使用して、セルフサービス サインアップ ユーザー フローを外部システムの API と統合することができます。 この API 統合は、[カスタム承認ワークフロー](../external-identities/self-service-sign-up-add-approvals.md)、[本人確認の実行](../external-identities/code-samples-self-service-sign-up.md)、およびユーザー属性の上書きなどのその他のタスクに使用できます。 API を使用するには、次の依存関係を管理する必要があります。

* **API コネクタの認証**:コネクタの設定には、エンドポイント URL、ユーザー名、パスワードが必要です。 これらの資格情報が管理されるプロセスを設定し、API 所有者と協力して、有効期限のスケジュールについて確実に把握するようにします。

* **API コネクタの応答**:API が使用できない場合に正常に失敗するように、サインアップ フロー内に API コネクタを設計します。 これらの[API 応答の例](../external-identities/self-service-sign-up-add-api-connector.md)と[トラブルシューティングのベスト プラクティス](../external-identities/self-service-sign-up-add-api-connector.md)を調べて、API 開発者に提供します。 API 開発チームと協力して、継続、検証エラー、ブロックの各応答を含む、考えられるすべての応答シナリオをテストします。 

## <a name="next-steps"></a>次のステップ
管理者とアーキテクト向けの回復性に関するリソース
 
* [資格情報管理を使用して回復性を強化する](resilience-in-credentials.md)

* [デバイスの状態を使用して回復性を強化する](resilience-with-device-states.md)

* [継続的アクセス評価 (CAE) を使用して回復性を強化する](resilience-with-continuous-access-evaluation.md)

* [ハイブリッド認証で回復性を強化する](resilience-in-hybrid.md)

* [アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する](resilience-on-premises-access.md)

開発者向けの回復性に関するリソース

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [CIAM システムで回復性を強化する](resilience-b2c.md)
