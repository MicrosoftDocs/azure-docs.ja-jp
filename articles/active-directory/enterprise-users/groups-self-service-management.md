---
title: セルフサービス グループ管理の設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory にセキュリティ グループまたは Microsoft 365 グループを作成して管理したり、セキュリティ グループまたは Microsoft 365 グループのメンバーシップを要求したりすることができます
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b2b8e3374c362f937aa5cfe106e8da9f9aa39f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548003"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Azure Active Directory でのセルフサービス グループ管理の設定 

Azure Active Directory (Azure AD) では、独自のセキュリティ グループまたは Microsoft 365 グループを作成して管理することができます。 グループの所有者は、メンバーシップ要求を承認または拒否できます。また、グループ メンバーシップの制御を委任できます。 セルフサービスによるグループ管理機能は、メールを有効にしたセキュリティ グループまたは配布リストでは使用できません。

## <a name="self-service-group-membership-defaults"></a>セルフ サービス グループ メンバーシップの既定値

Azure portal で、または Azure AD PowerShell を使用してセキュリティ グループが作成された場合、グループの所有者のみがメンバーシップを更新できます。 所有者による承認か自動承認かにかかわらず、[アクセス パネル](https://account.activedirectory.windowsazure.com/r#/joinGroups)でセルフサービスにより作成されたセキュリティ グループおよびすべての Microsoft 365 グループにはすべてのユーザーが参加できます。 アクセス パネルでは、グループを作成するときにメンバーシップ オプションを変更できます。

グループの作成場所 | セキュリティ グループの既定の動作 | Microsoft 365 グループの既定の動作
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | メンバーを追加できるのは所有者のみです<br>アクセス パネルには表示されますが、参加できません | すべてのユーザーが参加できます
[Azure Portal](https://portal.azure.com) | メンバーを追加できるのは所有者のみです<br>アクセス パネルには表示されますが、参加できません<br>グループ 作成時に所有者は自動的に割り当てられません | すべてのユーザーが参加できます
[アクセス パネル](https://account.activedirectory.windowsazure.com/r#/joinGroups) | すべてのユーザーが参加できます<br>グループの作成時に、メンバーシップ オプションを変更できます | すべてのユーザーが参加できます<br>グループの作成時に、メンバーシップ オプションを変更できます

## <a name="self-service-group-management-scenarios"></a>セルフサービス グループ管理のシナリオ

* **グループ管理の委任** 管理者が、社内で利用している SaaS アプリケーションへのアクセスを管理しているとします。 それらのアクセス権を管理する負担が大きくなってきたことから、この管理者はビジネス オーナーに依頼して新しいグループを作成してもらいます。 管理者はアプリケーションへのアクセス権を新しいグループに割り当て、既にアプリケーションにアクセスしているすべてのユーザーをそのグループに追加します。 これ以降はビジネス オーナーがユーザーをさらに追加できます。追加されたユーザーはアプリケーションへと自動的にプロビジョニングされます。 ビジネス オーナーは、管理者によるユーザーのアクセス管理が行われるまで待つ必要がありません。 管理者が同じ権限を別のビジネス グループのマネージャーに付与した場合、その人物も自分のグループ メンバーのアクセス管理ができるようになります。 ビジネス オーナーもマネージャーも、互いのグループ メンバーを表示または管理することはできません。 管理者は依然として、そのアプリケーションへのアクセス権を持ったすべてのユーザーを表示でき、必要に応じてアクセス権をブロックすることができます。
* **グループ管理のセルフサービス化** たとえば、2 人のユーザーが別々に SharePoint Online サイトを設定しているとします。 この 2 人のユーザーは、自分のサイトへのアクセス権を互いのチームに付与しようとしています。 これを実現するには、Azure AD に 1 つのグループを作成し、SharePoint Online で両者がそのグループを選択して、それぞれのサイトへのアクセス権を付与します。 アクセス権を必要とする人がいれば、その人物がアクセス パネルからアクセス権を要求します。承認が下りればその人物は自動的に、両方の SharePoint Online サイトにアクセスできるようになります。 後日、2 人のうち一方が、サイトにアクセスしているすべてのユーザーに、特定の SaaS アプリケーションへのアクセス権も付与することに決めたとします。 SaaS アプリケーションの管理者は、アプリケーションによる SharePoint Online サイトへのアクセス権を追加することができます。 それ以後は、要求が承認されると、2 つの SharePoint Online サイトへのアクセス権に加え、この SaaS アプリケーションへのアクセス権も付与されるようになります。

## <a name="make-a-group-available-for-user-self-service"></a>グループをユーザーのセルフ サービスに使用できるようにする

1. ディレクトリのグローバル管理者のアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選択し、 **[全般]** 設定を選択します。
1. **[所有者はアクセス パネルでのグループ メンバーシップの要求を管理できる]** を **[はい]** に設定します。
1. **[アクセス パネルでのグループへのアクセスを制限する]** を **[いいえ]** に設定します。
1. **[ユーザーは Azure Portal でセキュリティ グループを作成できる]** または **[ユーザーは Azure Portal で Microsoft 365 グループを作成することができる]** を次の値に設定した場合

    - **[はい]** :Azure AD 組織内のすべてのユーザーが、新しいセキュリティ グループを作成したりそれらのグループにメンバーを追加したりできるようになります。 これらの新しいグループは、他のすべてのユーザーのアクセス パネルにも表示されるようになります。 グループのポリシー設定で許可されている場合、他のユーザーはこれらのグループへの参加要求を作成できます
    - **[いいえ]** :ユーザーはグループを作成できず、所有している既存のグループを変更することもできません。 ただし、それらのグループのメンバーシップの管理と、他のユーザーからのグループへの参加要求の承認は、引き続き行うことができます。

また、**Azure portal でメンバーをグループの所有者として割り当てることができる所有者** を使用して、ユーザーのセルフサービスのグループ管理よりも詳細なアクセス制御を実現することもできます。

ユーザーがグループを作成できる場合、自分の組織内のすべてのユーザーが、新しいグループを作成でき、既定の所有者としてそれらのグループにメンバーを追加できるようになります。 独自のグループを作成できる個人を指定することはできません。 個人を指定できるのは、別のグループ メンバーをグループの所有者にする場合のみです。

> [!NOTE]
> ユーザーが セキュリティ グループまたは Microsoft 365 グループへの参加を要求したり、所有者がメンバーシップ要求を承認または拒否するように要求するには、Azure Active Directory Premium (P1 または P2) ライセンスが必要です。 Azure Active Directory Premium ライセンスがない場合でも、ユーザーはアクセス パネルでグループを管理できますが、アクセス パネルで所有者の承認を必要とするグループを作成することはできません。また、グループに参加するように要求することもできません。

## <a name="next-steps"></a>次のステップ

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory のグループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-cmdlets.md)
* [Azure Active Directory のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [Azure Active Directory とは](../fundamentals/active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)
