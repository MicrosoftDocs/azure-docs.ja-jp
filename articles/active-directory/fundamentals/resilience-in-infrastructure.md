---
title: Azure Active Directory の IAM インフラストラクチャで回復性を強化する
description: IAM インフラストラクチャの中断に対する回復性の強化に関する、アーキテクトおよび IT 管理者向けのガイド。
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
ms.openlocfilehash: 64fe4b8c217ec46cbb6dd046339c3ac65eebb121
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724679"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>ID およびアクセス管理インフラストラクチャで回復性を強化する

Azure Active Directory は、組織のリソースに対する認証や認可などの重要なサービスを提供する、グローバル クラウドの ID およびアクセス管理システムです。 このドキュメントでは、Azure Active Directory (Azure AD) に依存するリソースの認証または認可サービスが中断されるリスクを理解し、封じ込め、軽減するためのガイダンスを提供します。 

このドキュメント セットの設計対象

* ID アーキテクト

* ID サービスの所有者

* ID 運用チーム

[アプリケーション開発者](./resilience-app-development-overview.md)および [Azure AD B2C システム](resilience-b2c.md)を対象としたドキュメントも参照してください。

## <a name="what-is-resilience"></a>回復性とは

ID インフラストラクチャのコンテキストにおいて、回復性とは、ビジネス、ユーザー、および運用への影響を最小限またはゼロに抑えながら、認証や認可などのサービスの中断、または他のコンポーネントの障害に耐える能力のことです。 中断の影響は深刻なものになる可能性があり、回復性には入念な計画が必要です。

## <a name="why-worry-about-disruption"></a>中断について考慮する理由

認証システムへのすべての呼び出しは、呼び出しのいずれかのコンポーネントで障害が発生すると中断される可能性があります。 基本コンポーネントのエラーが原因で認証が中断されると、ユーザーはアプリケーションにアクセスできなくなります。 したがって、認証呼び出しの回数とそれらの呼び出しに含まれる依存関係の数を減らすことが、回復性にとって重要になります。 アプリケーション開発者は、トークンの要求頻度に対して何らかの制御をアサートできます。 たとえば、開発者と協力して、可能な限り、アプリケーションに Azure AD マネージド ID が使用されるようにします。 

Azure AD のようなトークンベースの認証システムでは、ユーザーのアプリケーション (クライアント) は、アプリケーションやその他のリソースにアクセスする前に、ID システムからセキュリティ トークンを取得する必要があります。 有効期間中、クライアントは、アプリケーションにアクセスするために、同じトークンを複数回提示できます。

アプリケーションに提示されたトークンの有効期限が切れると、そのトークンはアプリケーションによって拒否され、クライアントは Azure AD から新しいトークンを取得する必要があります。 新しいトークンを取得するには、資格情報のプロンプトなどのユーザー操作や、認証システムのその他の要件を満たすことが必要になる場合があります。 有効期間が長いトークンを使用して認証呼び出しの頻度を減らすことで、不要なやりとりが減ります。 ただし、トークンの有効期間と、ポリシー評価数を減らすことで生じるリスクとのバランスを取る必要があります。 トークンの有効期間の管理について詳しくは、[再認証プロンプトの最適化](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)に関するこちらの記事を参照してください。

## <a name="ways-to-increase-resilience"></a>回復性を向上させる方法
次の図は、回復性を向上させるための 6 つの具体的な方法を示しています。 各方法の詳細については、この記事の「次のステップ」部分でリンクされている記事をご覧ください。
  
![管理の回復性の概要を示す図](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>次のステップ
管理者とアーキテクト向けの回復性に関するリソース
 
* [資格情報管理を使用して回復性を強化する](resilience-in-credentials.md)

* [デバイスの状態を使用して回復性を強化する](resilience-with-device-states.md)

* [継続的アクセス評価 (CAE) を使用して回復性を強化する](resilience-with-continuous-access-evaluation.md)

* [外部ユーザー認証の回復性を強化する](resilience-b2b-authentication.md)

* [ハイブリッド認証で回復性を強化する](resilience-in-hybrid.md)

* [アプリケーション プロキシを使用したアプリケーション アクセスで回復性を強化する](resilience-on-premises-access.md)

開発者向けの回復性に関するリソース

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [CIAM システムで回復性を強化する](resilience-b2c.md)