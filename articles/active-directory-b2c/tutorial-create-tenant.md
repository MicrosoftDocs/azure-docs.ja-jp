---
title: チュートリアル - Azure Active Directory B2C テナントを作成する
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory B2C テナントを作成し、アプリケーションの登録の準備をする方法について説明します。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa91851787bbb0f5570a4f439f794ee352bf0625
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579690"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>チュートリアル:Azure Active Directory B2C テナントの作成

アプリケーションが Azure Active Directory B2C (Azure AD B2C) とやりとりできるようにするには、管理しているテナントに登録する必要があります。 

> [!NOTE]
> サブスクリプションあたり最大 20 個のテナントを作成できます。 この制限は、サービス拒否攻撃などのリソースに対する脅威からの保護に役立ち、Azure portal と基盤となるテナント作成 API の両方で適用されます。 20 を超えるテナントを作成する必要がある場合は、[Microsoft サポート](support-options.md)にお問い合わせください。
> 
> 以前に削除しようとしたテナント名を再利用する場合に、ドメイン名を入力したときに "Already in use by another directory (既に別のディレクトリによって使用されています)" というエラーが表示された場合は、[これらの手順に従って最初にテナントを完全に削除する](./faq.md?tabs=app-reg-ga#how-do-i-delete-my-azure-ad-b2c-tenant)必要があります。 少なくともサブスクリプション管理者のロールが必要です。 テナントを削除した後、ドメイン名を再利用する前にサインアウトしてから再度サインインすることが必要になる場合もあります。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * Azure AD B2C テナントが含まれているディレクトリに切り替える
> * Azure portal で Azure AD B2C リソースを "**お気に入り**" として追加する

次のチュートリアルでは、アプリケーションを登録する方法を学習します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。 サブスクリプション内、またはサブスクリプション内のリソース グループ内で[共同作成者](../role-based-access-control/built-in-roles.md)以上のロールが割り当てられている Azure アカウントでサインインします。

1. ご利用のサブスクリプションが含まれているディレクトリを選択します。

    Azure portal のツール バーで、 **[Directory + Subscription]\(ディレクトリ + サブスクリプション\)** アイコンを選択し、ご利用のサブスクリプションが含まれているディレクトリを選択します。 このディレクトリは、Azure AD B2C テナントを含むディレクトリと異なります。

    ![サブスクリプション テナント、サブスクリプション テナントが選択された状態のディレクトリ + サブスクリプションのフィルター](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

   ![[リソースの作成] ボタンを選択する](media/tutorial-create-tenant/create-a-resource.png)

1. **[Azure Active Directory B2C]** を検索して、 **[作成]** を選択します。
2. **[Create a new Azure AD B2C Tenant]\(新しい Azure AD B2C テナントの作成\)** を選択します。

    ![Azure portal で選択された新しい Azure AD B2C テナントを作成する](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. **[ディレクトリの作成]** ページで、次の情報を入力します。

   - **[組織名]** - Azure AD B2C テナントの名前を入力します。
   - **[初期ドメイン名]** - Azure AD B2C テナントのドメイン名を入力します。
   - **[国またはリージョン]** - 該当する国またはリージョンを一覧から選択します。 この選択を後から変更することはできません。
   - **[サブスクリプション]** - 一覧からサブスクリプションを選択します。
   - **[リソース グループ]** - テナントが含まれるリソース グループを選択または検索します。

    ![Azure portal でのサンプル値が含まれているテナントの作成フォーム](media/tutorial-create-tenant/review-and-create-tenant.png)

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. ディレクトリの設定を確認します。 **[作成]** を選択します。 デプロイのエラーをトラブルシューティングする場合は、[こちら](../azure-resource-manager/templates/common-deployment-errors.md)を参照してください。

課金のために、複数の Azure AD B2C テナントを 1 つの Azure サブスクリプションにリンクすることができます。 テナントをリンクするユーザーは、Azure AD B2C テナントの管理者であること、また、Azure サブスクリプション内で共同作成者以上のロールが割り当てられていることが必要です。 「[Azure AD B2C テナントをサブスクリプションにリンクする](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)」を参照してください。

## <a name="select-your-b2c-tenant-directory"></a>B2C テナント ディレクトリを選択する

新しい Azure AD B2C テナントの使用を開始するには、テナントが含まれているディレクトリに切り替える必要があります。

Azure portal の上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択してから、Azure AD B2C テナントを含むディレクトリを選択します。

新しい Azure B2C テナントが最初からリストに表示されない場合、ブラウザー ウィンドウを更新し、上部メニューでもう一度 **[ディレクトリ + サブスクリプション]** を選択します。

![Azure portal で選択された B2C テナントを含むディレクトリ](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C をお気に入りとして追加する (省略可能)

この省略可能な手順を実行すると、次のチュートリアルおよび以降のすべてのチュートリアルでの Azure AD B2C テナントの選択が容易になります。

テナントの操作が必要になるたびに **[すべてのサービス]** から「*Azure AD B2C*」を検索する代わりに、そのリソースをお気に入りとして設定することができます。 これにより、ポータル メニューの **[お気に入り]** セクションから選択することで、Azure AD B2C テナントを素早く参照することができます。

この操作は 1 回だけ実行する必要があります。 これらの手順を実行する前に、前の「[B2C テナントのディレクトリを選択する](#select-your-b2c-tenant-directory)」セクションの説明に従って Azure AD B2C テナントを含むディレクトリに切り替えていることを確認してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal のメニューで、 **[すべてのサービス]** を選択します。
1. **[すべてのサービス]** 検索ボックスで、「**Azure AD B2C**」を検索し、検索結果の上にマウス ポインターを移動して、ヒント内の星のアイコンを選択します。 **Azure AD B2C** が Azure portal の **[お気に入り]** に表示されるようになりました。
1. 新しいお気に入りの位置を変更する場合は、Azure portal のメニューに移動し、 **[Azure AD B2C]** を選択して、目的の位置まで上下にドラッグします。

    ![Azure AD B2C、[お気に入り] メニュー、Microsoft Azure portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * Azure AD B2C テナントが含まれているディレクトリに切り替える
> * Azure portal で Azure AD B2C リソースを "**お気に入り**" として追加する

次に、新しいテナントに Web アプリケーションを登録する方法を学びます。

> [!div class="nextstepaction"]
> [アプリケーションの登録 >](tutorial-register-applications.md)