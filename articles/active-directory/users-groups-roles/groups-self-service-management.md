---
title: セルフサービス グループ管理の設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory にセキュリティ グループまたは Office 365 グループを作成して管理したり、セキュリティ グループまたは Office 365 グループのメンバーシップを要求したりすることができます。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b860257fd1b3f0897152dc3d48bff0c7e1d3d994
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111329"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Azure Active Directory でのセルフサービス グループ管理の設定 

Azure Active Directory (Azure AD) では、独自のセキュリティ グループまたは Office 365 グループを作成して管理することができます。 グループの所有者は、メンバーシップ要求を承認または拒否できます。また、グループ メンバーシップの制御を委任できます。 セルフサービスによるグループ管理機能は、メールを有効にしたセキュリティ グループまたは配布リストでは使用できません。

## <a name="self-service-group-membership-defaults"></a>セルフ サービス グループ メンバーシップの既定値

Azure portal で、または Azure AD PowerShell を使用してセキュリティ グループが作成された場合、グループの所有者のみがメンバーシップを更新できます。 所有者による承認か自動承認かにかかわらず、[アクセス パネル](https://account.activedirectory.windowsazure.com/r#/joinGroups)で作成されたセキュリティ グループおよびすべての Office 365 グループにはすべてのユーザーが参加できます。 アクセス パネルでは、グループを作成するときにメンバーシップ オプションを変更できます。

グループの作成場所 | セキュリティ グループの既定の動作 | Office 365 グループの既定の動作
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | メンバーを追加できるのは所有者のみです<br>アクセス パネルには表示されますが、参加できません | すべてのユーザーが参加できます
[Azure Portal](https://portal.azure.com) | メンバーを追加できるのは所有者のみです<br>アクセス パネルには表示されますが、参加できません<br>グループ 作成時に所有者は自動的に割り当てられません | すべてのユーザーが参加できます
[アクセス パネル](https://account.activedirectory.windowsazure.com/r#/joinGroups) | すべてのユーザーが参加できます<br>グループの作成時に、メンバーシップ オプションを変更できます | すべてのユーザーが参加できます<br>グループの作成時に、メンバーシップ オプションを変更できます

## <a name="self-service-group-management-scenarios"></a>セルフサービス グループ管理のシナリオ

* **グループ管理の委任** 管理者が、社内で利用している SaaS アプリケーションへのアクセスを管理しているとします。 それらのアクセス権を管理する負担が大きくなってきたことから、この管理者はビジネス オーナーに依頼して新しいグループを作成してもらいます。 管理者はアプリケーションへのアクセス権を新しいグループに割り当て、既にアプリケーションにアクセスしているすべてのユーザーをそのグループに追加します。 これ以降はビジネス オーナーがユーザーをさらに追加できます。追加されたユーザーはアプリケーションへと自動的にプロビジョニングされます。 ビジネス オーナーは、管理者によるユーザーのアクセス管理が行われるまで待つ必要がありません。 管理者が同じ権限を別のビジネス グループのマネージャーに付与した場合、その人物も自分のグループ メンバーのアクセス管理ができるようになります。 ビジネス オーナーもマネージャーも、互いのグループ メンバーを表示または管理することはできません。 管理者は依然として、そのアプリケーションへのアクセス権を持ったすべてのユーザーを表示でき、必要に応じてアクセス権をブロックすることができます。
* **グループ管理のセルフサービス化** たとえば、2 人のユーザーが別々に SharePoint Online サイトを設定しているとします。 この 2 人のユーザーは、自分のサイトへのアクセス権を互いのチームに付与しようとしています。 これを実現するには、Azure AD に 1 つのグループを作成し、SharePoint Online で両者がそのグループを選択して、それぞれのサイトへのアクセス権を付与します。 アクセス権を必要とする人がいれば、その人物がアクセス パネルからアクセス権を要求します。承認が下りればその人物は自動的に、両方の SharePoint Online サイトにアクセスできるようになります。 後日、2 人のうち一方が、サイトにアクセスしているすべてのユーザーに、特定の SaaS アプリケーションへのアクセス権も付与することに決めたとします。 SaaS アプリケーションの管理者は、アプリケーションによる SharePoint Online サイトへのアクセス権を追加することができます。 それ以後は、要求が承認されると、2 つの SharePoint Online サイトへのアクセス権に加え、この SaaS アプリケーションへのアクセス権も付与されるようになります。

## <a name="make-a-group-available-for-user-self-service"></a>グループをユーザーのセルフ サービスに使用できるようにする

1. ディレクトリのグローバル管理者のアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. **[ユーザーとグループ]** を選択し、**[グループ設定]** を選択します。
3. **[セルフサービスによるグループ管理が有効になりました]** を **[はい]** に設定します。
4. **[ユーザーはセキュリティ グループを作成できます]** または **[ユーザーは Office 365 グループを作成できます]** を **[はい]** に設定します。
   * これらの設定を有効にすると、自分のディレクトリ内のすべてのユーザーが、新しいセキュリティ グループを作成したりそれらのグループにメンバーを追加したりできるようになります。 これらの新しいグループは、他のすべてのユーザーのアクセス パネルにも表示されるようになります。 グループのポリシー設定で許可されている場合、他のユーザーはこれらのグループへの参加要求を作成できます。 
   * これらの設定が無効になっていると、ユーザーはグループを作成できず、所有している既存のグループを変更することもできません。 ただし、それらのグループのメンバーシップの管理と、他のユーザーからのグループへの参加要求の承認は、引き続き行うことができます。

ユーザーによるセルフサービスのグループ管理は、**[セキュリティ グループを管理できるユーザー]** と **[Office 365 グループを管理できるユーザー]** を使用することで、さらに細かくアクセス制御を行うこともできます。 **[ユーザーはグループを作成できます]** を有効にすると、自分のテナント内のすべてのユーザーが、新しいグループを作成したりそれらのグループにメンバーを追加したりできるようになります。 独自のグループを作成できる個人を指定することはできません。 個人を指定できるのは、別のグループ メンバーをグループの所有者にする場合のみです。

**[セキュリティ グループのためのセルフサービスを使用できるユーザー]** と **[Office 365 グループを管理できるユーザー]** を **[はい]** に設定すると、自分のテナント内のすべてのユーザーが新しいグループを作成できます。

**[セキュリティ グループを管理できるグループ]** または **[Office 365 グループを管理できるグループ]** を使用して、特定のグループを 1 つ指定し、そのメンバーにセルフサービス機能の利用を許可することもできます。

## <a name="next-steps"></a>次の手順

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory のグループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](groups-settings-cmdlets.md)
* [Azure Active Directory のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [Azure Active Directory とは](../fundamentals/active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)
