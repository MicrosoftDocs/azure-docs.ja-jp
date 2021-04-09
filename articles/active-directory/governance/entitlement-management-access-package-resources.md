---
title: Azure AD エンタイトルメント管理でアクセス パッケージのリソース ロールを変更する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理で既存のアクセス パッケージのリソース ロールを変更する方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f69cf492ec3e28d7f4aa86971dc6c91b34bdf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644181"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージのリソース ロールを変更する

アクセス パッケージ マネージャーは、新規のリソースへのユーザーのアクセスのプロビジョニングや以前のリソースからのアクセスの削除について心配することなく、アクセス パッケージ内のリソースをいつでも変更できます。 この記事では、既存のアクセス パッケージのリソース ロールを変更する方法について説明します。

このビデオでは、アクセス パッケージを変更する方法の概要について説明しています。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>リソースのカタログの確認

アクセス パッケージにリソースを追加する必要がある場合は、必要なリソースがカタログで利用可能かどうかを確認する必要があります。 アクセス パッケージ マネージャーの場合は、所有していても、カタログにリソースを追加することはできません。 カタログで利用可能なリソースの使用に制限されます。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、カタログを開きます。

1. 左側のメニューで、 **[リソース]** をクリックして、このカタログ内のリソースの一覧を表示します。

    ![カタログ内のリソースの一覧](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. あなたがアクセス パッケージ マネージャーで、カタログにリソースを追加する必要がある場合は、カタログ所有者にそれらを追加するよう依頼できます。

## <a name="add-resource-roles"></a>リソース ロールを追加する

リソース ロールは、リソースに関連付けられたアクセス許可を集めたものです。 ユーザーがリソースを要求できるようにする方法は、アクセス パッケージにリソース ロールを追加することです。 グループ、チーム、アプリケーション、SharePoint サイトのリソース ロールを追加できます。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[リソース ロール]** をクリックします。

1. **[リソース ロールの追加]** をクリックして、[リソースのロールをアクセス パッケージに追加する] ページを開きます。

    ![アクセス パッケージ - リソース ロールの追加](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. リソース ロールを追加するのがグループ、チーム、アプリケーション、SharePoint サイトのどれなのかに応じて、次のいずれかのセクションの手順を行います。

## <a name="add-a-group-or-team-resource-role"></a>グループまたはチームのリソース ロールを追加する

ユーザーにアクセス パッケージが割り当てられているときは、エンタイトルメント管理で自動的にユーザーをグループまたは Microsoft Teams のチームに追加することができます。 

- グループまたはチームがアクセス パッケージに含まれている場合、そのアクセス パッケージに割り当てられているユーザーは、まだ存在しなければ、そのグループまたはチームに追加されます。
- ユーザーのアクセス パッケージの割り当てが期限切れになると、ユーザーはグループまたはチームから削除されます。ただし、現時点で同じグループまたはチームが含まれる別のアクセス パッケージへの割り当てがある場合は削除されません。

任意の [Azure AD セキュリティ グループまたは Microsoft 365 グループ](../fundamentals/active-directory-groups-create-azure-portal.md)を選択できます。 管理者はカタログにグループを追加することができます。カタログ所有者は、グループの所有者である場合は、カタログにグループを追加することができます。 グループを選択する際は、次の Azure AD の制約に注意してください。

- メンバーとしてグループまたはチームに追加されたユーザー (ゲストを含む) は、そのグループまたはチームの他のすべてのメンバーを表示できます。
- Azure AD では、Azure AD Connect を使用して Windows Server Active Directory から同期されたグループまたは、Exchange Online で配布グループとして作成されたグループのメンバーシップを変更できません。  
- 動的グループのメンバーシップはメンバーの追加または削除によって更新することはできないため、動的グループのメンバーシップはエンタイトルメント管理での使用には適していません。

詳細については、「[グループを比較する](/office365/admin/create-groups/compare-groups)」と「[Microsoft 365 グループおよび Microsoft Teams](/microsoftteams/office-365-groups)」を参照してください。

1. **[リソースのロールをアクセス パッケージに追加する]** ページで、 **[Groups and Teams]\(Groups と Teams\)** をクリックして [グループの選択] ウィンドウを開きます。

1. アクセス パッケージに含めるグループとチームを選択します。

    ![アクセス パッケージ - リソース ロールの追加 - グループの選択](./media/entitlement-management-access-package-resources/group-select.png)

1. **[選択]** をクリックします。

    グループまたはチームを選択すると、 **[サブ タイプ]** 列に次のいずれかのサブタイプが表示されます。

    | サブタイプ | 説明 |
    | --- | --- |
    | セキュリティ | リソースにアクセスを付与するために使用されます。 |
    | Distribution | ユーザー グループに通知を送信するために使用されます。 |
    | Microsoft 365 | Teams が有効になっていない Microsoft 365 グループ。 社内と社外の両方でユーザー間の共同作業に使用されます。 |
    | チーム | Teams が有効になっている Microsoft 365 グループ。 社内と社外の両方でユーザー間の共同作業に使用されます。 |

1. **[ロール]** 一覧で **[所有者]** または **[メンバー]** を選択します。

    通常は [メンバー] ロールを選択します。 所有者ロールを選択すると、ユーザーが他のメンバーまたは所有者を追加または削除できるようになります。

    ![アクセス パッケージ - グループまたはチームのリソース ロールの追加](./media/entitlement-management-access-package-resources/group-role.png)

1. **[追加]** をクリックします。

    追加されると、アクセス パッケージへの既存の割り当てがあるユーザーは、自動的にこのグループまたはチームのメンバーになります。

## <a name="add-an-application-resource-role"></a>アプリケーションのリソース ロールを追加する

ユーザーにアクセス パッケージが割り当てられている場合は、Azure AD で、Azure AD エンタープライズ アプリケーション (SaaS アプリケーションと、Azure AD との間で認証連携された組織のアプリケーションの両方を含む) へのアクセスを、ユーザーに自動的に割り当てることができます。 フェデレーション シングル サインオンを使用して Azure AD と統合されるアプリケーションでは、Azure AD で、アプリケーションに割り当てられたユーザーに対するフェデレーション トークンが発行されます。

アプリケーションには複数のロールを割り当てることができます。 アプリケーションをアクセス パッケージに追加する場合、そのアプリケーションに複数のロールがある場合は、それらのユーザーに適切なロールを指定する必要があります。 アプリケーションを開発している場合、これらのロールをアプリケーションに追加する方法の詳細については、「[方法:エンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する](../develop/active-directory-enterprise-app-role-management.md)」を参照してください。

アプリケーション ロールがアクセス パッケージに含められたら、次のようになります。

- ユーザーがそのアクセス パッケージに割り当てられると、まだ存在しない場合は、ユーザーがそのグループに追加されます。
- ユーザーのアクセス パッケージの割り当てが期限切れになると、ユーザーがそのアプリケーション ロールが含まれている別のアクセス パッケージへの割り当てを持っている場合を除き、ユーザーのアクセス権はそのアプリケーションから削除されます。

アプリケーションを選択する際は、次の点を考慮します。

- アプリケーションは、グループもそのロールに割り当てられている場合もあります。  アクセス パッケージ内のアプリケーション ロールの代わりにグループを追加することができます。ただし、そのアプリケーションは、ユーザーにはマイ アクセス ポータルでアクセス パッケージの一部として表示されません。

1. **[リソースのロールをアクセス パッケージに追加する]** ページで、 **[アプリケーション]** をクリックして [Select applications]\(アプリケーションの選択) ウィンドウを開きます。

1. アクセス パッケージに含めるアプリケーションを選択します。

    ![アクセス パッケージ - リソース ロールの追加 - アプリケーションの選択](./media/entitlement-management-access-package-resources/application-select.png)

1. **[選択]** をクリックします。

1. **[ロール]** 一覧でアプリケーション ロールを選択します。

    ![アクセス パッケージ - アプリケーションのリソース ロールの追加](./media/entitlement-management-access-package-resources/application-role.png)

1. **[追加]** をクリックします。

    アクセス パッケージへの既存の割り当てがあるユーザーは、追加されると自動的にこのアプリケーションへのアクセス権が付与されます。

## <a name="add-a-sharepoint-site-resource-role"></a>SharePoint サイトのリソース ロールを追加する

Azure AD では、ユーザーにアクセス パッケージが割り当てられたときに、そのユーザーに自動的に SharePoint Online サイトまたは SharePoint Online のサイト コレクションへのアクセス権を割り当てられることができます。

1. **[リソースのロールをアクセス パッケージに追加する]** ページで、 **[SharePoint サイト]** をクリックして [Select SharePoint Online sites]\(SharePoint Online サイトの選択) ウィンドウを開きます。

    :::image type="content" source="media/entitlement-management-access-package-resources/resource-sharepoint-add.png" alt-text="アクセス パッケージ - リソース ロールの追加 - SharePoint サイトの選択 - ポータル ビュー":::

1. アクセス パッケージに含める SharePoint Online サイトを選択します。

    ![アクセス パッケージ - リソース ロールの追加 - SharePoint Online サイトの選択](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. **[選択]** をクリックします。

1. **[ロール]** の一覧で、SharePoint Online サイトのロールを選択します。

    ![アクセス パッケージ - SharePoint Online サイトのリソース ロールの追加](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. **[追加]** をクリックします。

    アクセス パッケージへの既存の割り当てがあるユーザーは、追加されると自動的に SharePoint Online サイトへのアクセス権が付与されます。

## <a name="remove-resource-roles"></a>リソース ロールを削除する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[リソース ロール]** をクリックします。

1. リソース ロールの一覧で、削除するリソース ロールを見つけます。

1. 省略記号 ( **...** ) をクリックして **[Remove resource role]\(リソース ロールの削除)** をクリックします。

    アクセス パッケージへの既存の割り当てがあるユーザーは、削除されると自動的にこのリソース ロールへのアクセス権が取り消されます。

## <a name="when-changes-are-applied"></a>変更が適用される場合

エンタイトルメント管理では、Azure AD で、アクセス パッケージ内の割り当てとリソースの一括変更が 1 日に数回処理されます。 そのため、割り当てを行ったりアクセス パッケージのリソース ロールを変更したりする場合は、Azure AD でその変更を行うのに最大 24 時間かかり、それに加えてそれらの変更を他の Microsoft Online Services や接続されている SaaS アプリケーションに伝達する時間がかかる可能性があります。 変更がほんの一部のオブジェクトにしか影響しない場合は、変更が Azure AD で適用された後、Azure AD の他のコンポーネントがその変更を検出して SaaS アプリケーションを更新するのに数分しかかからない可能性があります。 変更が何千ものオブジェクトに影響する場合、変更にはさらに長い時間がかかります。 たとえば、2 つのアプリケーションと 100 のユーザー割り当てを含むアクセス パッケージがあり、そのアクセス パッケージに SharePoint サイトのロールを追加する場合は、すべてのユーザーがその SharePoint サイトのロールに含められるまで遅延が発生する場合があります。 Azure AD 監査ログ、Azure AD プロビジョニング ログ、および SharePoint サイトの監査ログで、進行状況を監視することができます。

チームのメンバーを削除すると、Microsoft 365 グループからも削除されます。 チームのチャット機能から削除されるタイミングは遅れる場合があります。 詳細については、「[グループ メンバーシップ](/microsoftteams/office-365-groups#group-membership)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../fundamentals/active-directory-groups-create-azure-portal.md)
- [方法: エンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する](../develop/active-directory-enterprise-app-role-management.md)
- [SharePoint Online の概要](/sharepoint/introduction)