---
title: Microsoft 365 アカウントを使用して Azure にサインアップする
description: Microsoft 365 アカウントを使用して、Azure サブスクリプションを作成する方法について説明します。 また、既存の Azure と Microsoft 365 のアカウントを相互に関連付けることもできます。
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5fef68c9de4dae8063799255fcc7d68ff6b1e6b8
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684849"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Microsoft 365 アカウントを使用して Azure のサブスクリプションにサインアップする

Microsoft 365 サブスクリプションがある場合は、Microsoft 365 アカウントを使用して Azure サブスクリプションを作成できます。 Microsoft 365 のユーザー名とパスワードを使用して [Azure portal](https://portal.azure.com/) にサインインします。 仮想マシンを設定するか、他の Azure サービスを使用する場合は、Azure サブスクリプションにサインアップする必要があります。 他のユーザーと Azure サブスクリプションを共有し、[ロールベースのアクセス制御を使用して、Azure サブスクリプションとリソースへのアクセスを管理できます](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

既に Microsoft 365 アカウントと Azure サブスクリプションの両方がある場合は、[Microsoft 365 テナントを Azure サブスクリプションに関連付ける](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)方法に関するページを参照してください。

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Microsoft 365 アカウントを使用して Azure サブスクリプションを取得する

Microsoft 365 のユーザー名とパスワードを使用して Azure にサインアップすることで、時間を節約し、アカウントの増加を回避します。

1. [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs) にサインアップします。
2. Microsoft 365 のユーザー名とパスワードを使用してサインインします。 使用するアカウントでは、管理者のアクセス許可は必要ありません。 複数の Microsoft 365 アカウントを持っている場合、Azure サブスクリプションに関連付ける Microsoft 365 アカウントの資格情報を使用していることを確認します。

   ![サインイン ページを示すスクリーン ショット。](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. 必要な情報を入力し、サインアップ プロセスを完了します。 Microsoft 365 アカウントが既にある場合、一部の情報は不要である可能性があります。

    ![サインアップ フォームを示すスクリーン ショット。](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Azure サブスクリプションに組織内の他のユーザーを追加する必要がある場合は、[Azure ポータルでのアクセス管理の開始](../../role-based-access-control/overview.md)に関する記事を参照してください。

## <a name=""></a><a id="more-about-subs">Azure サブスクリプションと Microsoft 365 サブスクリプションの背景情報</a>

Microsoft 365 と Azure では、ユーザーとサブスクリプションの管理に Azure AD サービスが使用されています。 Azure のディレクトリは、ユーザーとサブスクリプションをグループ化するコンテナーのようなものです。 Azure サブスクリプションと Microsoft 365 サブスクリプションで同じユーザー アカウントを使用するには、Azure サブスクリプションを Microsoft 365 サブスクリプションと同じディレクトリに作成する必要があります。 以下の点に注意してください。

* サブスクリプションはディレクトリに作成されます。
* ユーザーはディレクトリに属しています。
* サブスクリプションは、サブスクリプションを作成したユーザーのディレクトリに属します。 そのため、Microsoft 365 サブスクリプションは、Azure サブスクリプションと同じアカウントに関連付けられます。
* Azure サブスクリプションの所有者は、ディレクトリ内の各ユーザーです。
* Microsoft 365 サブスクリプションの所有者は、ディレクトリ自体です。 ディレクトリ内で適切なアクセス許可を持つユーザーは、これらのサブスクリプションを管理できます。

![ディレクトリとユーザー、サブスクリプションの関係を示すスクリーン ショット。](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- 他のユーザーと Azure サブスクリプションを共有し、[ロールベースのアクセス制御を使用して、Azure サブスクリプションとリソースへのアクセスを管理します](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。