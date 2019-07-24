---
title: ライセンスの割り当てと削除 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory ライセンスをユーザーまたはグループに割り当てたり、それらのライセンスを削除したりする手順を紹介します。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e7a3f80067adb3093bd27e34a45b3afd72b4993
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286858"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Azure Active Directory ポータルを使用したライセンスの割り当てと削除
多くの Azure Active Directory (Azure AD) サービスでは、Azure AD 製品をアクティブ化し、ユーザーまたはグループ (および関連するメンバー) ごとにその製品のライセンスを付与する必要があります。 アクティブなライセンスを持つユーザーのみが、ライセンスされている Azure AD サービスにアクセスおよび使用できるようになります。

## <a name="available-product-editions"></a>使用可能な製品エディション
Azure AD 製品に利用できるいくつかのエディションがあります。

- Azure AD Free

- Azure AD Basic

- Azure AD Premium 1 (Azure AD P1)

- Azure AD Premium 2 (Azure AD P2)

各製品エディションと関連するライセンスの詳細に関する固有の情報については、「[必要なライセンス](../authentication/concept-sspr-licensing.md)」をご覧ください。

## <a name="view-your-product-edition-and-license-details"></a>ご利用の製品エディションとライセンスの詳細を表示する
保留中の有効期限および利用可能な割り当て数を確認して、個別のライセンスなどの使用可能な製品を表示できます。

### <a name="to-find-your-product-and-license-details"></a>ご利用の製品とライセンスの詳細を確認するには
1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。

2. **[Azure Active Directory]** を選択し、**[ライセンス]** を選択します。

    **[ライセンス]** ページが表示されます。

    ![購入した製品数と割り当てられたライセンスを示す [ライセンス] ページ](media/license-users-groups/license-details-blade.png)
    
3. **購入した製品**のリンクを選択して **[製品]** ページを表示し、特定の製品エディションごとに **[割り当て済み]**、**[利用可能]**、**[まもなく有効期限切れ]** の詳細情報を確認します。

    ![製品エディションと関連するライセンス情報を含む [製品] ページ](media/license-users-groups/license-products-blade-with-products.png)

4. 製品エディション名を選択して、そのライセンスされているユーザーとグループを確認します。

## <a name="assign-licenses-to-users-or-groups"></a>ユーザーまたはグループにライセンスを割り当てる
ライセンスされている Azure AD サービスを使用する必要がある任意のユーザーに適切なライセンスがあることを確認します。 個別のユーザーまたはグループ全体にライセンスの権限を追加するかどうかの判断はお客様にお任せします。

>[!Note]
>グループベースのライセンスは Azure AD のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランに付属します。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。<br><br>ユーザーを追加する方法に関する詳細については、「[クイック スタート: Azure Active Directory に新しいユーザーを追加する](add-users-azure-active-directory.md)」をご覧ください。 グループを作成してメンバーを追加する方法に関する詳細については、「[基本グループを作成してメンバーを追加する](active-directory-groups-create-azure-portal.md)」を参照してください。

