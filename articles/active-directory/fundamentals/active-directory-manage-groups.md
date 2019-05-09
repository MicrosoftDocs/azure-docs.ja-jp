---
title: グループを使用したアプリとリソース アクセスの管理 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory グループを使用して、組織のクラウドベースのアプリ、オンプレミスのアプリ、およびリソースに対するアクセスを管理する方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c2bffad4254815d2ed51d9bbc14b32a521b5537
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193868"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Azure Active Directory グループを使用したアプリとリソース アクセスの管理
Azure Active Directory (Azure AD) は、組織のグループを使用した、クラウド ベースのアプリ、オンプレミスのアプリ、およびリソースの管理に役立ちます。 リソースは、ディレクトリ内のロールによってオブジェクトを管理するアクセス許可のように、ディレクトリの一部となっている場合と、サービスとしてのソフトウェア (SaaS) アプリ、Azure サービス、SharePoint サイト、オンプレミスのリソースのように、ディレクトリの外部リソースである場合があります。

>[!NOTE]
>Azure Active Directory を使用するには、Azure のアカウントが必要です。 アカウントを持っていない場合は、 [無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)できます。

## <a name="how-does-access-management-in-azure-ad-work"></a>Azure AD でのアクセス管理のしくみ
Azure AD では、1 人のユーザーまたは Azure AD のグループ全体にアクセス権を提供することで、組織のリソースに対するアクセスをより容易に付与できるようにしています。 グループの使用によって、リソース所有者 (または Azure AD ディレクトリの所有者) は、各人に権限を提供する必要なしに、グループのすべてのメンバーにアクセス権限のセットを割り当てることができます。 リソースまたはディレクトリの所有者は、メンバー一覧の管理権限を、部門のマネージャーやヘルプ デスクの管理者などの他のユーザーに付与し、そのユーザーが必要に応じてメンバーの追加と削除を行えるようにすることもできます。 グループ所有者を管理する方法の詳細については、[グループ所有者の管理](active-directory-accessmanagement-managing-group-owners.md)に関するページを参照してください

![Azure Active Directory アクセス管理図](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>アクセス権を割り当てる方法
ユーザーにリソースへのアクセス権を割り当てる方法は 4 つあります。

- **直接割り当て。** リソース所有者は、ユーザーをリソースに直接割り当てます。

- **グループの割り当て。** リソース所有者は、Azure AD グループをリソースに割り当てます。これにより、グループ メンバー全員に、リソースへのアクセスが自動的に与えられます。 グループのメンバーシップは、グループ所有者とリソース所有者の両方によって管理され、どちらの所有者も、グループに対するメンバーの追加または削除を行えるようになります。 グループ メンバーの追加または削除に関する詳細については、[Azure Active Directory ポータルを使用して別のグループからグループを追加または削除する方法](active-directory-groups-membership-azure-portal.md)に関するページを参照してください。 

- **ルール ベースの割り当て。** リソース所有者は、グループを作成し、ルールを使用して、特定のリソースにどのユーザーが割り当てられるかを定義します。 ルールは、個々のユーザーに割り当てられている属性に基づきます。 リソース所有者は、リソースへのアクセスを許可するためにはどの属性と値が必要であるかを判断し、ルールを管理します。 詳細については、「[動的グループの作成と状態チェックを行う](../users-groups-roles/groups-create-rule.md)」を参照してください。

    この短いビデオでも、動的なグループの作成と使用に関する簡単な説明をご覧いただけます。

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **外部機関の割り当て。** アクセス権は、オンプレミスのディレクトリや SaaS アプリなど、外部のソースから付与されます。 この状況においては、リソース所有者がリソースへのアクセス権を提供するためのグループを割り当ててから、外部ソースがグループのメンバーを管理します。

   ![アクセス管理の概要図](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>ユーザーは、割り当てられることなくグループに参加できるのでしょうか。
グループ所有者は、ユーザーの割り当てを行う代わりに、参加する自分のグループをユーザーに見つけさせることができます。 所有者は、参加するユーザー全員を自動的に受け入れるようにも、承認を要求するようにもグループを設定できます。

ユーザーがグループへの参加を要求した後、要求はグループ所有者に転送されます。 それが必須の場合、所有者は要求を承認することができ、そのユーザーにグループ メンバーシップが通知されます。 ただし、複数の所有者がいて、いずれかが承認しない場合、ユーザーへの通知は行われますが、グループへのユーザーの追加は行われません。 ユーザーにグループへの参加を要求させる方法の詳細と手順については、[ユーザーがグループへの参加を要求できるような Azure AD の設定](../users-groups-roles/groups-self-service-management.md)に関するページを参照してください

## <a name="next-steps"></a>次の手順
グループを使用したアクセス管理の概要について少し学習したので、リソースとアプリの管理を開始します。

- [Azure Active Directory を使用して新しいグループを作成する](active-directory-groups-create-azure-portal.md)または [PowerShell コマンドレットを使用して新しいグループを作成して管理する](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [グループを使用して、統合された SaaS アプリに対するアクセス権を割り当てる](../users-groups-roles/groups-saasapps.md)

- [Azure AD Connect を使用して Azure に対してオンプレミス グループを同期する](../hybrid/whatis-hybrid-identity.md)
