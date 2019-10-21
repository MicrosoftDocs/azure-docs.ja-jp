---
title: Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない | Microsoft Docs
description: Azure AD でユーザー プロビジョニングのために構成した Azure AD ギャラリー アプリケーションにユーザーが表示されない場合に発生する一般的な問題をトラブルシューティングする方法
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0562027bb599b596b640e0c1a669447e3fed9680
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034163"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない
アプリケーションで自動プロビジョニングが構成された後 (アプリに接続するために Azure AD に提供されたアプリの資格情報が有効であることの確認を含む)、そのアプリに対してユーザーまたはグループ、あるいはその両方がプロビジョニングされます。 プロビジョニングは、次のものによって決定されます。

-   どのユーザーとグループがアプリケーションに**割り当てられている**か。 入れ子になったグループまたは Office 365 グループのプロビジョニングはサポートされていません。 割り当ての詳細については、「[Azure Active Directory プレビューでエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)」を参照してください。
-   **属性マッピング**が有効になっているかどうか、および有効な属性を Azure AD からアプリに同期するように構成されているかどうか。 属性マッピングの詳細については、「[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](customize-application-attributes.md)」を参照してください。
-   特定の属性値に基づいてユーザーをフィルター処理する**スコープ フィルター**が存在するかどうか。 スコープ フィルターの詳細については、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](define-conditional-rules-for-provisioning-user-accounts.md)」を参照してください。
  
ユーザーがプロビジョニングされていないことがわかった場合は、Azure AD の[プロビジョニング ログ (プレビュー)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) を調べてください。 特定のユーザーのログ エントリを検索します。

Azure portal で、 **[Azure Active Directory]** &gt; **[エンタープライズ アプリ]** &gt; **[プロビジョニング ログ (プレビュー)]** ( **[アクティビティ]** セクション内) を順に選択して、プロビジョニング ログにアクセスできます。 プロビジョニング データは、ユーザー名か、ソース システムまたはターゲット システムの識別子に基づいて検索できます。 詳細については、[プロビジョニング ログ(プレビュー)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) に関する記事を参照してください。 

プロビジョニング ログには、プロビジョニング サービスによって実行されたすべての操作が記録されます。これには、プロビジョニングの対象となる割り当て済みのユーザーを Azure AD で照会する操作、これらのユーザーが存在するかどうかを対象となるアプリで照会する操作、システム間でユーザー オブジェクトを比較する操作が含まれます。 その後、比較に基づいて、対象のシステムのユーザー アカウントを追加または更新するか、無効にします。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>プロビジョニングに関して考慮すべき一般的問題
次の一覧は、どこから始めるべきかわかっている場合に詳しく確認できる一般的問題を示しています。

- [プロビジョニング サービスが開始されない](#provisioning-service-does-not-appear-to-start)
- [ユーザーを割り当てたにもかかわらず、ユーザーがスキップされておりプロビジョニングされていないとプロビジョニング ログに表示される](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>プロビジョニング サービスが開始されない
Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [プロビジョニング]** セクションで、 **[Provisioning Status (プロビジョニングの状態)]** を**オン**にしているとします。 しかし、その後再読み込みしても、他の状態の詳細がページに表示されません。サービスは実行されていますが、初回サイクルがまだ完了していない可能性があります。 前述の**プロビジョニング ログ (プレビュー)** を確認し、サービスによって実行されている操作と、エラーの有無を判断します。

>[!NOTE]
>初回サイクルには 20 分から数時間かかることがあります。この時間は Azure AD のディレクトリ サイズと、プロビジョニングの対象となるユーザーの数によって異なります。 初回サイクル後の両方のシステムの状態を表すウォーターマークがプロビジョニング サービスに保存されるため、初回サイクルの後の後続の同期はより速くなります。 初回サイクルにより、以降の同期のパフォーマンスが向上します。
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>ユーザーを割り当てたにもかかわらず、ユーザーがスキップされておりプロビジョニングされていないとプロビジョニング ログに表示される

プロビジョニング ログにユーザーが "スキップ" されていると表示される場合、ログの **[ステップ]** タブを確認して原因を判断することが重要です。 一般的な原因と解決策を次に示します。

- **属性値に基づいてユーザーをフィルター処理する** **スコープ フィルターが構成されています**。 スコープ フィルターの詳細については、「[スコープ フィルター](define-conditional-rules-for-provisioning-user-accounts.md)」を参照してください。
- **ユーザーが "実質的に有効でない" 状態です**。 この特定のエラー メッセージが表示された場合、その原因は、Azure AD に格納されているユーザー割り当てレコードの問題です。 この問題を解決するには、アプリからユーザー (またはグループ) を割り当て解除し、再割り当てします。 割り当ての詳細については、[ユーザーまたはグループ アクセスの割り当て](assign-user-or-group-access-portal.md)に関するページを参照してください。
- **必須の属性が見つからないか、ユーザー用に設定されていません**。 プロビジョニングを設定するときに考慮すべき重要なことは、どのユーザー (またはグループ) プロパティが Azure AD からアプリケーションに提供されるかを定義する属性マッピングとワークフローを確認して構成することです。 この構成には、2 つのシステム間でユーザーまたはグループを一意に識別して照合するために使用される "照合プロパティ" の設定が含まれます。 この重要なプロセスの詳細については、「[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](customize-application-attributes.md)」を参照してください。
- **グループの属性マッピング:** 一部のアプリケーションでサポートされている場合は、メンバーだけでなくグループの名前と詳細もプロビジョニングします。 **[プロビジョニング]** タブに表示されるグループ オブジェクトの **[マッピング]** を有効化/無効化して、この機能を有効または無効にします。グループのプロビジョニングが有効になっている場合は、適切なフィールドが "照合 ID" に使用されていることを確かめるために、属性マッピングを必ず確認してください。 この照合 ID は、表示名または電子メール エイリアスである場合があります。 照合プロパティが空か、または Azure AD のグループ用に設定されていない場合、グループとそのメンバーはプロビジョニングされません。

## <a name="next-steps"></a>次の手順

[Azure AD Connect 同期:宣言型のプロビジョニングについて](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
