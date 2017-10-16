---
title: "Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない | Microsoft Docs"
description: "Azure AD でユーザーをプロビジョニングできるように構成した Azure AD ギャラリー アプリケーションにユーザーが表示されないときの一般的な問題をトラブルシューティングする方法について説明します。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: 30db71c0706de1dcc0d48fb72fa5d62475a14ba7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない

アプリケーションで自動プロビジョニングが構成されると (アプリに接続するために Azure AD に提供されたアプリの資格情報が有効であることの確認など)、 ユーザーやグループがアプリにプロビジョニングされます。これは、次の情報に基づいて決定されます。

-   どのユーザーとグループがアプリケーションに**割り当てられている**か。 割り当ての詳細については、「[Azure Active Directory プレビューでエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)」を参照してください。

-   **属性マッピング**が有効になっているかどうか、および有効な属性を Azure AD からアプリに同期するように構成されているかどうか。 属性マッピングの詳細については、「[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)」を参照してください。

-   特定の属性値に基づいてユーザーをフィルター処理する**スコープ フィルター**が存在するかどうか。 スコープ フィルターの詳細については、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters)」を参照してください。

ユーザーがプロビジョニングされていないことを確認した場合、Azure AD の監査ログを確認し、特定のユーザーに関するログ エントリを検索します。

プロビジョニングの監査ログには、Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [監査ログ]** タブからアクセスできます。**[アカウント プロビジョニング]** カテゴリでログをフィルター処理すると、そのアプリのプロビジョニング イベントのみを表示できます。 属性マッピングでユーザー向けに構成された "照合 ID" に基づいてユーザーを検索できます。 たとえば、"ユーザー プリンシパル名" または "電子メール アドレス" を Azure AD 側で照合属性として構成した場合、プロビジョニングされていないユーザーは "audrey@contoso.com" という値を持つため、 監査ログで "audrey@contoso.com" を検索し、返されたエントリを確認します。

プロビジョニングの監査ログには、プロビジョニング サービスによって実行されたすべての操作が記録されます。これには、プロビジョニングの対象となる割り当て済みのユーザーを Azure AD で照会する操作、これらのユーザーが存在するかどうかを対象となるアプリで照会する操作、システム間でユーザー オブジェクトを比較する操作が含まれます。 その後、比較に基づいて、対象のシステムのユーザー アカウントを追加または更新するか、無効にします。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>プロビジョニングに関して考慮すべき一般的問題

次の一覧は、どこから始めるべきかわかっている場合に詳しく確認できる一般的問題を示しています。

* [プロビジョニング サービスが開始されない](#provisioning-service-does-not-appear-to-start)
* [ユーザーを割り当てたにもかかわらず、ユーザーがスキップされておりプロビジョニングされていないと監査ログに表示される](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>プロビジョニング サービスが開始されない

Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [プロビジョニング]** セクションで、**[Provisioning Status (プロビジョニングの状態)]** を**オン**にしているとします。 しかし、その後再読み込みしても、他の状態がページに表示されません。これは、サービスは実行されていますが、初回の同期がまだ完了していないことを示している可能性があります。 上で説明されている**監査ログ**を確認し、サービスによってどのような操作が実行されているのかと、エラーが発生していないかを判断します。

>[!NOTE]
>初回の同期には 20 分～数時間かかることがあります。この時間は Azure AD のディレクトリ サイズと、プロビジョニングの対象となるユーザーの数によって異なります。 初回の同期後の両方のシステムの状態を表すウォーターマークがプロビジョニング サービスに保存されるため、初回の同期後の後続の同期はより速くなります。これにより、後続の同期のパフォーマンスが向上します。
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>ユーザーを割り当てたにもかかわらず、ユーザーがスキップされておりプロビジョニングされていないと監査ログに表示される

監査ログでユーザーが "スキップ" されていると表示される場合、ログ メッセージで詳細を確認して原因を判断することが非常に重要です。 一般的な原因と解決策を次に示します。

-   **属性値に基づいてユーザーをフィルター処理する****スコープ フィルターが構成されています**。 スコープ フィルターの詳細については、<https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters> を参照してください。

-   **ユーザーが "実質的に有効でない" 状態です**。 この特定のエラー メッセージが表示された場合、その原因は、Azure AD に格納されているユーザー割り当てレコードの問題です。 この問題を解決するには、ユーザー (またはグループ) の割り当てをアプリから解除し、再割り当てを行います。 割り当ての詳細については、<https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal> を参照してください。

-   **必須の属性が見つからないか、ユーザー用に設定されていません**。 プロビジョニングを設定するときの重要な考慮事項の 1 つは、属性マッピングとワークフローを確認し、構成することです。これらは、どのユーザー (またはグループ) のプロパティが Azure AD からアプリケーションに適用されるかを定義します。 これには、2 つのシステム間でユーザーまたはグループを一意に識別して照合するために使用される "照合プロパティ" の設定も含まれます。 この重要なプロセスの詳細については、「[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)」を参照してください。

  * **グループの属性マッピング:** 一部のアプリケーションでサポートされている場合は、メンバーだけでなくグループの名前と詳細もプロビジョニングします。 **[プロビジョニング]** タブに表示されるグループ オブジェクトの **[マッピング]** を有効または無効にして、この機能を有効または無効にすることができます。グループのプロビジョニングが有効になっている場合は、適切なフィールドが "照合 ID" に使用されていることを確かめるために、属性マッピングを必ず確認してください。 照合プロパティが空か、Azure AD のグループ用に設定されていない場合は、グループとそのメンバーがプロビジョニングされないので、これが表示名または電子メール エイリアスとなる場合があります。

## <a name="next-steps"></a>次のステップ
[Azure AD Connect 同期: 宣言型のプロビジョニングについて](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