### <a name="to-assign-a-license-to-a-specific-user"></a>ライセンスを特定のユーザーに割り当てるには
1. **[製品]** ページで、ユーザーに割り当てる必要があるエディション名を選択します。 たとえば、_Azure Active Directory Premium プラン 2_ です。

    ![製品エディションが強調表示されている [製品] ページ](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. **[Azure Active Directory Premium プラン 2]** ページで、**[割り当て]** を選択します。

    ![[割り当て] オプションが強調表示されている [製品] ページ](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. **[割り当て]** ページで **[ユーザーとグループ]** を選択し、ライセンスを割り当てるユーザーを検索して選択します。 たとえば、_Mary Parker_ などです。

    ![検索と [選択] オプションが強調表示されている [ライセンスの割り当て] ページ](media/license-users-groups/assign-license-blade-with-highlight.png)

4. **[割り当てオプション]** を選択して、適切なライセンス オプションをオンにしていることを確認し、**[OK]** を選択します。

    ![エディションで利用可能なすべてのオプションを示している [ライセンス オプション] ページ](media/license-users-groups/license-option-blade-assignments.png)

    **[ライセンスの割り当て]** ページが更新され、ユーザーが選択され、割り当てが構成されていることが示されます。

    >[!NOTE]
    >すべての Microsoft サービスが、すべての場所で利用できるわけではありません。 ライセンスをユーザーに割り当てる前に、お客様は **[利用場所]** を指定しておく必要があります。 この値は Azure AD の **[Azure Active Directory] &gt; [ユーザー] &gt; [プロファイル] &gt; [設定]** 領域で設定できます。

5. **[割り当て]** を選択します。

    ユーザーがライセンスされているユーザーの一覧に追加され、含まれる Azure AD サービスにアクセスできます。

### <a name="to-assign-a-license-to-an-entire-group"></a>ライセンスをグループ全体に割り当てるには
1. **[製品]** ページで、ユーザーに割り当てる必要があるエディション名を選択します。 たとえば、_Azure Active Directory Premium プラン 2_ です。

    ![製品エディションが強調表示されている [製品] ブレード](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. **[Azure Active Directory Premium プラン 2]** ページで、**[割り当て]** を選択します。

    ![[割り当て] オプションが強調表示されている [製品] ページ](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. **[割り当て]** ページで **[ユーザーとグループ]** を選択し、ライセンスを割り当てるグループを検索して選択します。 たとえば、_[MDM policy - West]\(MDM ポリシー - 西部\)_ です。

    ![検索と [選択] オプションが強調表示されている [ライセンスの割り当て] ページ](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. **[割り当てオプション]** を選択して、適切なライセンス オプションをオンにしていることを確認し、**[OK]** を選択します。

    ![エディションで利用可能なすべてのオプションを示している [ライセンス オプション] ページ](media/license-users-groups/license-option-blade-group-assignments.png)

    **[ライセンスの割り当て]** ページが更新され、ユーザーが選択され、割り当てが構成されていることが示されます。

    >[!NOTE]
    >すべての Microsoft サービスが、すべての場所で利用できるわけではありません。 ライセンスをグループに割り当てる前に、お客様はすべてのメンバーに対して **[利用場所]** を指定しておく必要があります。 この値は Azure AD の **[Azure Active Directory] &gt; [ユーザー] &gt; [プロファイル] &gt; [設定]** 領域で設定できます。 利用場所が指定されていないユーザーは、テナントの場所を継承します。

5. **[割り当て]** を選択します。

    グループはライセンスされているグループのリストに追加され、そのすべてのメンバーが含まれる Azure AD サービスにアクセスできます。

## <a name="remove-a-license"></a>ライセンスを削除する
**[ライセンス]** ページからユーザーまたはグループのいずれかからライセンスを削除できます。

### <a name="to-remove-a-license-from-a-specific-user"></a>特定のユーザーからライセンスを削除するには
1. 製品エディションの **[ライセンスされているユーザー]** ページで、ライセンスが必要なくなったユーザーを選択します。 たとえば、_Alain Charon_ にします。

2. **[ライセンスを削除する]** を選択します。

    ![[ライセンスを削除する] オプションが強調表示されている [ライセンスされているユーザー] ページ](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>グループからライセンスを削除するには
1. 製品エディションの **[ライセンスされているグループ]** ページで、ライセンスが必要なくなったグループを選択します。 たとえば、_[MDM policy - West]\(MDM ポリシー - 西部\)_ です。

2. **[ライセンスを削除する]** を選択します。

    ![[ライセンスを削除する] が強調表示されている [ライセンスされているグループ] ページ](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>ユーザーがグループから継承したライセンスを直接削除することはできません。 代わりに、ライセンスを継承したグループからユーザーを削除する必要があります。

## <a name="next-steps"></a>次の手順
ライセンスを割り当てた後、次のプロセスを実行できます。

- [ライセンスの割り当てに関する問題を特定して解決する](../users-groups-roles/licensing-groups-resolve-problems.md)

- [ライセンス付与の目的でライセンスされているユーザーをグループに追加する](../users-groups-roles/licensing-groups-migrate-users.md)

- [Azure Active Directory のライセンス管理にグループを使用する際のシナリオ、制限、および既知の問題](../users-groups-roles/licensing-group-advanced.md)

- [ユーザー プロファイル情報の追加または変更](active-directory-users-profile-azure-portal.md)
