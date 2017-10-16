---
title: "新しい Azure Stack テナント アカウントを Azure Active Directory に追加する | Microsoft Docs"
description: "Microsoft Azure Stack Development Kit のデプロイを行った後、少なくとも 1 つのテナント ユーザー アカウントを作成して、テナント ポータルを表示できるようにする必要があります。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 91d1c52c0abf14656e08b511e4f6c8041e319020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
*適用対象: Azure Stack 開発キット*

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>新しい Azure Stack テナント アカウントをAzure Active Directory に追加する
[Azure Stack Development Kit をデプロイ](azure-stack-run-powershell-script.md)した後は、テナント ポータルを操作し、オファーとプランをテストするためにテナント ユーザー アカウントが必要になります。 テナント アカウントを作成するには、[Azure Portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) または [PowerShell](#create-an-azure-stack-tenant-account-using-powershell) を使用します。

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Azure ポータルを使用して Azure Stack テナント アカウントを作成する
Azure ポータルを使用するには Azure サブスクリプションが必要です。

1. [Azure](http://manage.windowsazure.com) にログインします。
2. Microsoft Azure の左側のナビゲーション バーで、 **[Active Directory]**をクリックします。
3. ディレクトリ一覧で Azure Stack に使用するディレクトリをクリックするか、新しいディレクトリを作成します。
4. ディレクトリ ページで **[ユーザー]**をクリックします。
5. **[ユーザーの追加]**をクリックします。
6. **ユーザーの追加**ウィザードの **[ユーザーの種類]** 一覧で **[組織内の新しいユーザー]** を選択します。
7. **[ユーザー名]** ボックスに、ユーザーの名前を入力します。
8. **@** ボックスで、適切なエントリを選択します。
9. 次へ進む矢印をクリックします。
10. ウィザードの **[ユーザー プロファイル]** ページで、**[姓]**、**[名]**、**[表示名]** を入力します。
11. **[ロール]** 一覧で **[ユーザー]** を選択します。
12. 次へ進む矢印をクリックします。
13. **[一時パスワードの取得]** ページで、**[作成]** をクリックします。
14. **新しいパスワード**をコピーします。
15. 新しいアカウントで Microsoft Azure にログインします。 パスワードの変更を求められたら、変更します。
16. `https://portal.local.azurestack.external` に新しいアカウントでログインして、テナント ポータルを表示します。

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>PowerShell を使用して Azure Stack テナント アカウントを作成する
Azure サブスクリプションがない場合は、Azure Portal を使用してテナント ユーザー アカウントを追加できません。 その場合は、代わりに Windows PowerShell 用 Azure Active Directory モジュールを利用できます。

> [!NOTE]
> Microsoft アカウント (Live ID) を使用して Azure Stack Development Kit をデプロイしている場合は、AAD PowerShell を使用してテナント アカウントを作成できません。 
> 
> 

1. [IT プロフェッショナル 用 Microsoft Online Services サインイン アシスタント RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950) をインストールします。
2. [Windows PowerShell 用 Azure Active Directory Module (64 ビット版)](http://go.microsoft.com/fwlink/p/?linkid=236297) をインストールして開きます。
3. 次のコマンドレットを実行します。

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 新しいアカウントで Microsoft Azure にサインインします。 パスワードの変更を求められたら、変更します。
2. `https://portal.local.azurestack.external` に新しいアカウントでサインインして、テナント ポータルを表示します。

