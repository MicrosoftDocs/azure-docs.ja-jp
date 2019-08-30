---
title: Azure のロールベースのアクセス制御 (RBAC) を使用してサポート要求を作成および管理するアクセス権を制御する | Microsoft Docs
description: Azure のロールベースのアクセス制御 (RBAC) を使用してサポート要求を作成および管理するアクセス権を制御する
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 3cf17f6e391608af9d17591a81c579a1db779a6a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967802"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure のロールベースのアクセス制御 (RBAC) を使用してサポート要求を作成および管理するアクセス権を制御する

[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) を使用すると、Azure のアクセス権をきめ細かく管理できます。
Azure Portal ([portal.azure.com](https://portal.azure.com)) でサポート要求を作成するときは、Azure の RBAC モデルを使用して、サポート要求を作成および管理できるユーザーを定義します。
アクセス権を付与するには、特定のスコープでユーザー、グループ、アプリケーションに適切な RBAC ロールを割り当てます。スコープには、サブスクリプション、リソース グループ、またはリソースを指定できます。

例を見てみましょう。サブスクリプション スコープで読み取りアクセス許可を持つリソース グループ所有者は、リソース グループに属するすべてのリソース (Web サイト、仮想マシン、サブネットなど) を管理できます。
ただし、仮想マシン リソースに対するサポート要求を作成しようとすると、次のエラーが発生します。

![サブスクリプション エラー](./media/create-manage-support-requests-using-access-control/subscription-error.png)

サポート要求の管理では、サポート要求を作成および管理できるように、書き込みアクセス許可、またはサブスクリプション スコープでサポート アクション (Microsoft.Support/*) を実行できるロールが必要です。

以下の記事では、Azure のカスタムのロールベースのアクセス制御 (RBAC) を使用して、Azure Portal でサポート要求を作成および管理する方法について説明します。

## <a name="getting-started"></a>Getting Started (概要)

前述の例では、リソース グループ所有者は、サブスクリプション所有者がサブスクリプションでカスタム RBAC ロールを割り当てれば、サポート要求を作成できます。
[カスタム RBAC ロール](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/)は、Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、および REST API を使用して作成できます。

カスタム ロールでアクセス権を付与する Azure の操作は、そのロールの actions プロパティで指定します。
サポート要求を管理するためのカスタム ロールを作成するには、ロールに Microsoft.Support/* アクションが必要です。

サポート要求の作成と管理に使用できるカスタム ロールの例を次に示します。
ここでは、このロールに "Support Request Contributor (サポート要求の共同作成者)" という名前を付けました。この記事のカスタム ロールにはこの名前を使用します。

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

サブスクリプションのカスタム ロールの作成方法については、[こちらのビデオ](https://www.youtube.com/watch?v=-PaBaDmfwKI)で説明する手順に従ってください。

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Azure Portal でのサポート要求の作成と管理

例を見てみましょう。あなたは "Visual Studio MSDN サブスクリプション" のサブスクリプション所有者です。
同僚の Joe は、このサブスクリプション内の一部のリソース グループのリソース所有者であり、サブスクリプションに対する読み取りアクセス許可を持っています。
あなたは、このサブスクリプションでリソースのサポート チケットを作成および管理できるアクセス権を Joe に付与したいと考えています。

1. 最初の手順は、サブスクリプションにアクセスすることです。 **[設定]** で、ユーザーの一覧を表示します。 サブスクリプションに対する閲覧者アクセス権を持つユーザー Joe を選択します。 新しいカスタム ロールを Joe に割り当ててみましょう。

    ![ロールの追加](./media/create-manage-support-requests-using-access-control/add-role.png)

2. [ユーザー] ブレードで [追加] をクリックします。 ロールの一覧で、"Support Request Contributor" というカスタム ロールを選択します。

    ![サポートの共同作成者ロールの追加](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. ロール名を選択したら、[ユーザーの追加] をクリックし、Joe の電子メールの資格情報を入力します。 [選択] をクリックします。

    ![Add users](./media/create-manage-support-requests-using-access-control/add-users.png)

4. [OK] をクリックして続行します。

    ![アクセス権の追加](./media/create-manage-support-requests-using-access-control/add-access.png)

5. あなたが所有者であるサブスクリプションで、新しく追加されたカスタム ロール "Support Request Contributor" が割り当てられたユーザーが表示されます。

    ![追加されたユーザー](./media/create-manage-support-requests-using-access-control/user-added.png)

    Joe がポータルにサインインすると、Joe が追加されたサブスクリプションが Joe に表示されます。

7. Joe は、[ヘルプとサポート] ブレードの [新しいサポート要求] をクリックし、"Visual Studio Ultimate with MSDN" のサポート要求を作成できます。

    ![新しいサポート要求](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Joe が "すべてのサポート要求" をクリックすると、このサブスクリプションで作成されたサポート要求の一覧が表示されます。![サポート案件の詳細ビュー](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Azure Portal でのサポート要求アクセス権の削除

サポート要求を作成および管理するアクセス権をユーザーに付与できるのと同様に、ユーザーのアクセス権を削除することもできます。

サポート要求を作成および管理する権限を削除するには、サブスクリプションに移動して [設定] をクリックし、目的のユーザー (この例では Joe) をクリックします。 ロール名 "Support Request Contributor" を右クリックし、[削除] をクリックします。

![サポート要求アクセス権の削除](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Joe がポータルにログインし、サポート要求を作成しようとすると、次のエラーが発生します。

![サブスクリプション エラー 2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe が "All support requests" (すべてのサポート要求) を選択しても、サポート要求は表示されません

![サポート案件の詳細ビュー 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
