---
title: 基本グループを作成してメンバーを追加する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用した基本グループの作成方法に関する手順です。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8041656a11689d0cca64c3edae1af2e70409028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97027715"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Azure Active Directory を使用して基本グループを作成してメンバーを追加する
Azure Active Directory (Azure AD) ポータルを使用して、基本グループを作成できます。 この記事の目的に合わせて、基本グループは、リソース所有者 (管理者) によって単一リソースに追加され、そのリソースにアクセスする必要がある特定のメンバー (従業員) を含みます。 動的なメンバーシップやルールの作成など、より複雑なシナリオについては、「[Azure Active Directory のユーザー管理のドキュメント](../enterprise-users/index.yml)」を参照してください。

## <a name="group-and-membership-types"></a>グループとメンバーシップの種類
グループとメンバーシップの種類はいくつかあります。 グループの作成時に使用するオプションの決定に役立つように、各グループとメンバーシップの種類とその使用理由について以下に説明します。

### <a name="group-types"></a>グループの種類:
- **セキュリティ**。 メンバーを管理し、グループ ユーザーの共有リソースへのコンピューター アクセスを管理するために、使用されます。 たとえば、特定のセキュリティ ポリシーのセキュリティ グループを作成できます。 この方法で作成すると、すべてのメンバーに一連のアクセス許可を一度に付与でき、各メンバーにアクセス許可を個別に追加する必要はありません。 セキュリティ グループには、ユーザー、デバイス、グループ、およびサービス プリンシパルをメンバーとして設定でき、ユーザーとサービス プリンシパルを所有者として設定できます。 リソースへのアクセス管理の詳細については、[Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)に関するページを参照してください。
- **Microsoft 365**。 共有メールボックス、カレンダー、ファイル、SharePoint サイトなどへのアクセスをメンバーに付与することで、共同作業の機会を提供します。 また、このオプションでは、組織外のユーザーにグループへのアクセス権を付与することもできます。 Microsoft 365 グループには、ユーザーのみをメンバーとして設定することができます。 ユーザーとサービス プリンシパルはどちらも、Microsoft 365 グループの所有者にすることができます。 Microsoft 365 グループの詳細については、「[Microsoft 365 グループの概要](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2)」を参照してください。

### <a name="membership-types"></a>メンバーシップの種類:
- **[割り当て済み]。** このグループのメンバーとなり、一意のアクセス許可を保持するように、特定のメンバーを追加できます。 この記事の目的に合わせて、このオプションを使用しています。
- **[動的ユーザー]。** 動的メンバーシップ ルールを使用して、メンバーを自動的に追加および削除できます。 メンバーの属性が変更されると、システムは、ディレクトリの動的なグループ ルールを確認して、そのメンバーがルール要件を満たしているか (追加される)、またはルール要件を満たさなくなったか (削除される) を判定します。
- **[動的デバイス]。** 動的なグループ ルールを使用して、自動的にデバイスを追加および削除できます。 デバイスの属性が変更されると、システムは、ディレクトリの動的なグループ ルールを確認して、そのデバイスがルール要件を満たしているか (追加される)、またはルール要件を満たさなくなったか (削除される) を判定します。

    > [!IMPORTANT]
    > デバイスまたはユーザーのどちらかに対して動的グループを作成することは可能ですが、両方に対して作成することはできません。 また、デバイス所有者の属性に基づいてデバイス グループを作成することはできません。 デバイス メンバーシップ ルールで参照できるのは、デバイスの属性のみです。 ユーザーとデバイスの動的グループの作成に関する詳細については、[動的グループの作成と状態チェックを行う](../enterprise-users/groups-create-rule.md)に関する記事をご覧ください。

## <a name="create-a-basic-group-and-add-members"></a>基本グループを作成してメンバーを追加する
基本グループを作成し、それと同時にメンバーを追加できます。 基本グループを作成してメンバーを追加するには、次の手順を使用します。

1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。

1. **Azure Active Directory** を検索して選択します。

1. **[Active Directory]** ページで、 **[グループ]** 、 **[新しいグループ]** の順に選択します。

    ![[グループ] が表示された Azure AD ページ](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. **[新しいグループ]** ペインが表示されるので、必要な情報を入力する必要があります。

    ![情報の例が入力されている新しいグループ ページ](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. 事前に定義された **[グループの種類]** を選択します。 グループの種類の詳細については、「[グループとメンバーシップの種類](#group-types)」を参照してください。

1. **[グループ名]** を作成して追加します。 覚えやすく、グループにとって意味のある名前を選びます。 名前が別のグループによって既に使用されているかどうかを判断するチェックが実行されます。 名前が既に使用されている場合は、重複した名前付けを避けるために、グループの名前を変更するように求められます。

1. グループに **[グループのメール アドレス]** を追加するか、自動的に入力されたメール アドレスをそのまま使用します。

1. **[グループの説明]。** 任意で、グループに説明を追加します。

1. 事前に定義された **[メンバーシップの種類]** を選択します (必須)。 メンバーシップの種類の詳細については、「[グループとメンバーシップの種類](#membership-types)」を参照してください。

1. **［作成］** を選択します グループが作成され、メンバーを追加する準備ができました。

1. **[グループ]** ページから **[メンバー]** 領域を選択して、 **[メンバーの選択]** ページからグループに追加するメンバーの検索を開始します。

    ![グループの作成プロセスの間に、グループのメンバーを選択する](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. メンバーの追加が完了したら、 **[選択]** をクリックします。

    **[グループの概要]** ページが更新され、グループに追加されたメンバー数が表示されるようになりました。

    ![[グループの概要] ページで、メンバー数が強調されている](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>グループ ウェルカム メールをオンまたはオフにする

メンバーシップが動的であるか静的であるかを問わず、新しい Microsoft 365 グループが作成されると、そのグループに追加されたすべてのユーザーにウェルカム通知が送信されます。 ユーザーまたはデバイスのいずれかの属性が変更されると、組織内のすべての動的グループ ルールが、潜在的なメンバーシップの変更のために処理されます。 追加されたユーザーは、ウェルカム通知も受け取ります。 この動作は、[Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup) を使用して無効にすることができます。 

## <a name="next-steps"></a>次のステップ

- [グループを使用して SaaS アプリへのアクセスを管理する](../enterprise-users/groups-saasapps.md)
- [PowerShell コマンドを使用してグループを管理する](../enterprise-users/groups-settings-v2-cmdlets.md)
