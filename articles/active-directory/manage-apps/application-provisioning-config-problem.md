---
title: Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングの構成に関する問題 | Microsoft Docs
description: 既に Azure AD アプリケーション ギャラリーに一覧表示されているアプリケーションにユーザー プロビジョニングを構成するときの一般的な問題をトラブルシューティングする方法について説明します。
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
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf496e448cbc11b9e986ca3b58c956c4cd18a34e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113454"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングの構成に関する問題

アプリへの[自動プロビジョニング](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) (サポートされている場合) を構成するには、自動プロビジョニングができるように、特定の手順でアプリケーションを準備する必要があります。 その後、Azure Portal を使用して、ユーザー アカウントがアプリケーションと同期されるようにプロビジョニング サービスを構成できます。

必ず最初にそのアプリケーションのプロビジョニングの設定を取り上げた設定チュートリアルを探してください。 次に、その手順に従ってアプリと Azure AD の両方を構成し、プロビジョニングの接続を作成します。 アプリのチュートリアルの一覧については、「[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)」を参照してください。

## <a name="how-to-see-if-provisioning-is-working"></a>プロビジョニングが機能していることを確認する方法 

サービスが構成されると、次の 2 つの場所から、サービスの操作に関するほとんどの洞察を得ることができます。

-   **監査ログ** – プロビジョニングの監査ログには、プロビジョニング サービスによって実行されたすべての操作が記録されます。これには、プロビジョニングの対象となる割り当て済みのユーザーを Azure AD で照会する操作が含まれます。 システム間でユーザー オブジェクトを比較しながら、これらのユーザーが存在するかどうかを対象となるアプリで照会できます。 その後、比較に基づいて、対象のシステムのユーザー アカウントを追加または更新するか、無効にします。 プロビジョニングの監査ログには、Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [監査ログ]** タブからアクセスできます。**[アカウント プロビジョニング]** カテゴリでログをフィルター処理すると、そのアプリのプロビジョニング イベントのみを表示できます。

-   **プロビジョニングの状態** – 指定したアプリで実行された最新のプロビジョニングの概要は、**[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [プロビジョニング]** セクションで確認できます。これは画面下部のサービス設定の下にあります。 このセクションでは、現在どれだけのユーザー (またはグループ) が 2 つのシステム間で同期されているか、およびエラーが発生していないか、その概要がまとめられています。 エラーの詳細は、監査ログで確認できます。 プロビジョニングの状態は、Azure AD とアプリの間で初回の同期がすべて完了するまで設定されないことに注意してください。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>プロビジョニングに関して考慮すべき一般的問題

次の一覧は、どこから始めるべきかわかっている場合に詳しく確認できる一般的問題を示しています。

* [プロビジョニング サービスが開始されない](#provisioning-service-does-not-appear-to-start)
* アプリの資格情報が機能していないため、構成を保存できない
* [ユーザーを割り当てたにもかかわらず、ユーザーが "スキップ" されておりプロビジョニングされていないと監査ログに表示される](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>プロビジョニング サービスが開始されない

Azure Portal の **[Azure Active Directory] &gt; [Enterprise Apps (エンタープライズ アプリ)] &gt; \[アプリケーション名\] &gt; [プロビジョニング]** セクションで、**[Provisioning Status (プロビジョニングの状態)]** を**オン**にしているとします。 しかし、その後再読み込みしても、他の状態がページに表示されません。 これは、サービスは実行されていますが、初回の同期がまだ完了していないことを示している可能性があります。 上で説明されている**監査ログ**を確認し、サービスによってどのような操作が実行されているのかと、エラーが発生していないかを判断します。

>[!NOTE]
>初回の同期には 20 分～数時間かかることがあります。この時間は Azure AD のディレクトリ サイズと、プロビジョニングの対象となるユーザーの数によって異なります。 初回同期後に両方のシステムの状態を表すウォーターマークがプロビジョニング サービスに保存されるため、後続の同期はより速くなり、パフォーマンスが改善されます。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>アプリの資格情報が機能していないため、構成を保存できない

プロビジョニングを機能させるためには、そのアプリによって提供されるユーザー管理 API に Azure AD で接続するための有効な資格情報が必要です。 これらの資格情報が機能しない場合、またはどのようなものかわからない場合は、既に説明済みであるこのアプリを設定するためのチュートリアルを確認します。

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>ユーザーを割り当てたにもかかわらず、ユーザーがスキップされておりプロビジョニングされていないと監査ログに表示される

監査ログでユーザーが "スキップ" されていると表示される場合、ログ メッセージで詳細を確認して原因を判断することが非常に重要です。 一般的な原因と解決策を次に示します。

- **属性値に基づいてユーザーをフィルター処理する** **スコープ フィルターが構成されています**。 スコープ フィルターの詳細については、「<https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>」を参照してください。

- **ユーザーが "実質的に有効でない" 状態です**。 この特定のエラー メッセージが表示された場合、その原因は、Azure AD に格納されているユーザー割り当てレコードの問題です。 この問題を解決するには、ユーザー (またはグループ) の割り当てをアプリから解除し、再割り当てを行います。 割り当ての詳細については、<https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal> を参照してください。

- **必須の属性が見つからないか、ユーザー用に設定されていません**。 プロビジョニングを設定するときの重要な考慮事項の 1 つは、属性マッピングとワークフローを確認し、構成することです。これらは、どのユーザー (またはグループ) のプロパティが Azure AD からアプリケーションに適用されるかを定義します。 これには、2 つのシステム間でユーザーまたはグループを一意に識別して照合するために使用される "照合プロパティ" の設定も含まれます。 この重要なプロセスの詳細については、「<https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>」を参照してください。

  * **グループの属性マッピング:** 一部のアプリケーションでサポートされている場合は、メンバーだけでなくグループの名前と詳細もプロビジョニングします。 **[プロビジョニング]** タブに表示されるグループ オブジェクトの **[マッピング]** を有効化/無効化して、この機能を有効または無効にします。グループのプロビジョニングが有効になっている場合は、適切なフィールドが "照合 ID" に使用されていることを確かめるために、属性マッピングを必ず確認してください。 照合プロパティが空か、Azure AD のグループ用に設定されていない場合は、グループとそのメンバーがプロビジョニングされないので、これが表示名または電子メール エイリアスとなる場合があります。

## <a name="next-steps"></a>次の手順
[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)
