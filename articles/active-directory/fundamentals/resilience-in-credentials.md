---
title: Azure Active Directory で資格情報管理を使用して回復性を強化する
description: 回復性がある資格情報戦略の構築に関する、アーキテクトおよび IT 管理者向けのガイド。
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
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724712"
---
# <a name="build-resilience-with-credential-management"></a>資格情報管理を使用して回復性を強化する

トークン要求で Azure AD に資格情報が提示された場合、検証に使用できる必要がある複数の依存関係があります。 1 つ目の認証要素は、Azure AD 認証 (および場合によってはオンプレミス インフラストラクチャ) に依存しています。 ハイブリッド認証アーキテクチャの詳細については、[ハイブリッド インフラストラクチャでの回復性の強化](resilience-in-hybrid.md)に関する記事を参照してください。 

2 つ目の要素を実装すると、2 つ目の要素の依存関係が 1 つ目のものの依存関係に追加されます。 たとえば、1 つ目の要素で PTA が使用され、2 つ目の要素が SMS の場合、依存関係は次のようになります。

* Azure AD 認証サービス

* Azure MFA サービス

* オンプレミス インフラストラクチャ

* 電話会社

* ユーザーのデバイス (図には示されていません)

 
![認証方法と依存関係の画像](./media/resilience-in-credentials/admin-resilience-credentials.png)

資格情報戦略では、各種の認証の依存関係、および単一障害点を回避するプロビジョニング方法を考慮する必要があります。 

認証方法にはさまざまな依存関係があるため、ユーザーができるだけ多くの第 2 要素オプションを登録できるようにすることをお勧めします。 可能であれば、依存関係が異なる第 2 要素を含めるようにしてください。 たとえば、第 2 要素としての音声通話と SMS では同じ依存関係が共有されるため、それらを唯一のオプションとして使用した場合、リスクは軽減されません。

最も回復性のある資格情報戦略は、パスワードレス認証を使用することです。 Windows Hello for Business と FIDO 2.0 のセキュリティ キーが持つ依存関係の数は、2 つの別々の要素を持つ強力な認証よりも少なくなります。 Microsoft Authenticator アプリ、Windows Hello for Business、および FIDO 2.0 のセキュリティ キーは最も安全です。 

第 2 要素として、時間ベースのワンタイム パスコード (TOTP) または OATH ハードウェア トークンを使用する　Microsoft Authenticator アプリまたはその他の認証アプリは、最も依存関係が少ないため、より回復性が高くなります。

## <a name="how-do-multiple-credentials-help-resilience"></a>複数の資格情報が回復性に役立つ理由

複数の種類の資格情報をプロビジョニングすることで、ユーザーの設定と環境の制約に対応したオプションが提供されます。 その結果、ユーザーが多要素認証の入力を求められる対話型認証は、要求時に特定の依存関係が使用できないことに対する回復性が向上します。 [多要素認証の再認証プロンプトを最適化](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)できます。

前述の個々のユーザーの回復性に加えて、企業では、構成の誤り、自然災害、または (特に多要素認証に使用されている場合の) オンプレミス フェデレーション サービスに対する企業全体でのリソース停止をもたらす運用上のエラーなど、大規模中断に関する不測の事態に備えて対策を講じておく必要があります。 

## <a name="how-do-i-implement-resilient-credentials"></a>回復性がある資格情報を実装する方法

* Windows Hello for Business、電話認証、および FIDO2 セキュリティ キーなどの[パスワードレス資格情報](../authentication/howto-authentication-passwordless-deployment.md)をデプロイして、依存関係を減らします。

* [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)を第 2 要素としてデプロイします。

* Windows Server Active Directory から同期されるハイブリッド アカウントで[パスワード ハッシュ同期](../hybrid/whatis-phs.md)をオンにします。 このオプションは、AD FS などのフェデレーション サービスと共に有効にでき、フェデレーション サービスで障害が発生した場合にフォールバックを提供します。

* [多要素認証方法の使用状況を分析して](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)、ユーザーのエクスペリエンスを向上させます。

* [回復性のあるアクセス制御戦略を実装する](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>次のステップ
管理者とアーキテクト向けの回復性に関するリソース
 
* [デバイスの状態を使用して回復性を強化する](resilience-with-device-states.md)

* [継続的アクセス評価 (CAE) を使用して回復性を強化する](resilience-with-continuous-access-evaluation.md)

* [外部ユーザー認証の回復性を強化する](resilience-b2b-authentication.md)

* [ハイブリッド認証で回復性を強化する](resilience-in-hybrid.md)

* [アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する](resilience-on-premises-access.md)

開発者向けの回復性に関するリソース

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [CIAM システムで回復性を強化する](resilience-b2c.md)