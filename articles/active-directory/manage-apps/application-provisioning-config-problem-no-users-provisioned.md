---
title: Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない | Microsoft Docs
description: Azure AD でユーザー プロビジョニングのために構成した Azure AD ギャラリー アプリケーションにユーザーが表示されない場合に発生する一般的な問題をトラブルシューティングする方法
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e74ad04f10865a830d27c1814be10eeff3ad59
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182971"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない
アプリケーションで自動プロビジョニングが構成された後 (アプリに接続するために Azure AD に提供されたアプリの資格情報が有効であることの確認を含む)、そのアプリに対してユーザーまたはグループ、あるいはその両方がプロビジョニングされます。 プロビジョニングは、次のものによって決定されます。

-   どのユーザーとグループがアプリケーションに**割り当てられている**か。 割り当ての詳細については、「[Azure Active Directory プレビューでエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)」を参照してください。
-   **属性マッピング**が有効になっているかどうか、および有効な属性を Azure AD からアプリに同期するように構成されているかどうか。 属性マッピングの詳細については、「[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](customize-application-attributes.md)」を参照してください。
-   特定の属性値に基づいてユーザーをフィルター処理する**スコープ フィルター**が存在するかどうか。 スコープ フィルターの詳細については、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](define-conditional-rules-for-provisioning-user-accounts.md)」を参照してください。
  
ユーザーがプロビジョニングされていないことがわかった場合は、Azure AD の監査ログを調べてください。 特定のユーザーのログ エントリを検索します。

プロビジョニングの監査ログには、Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [監査ログ]** タブからアクセスできます。**[アカウント プロビジョニング]** カテゴリでログをフィルター処理すると、そのアプリのプロビジョニング イベントのみを表示できます。 属性マッピングでユーザー向けに構成された "照合 ID" に基づいてユーザーを検索できます。 たとえば、"ユーザー プリンシパル名" または "メール アドレス" を Azure AD 側の照合属性として構成し、プロビジョニングされていないユーザーの値が "audrey@contoso.com" である場合は、監査ログで "audrey@contoso.com" を検索し、返されたエントリを確認します。

プロビジョニングの監査ログには、プロビジョニング サービスによって実行されたすべての操作が記録されます。これには、プロビジョニングの対象となる割り当て済みのユーザーを Azure AD で照会する操作、これらのユーザーが存在するかどうかを対象となるアプリで照会する操作、システム間でユーザー オブジェクトを比較する操作が含まれます。 その後、比較に基づいて、対象のシステムのユーザー アカウントを追加または更新するか、無効にします。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>プロビジョニングに関して考慮すべき一般的問題
次の一覧は、どこから始めるべきかわかっている場合に詳しく確認できる一般的問題を示しています。

- [プロビジョニング サービスが開始されない](#provisioning-service-does-not-appear-to-start)
- [ユーザーを割り当てたにもかかわらず、ユーザーがスキップされておりプロビジョニングされていないと監査ログに表示される](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>プロビジョニング サービスが開始されない
Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [プロビジョニング]** セクションで、**[Provisioning Status (プロビジョニングの状態)]** を**オン**にしているとします。 しかし、その後再読み込みしても、他の状態がページに表示されません。これは、サービスは実行されていますが、初回の同期がまだ完了していないことを示している可能性があります。 上で説明されている**監査ログ**を確認し、サービスによってどのような操作が実行されているのかと、エラーが発生していないかを判断します。

>[!NOTE]
>初回の同期には 20 分～数時間かかることがあります。この時間は Azure AD のディレクトリ サイズと、プロビジョニングの対象となるユーザーの数によって異なります。 初回の同期後の両方のシステムの状態を表すウォーターマークがプロビジョニング サービスに保存されるため、初回の同期後の後続の同期はより速くなります。初回の同期により、以降の同期のパフォーマンスが向上します。
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>ユーザーを割り当てたにもかかわらず、ユーザーがスキップされておりプロビジョニングされていないと監査ログに表示される

監査ログでユーザーが "スキップされ" ていると表示される場合は、ログ メッセージで詳細を確認して原因を判断することが重要です。 一般的な原因と解決策を次に示します。

- **属性値に基づいてユーザーをフィルター処理する** **スコープ フィルターが構成されています**。 スコープ フィルターの詳細については、「[スコープ フィルター](define-conditional-rules-for-provisioning-user-accounts.md)」を参照してください。
- **ユーザーが "実質的に有効でない" 状態です**。 この特定のエラー メッセージが表示された場合、その原因は、Azure AD に格納されているユーザー割り当てレコードの問題です。 この問題を解決するには、アプリからユーザー (またはグループ) を割り当て解除し、再割り当てします。 割り当ての詳細については、[ユーザーまたはグループ アクセスの割り当て](assign-user-or-group-access-portal.md)に関するページを参照してください。
- **必須の属性が見つからないか、ユーザー用に設定されていません**。 プロビジョニングを設定するときに考慮すべき重要なことは、どのユーザー (またはグループ) プロパティが Azure AD からアプリケーションに提供されるかを定義する属性マッピングとワークフローを確認して構成することです。 この構成には、2 つのシステム間でユーザーまたはグループを一意に識別して照合するために使用される "照合プロパティ" の設定が含まれます。 この重要なプロセスの詳細については、「[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](customize-application-attributes.md)」を参照してください。
- **グループの属性マッピング:** 一部のアプリケーションでサポートされている場合は、メンバーだけでなくグループの名前と詳細もプロビジョニングします。 **[プロビジョニング]** タブに表示されるグループ オブジェクトの **[マッピング]** を有効化/無効化して、この機能を有効または無効にします。グループのプロビジョニングが有効になっている場合は、適切なフィールドが "照合 ID" に使用されていることを確かめるために、属性マッピングを必ず確認してください。 この照合 ID は、表示名または電子メール エイリアスである場合があります。 照合プロパティが空か、または Azure AD のグループ用に設定されていない場合、グループとそのメンバーはプロビジョニングされません。

## <a name="next-steps"></a>次の手順

[Azure AD Connect 同期:宣言型のプロビジョニングについて](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
