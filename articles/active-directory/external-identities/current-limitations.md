---
title: B2B コラボレーションの制限 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの現在の制限事項
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4f960819aa208dcc8d3e476fc45a766452b612c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168952"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションの制限
現在、Azure Active Directory (Azure AD) B2B コラボレーションには、この記事に記載されている制限が適用されます。

## <a name="possible-double-multi-factor-authentication"></a>多要素認証重複の可能性
Azure AD B2B を使用すると、多要素認証をリソース組織 (招待する側の組織) で実施することができます。 このアプローチの理由については、「[B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)」を参照してください。 パートナー側で既に多要素認証が設定され、実施されている場合、パートナー側のユーザーは、ホーム組織と招待する側の組織の両方で認証が必要になる可能性があります。

## <a name="instant-on"></a>インスタント オン
B2B コラボレーションのフローでは、ユーザーをディレクトリに追加し、招待の使用、アプリ割り当てなどの際に動的に更新します。 更新と書き込みは、通常、1 つのディレクトリ インスタンスで行い、すべてのインスタンス間でレプリケートする必要があります。 すべてのインスタンスが更新されると、レプリケーションが完了します。 いずれかのインスタンスでオブジェクトの書き込みまたは更新が行われ、そのオブジェクトを取得する呼び出しが別のインスタンスに対して行われた場合は、レプリケーションの遅延が発生する可能性があります。 その場合は、更新または再試行が役立つことがあります。 API を使用してアプリを記述する場合は、バックオフの再試行がこの問題を軽減するための推奨される防御的な方法です。

## <a name="azure-ad-directories"></a>Azure AD ディレクトリ
Azure AD B2B は、Azure AD サービス ディレクトリの制限に従います。 ユーザーが作成できるディレクトリ数と、ユーザーまたはゲスト ユーザーが所属できるディレクトリ数の詳細については、「[Azure AD サービスの制限と制約](../enterprise-users/directory-service-limits-restrictions.md)」を参照してください。

## <a name="national-clouds"></a>各国のクラウド
[国内クラウド](../develop/authentication-national-cloud.md) は、物理的に分離された Azure のインスタンスです。 国内クラウドの境界を越えた B2B Collaboration はサポートされません。 たとえば、ご利用の Azure テナントがパブリックのグローバル クラウドに存在する場合、アカウントが国内クラウドにあるユーザーを招待することはできません。 ユーザーと共同作業を行うには、そのユーザーに別のメール アドレスを要求するか、またはご利用のディレクトリ内にメンバー ユーザー アカウントを作成してください。

## <a name="azure-us-government-clouds"></a>Azure US Government クラウド
Azure US Government クラウド内では、B2B コラボレーションは、両方が Azure US Government クラウド内にあるテナント間と、両方が B2B コラボレーションをサポートしているテナント間でサポートされています。 B2B コラボレーションをサポートする Azure US Government テナントは、Microsoft または Google アカウントを使用してソーシャル ユーザーと共同作業を行うこともできます。 これらのグループ外のユーザー (たとえばユーザーが Azure US Government クラウドの一部ではないテナント、または B2B コラボレーションをまだサポートしていないテナントにいる場合) を招待すると、その招待は失敗するか、またはその招待を履行できなくなります。 その他の制限事項の詳細については、「[Azure Active Directory Premium P1 と P2 のバリエーション](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2)」を参照してください。

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>B2B コラボレーションが Azure US Government テナントで利用可能かどうかを確認するにはどうすればよいですか？
Azure US Government クラウド テナントが B2B コラボレーションをサポートしているかどうかを確認するには、次に従います。

1. ブラウザーで次の URL にアクセスし、 *&lt;tenantname&gt;* のテナント名に置き換えます。

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. JSON の応答で `"tenant_region_scope"` を検索します。

   - `"tenant_region_scope":"USGOV”` が表示される場合は、B2B がサポートされています。
   - `"tenant_region_scope":"USG"` が表示される場合、B2B はサポートされていません。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [B2B コラボレーションの招待の委任](delegate-invitations.md)