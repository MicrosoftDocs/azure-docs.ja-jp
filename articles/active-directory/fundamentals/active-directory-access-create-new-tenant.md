---
title: クイックスタート - Azure AD にアクセスして新しいテナントを作成する - Azure AD
description: Azure Active Directory の検索方法と、組織の新しいテナントの作成方法に関する手順。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d6341aeb6db89d43ef887a3ae50c4439e3867e6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318609"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>クイック スタート:Azure Active Directory で新しいテナントを作成する
Azure Active Directory (Azure AD) ポータルを使用して、組織の新しいテナントの作成など、すべての管理タスクを実行できます。 

このクイック スタートでは、Azure portal と Azure Active Directory にアクセスする方法と、組織の基本的なテナントを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="create-a-new-tenant-for-your-organization"></a>組織の新しいテナントを作成する
Azure portal にサインインすると、組織の新しいテナントを作成できます。 新しいテナントは組織を表し、社内外のユーザー向けに特定インスタンスの Microsoft クラウド サービスを管理するために役立ちます。

### <a name="to-create-a-new-tenant"></a>新しいテナントを作成するには

1. 組織の [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure portal メニューから **[リソースの作成]** を選択します。  

    ![Azure Active Directory の [リソースの作成] ページ](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. **[ID]** を選択してから、 **[Azure Active Directory]** を選択します。

    **[ディレクトリの作成]** ページが表示されます。

    ![Azure Active Directory の作成ページ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  **[ディレクトリの作成]** ページで、次の情報を入力します。
    
    - **[組織名]** ボックスに「_Contoso_」と入力します。

    - **[初期ドメイン名]** ボックスに「_Contoso_」と入力します。

    - **[国またはリージョン]** ボックスは _[米国]_ オプションのままにします。

1. **［作成］** を選択します

新しいテナントは、ドメイン contoso.onmicrosoft.com で作成されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このアプリケーションを引き続き使用しない場合は、次の手順を使用してテナントを削除することができます。

- Azure portal で **[ディレクトリ + サブスクリプション]** フィルターを使用して、削除するディレクトリにサインインしていることを確認し、必要に応じてターゲット ディレクトリに切り替えます。
- **[Azure Active Directory]** を選択し、 **[Contoso - 概要]** ページで **[ディレクトリの削除]** を選択します。

    テナントとその関連情報は削除されます。

    ![概要ページ。[ディレクトリの削除] ボタンが強調表示されている](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>次のステップ
- 追加のドメイン名を変更または追加する方法については、[Azure Active Directory にカスタム ドメイン名を追加する方法](add-custom-domain.md)に関するページを参照してください。

- ユーザーの追加については、[新しいユーザーの追加または削除](add-users-azure-active-directory.md)に関するページを参照してください。

- グループとメンバーの追加については、[基本的なグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)に関するページを参照してください。

- 組織のアプリケーションとリソース アクセスの管理方法については、[Privileged Identity Management を使用したロールベースのアクセス](../../role-based-access-control/best-practices.md)と[条件付きのアクセス](../../role-based-access-control/conditional-access-azure-management.md)に関するページを参照してください。

- [基本的なライセンスの情報、用語、関連機能](active-directory-whatis.md)など、Azure AD について学習します。