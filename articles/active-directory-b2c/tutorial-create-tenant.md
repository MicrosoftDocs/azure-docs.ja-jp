---
title: チュートリアル - Azure Active Directory B2C テナントを作成する
description: Azure portal を使用して Azure Active Directory B2C テナントを作成し、アプリケーションの登録の準備をする方法について説明します。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056317"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>チュートリアル:Azure Active Directory B2C テナントの作成

アプリケーションが Azure Active Directory (Azure AD) B2C と対話できるようにするには、管理しているテナントに登録する必要があります。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク

次のチュートリアルでは、アプリケーションを登録する方法を学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご利用のサブスクリプションが含まれるディレクトリを必ず使用してください。 上部メニューで **[Directory and subscription filter]\(ディレクトリとサブスクリプション フィルター\)** フィルターをクリックし、ご利用のサブスクリプションが含まれるディレクトリを選択します。 このディレクトリは、Azure AD B2C テナントを含むディレクトリと異なります。

    ![サブスクリプション ディレクトリに切り替える](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Azure portal の左上にある **[リソースの作成]** を選択します。
4. **Active Directory B2C** を検索して選択し、**作成**をクリックします。
5. **[新しい Azure AD B2C テナントを作成する]** を選択し、組織名と初期ドメイン名を入力します。 国/地域を選択し (後で変更できません)、 **[作成]** をクリックします。

    初期ドメイン名はテナント名の一部として使用されます。 この例では、テナント名は *contoso0926Tenant.onmicrosoft.com* です。

    ![テナントの作成](./media/tutorial-create-tenant/create-tenant.PNG)

6. **[新しい B2C テナントの作成または既存のテナントへのリンク]** ページで、 **[既存の Azure AD B2C テナントを Azure サブスクリプションにリンクする]** を選択します。

    作成したテナントを選択し、ご利用のサブスクリプションを選択します。

    リソース グループには **[新規作成]** を選択します。 テナントを含むリソース グループの名前を入力し、場所を選択してから、 **[作成]** をクリックします。
1. 新しいテナントを使い始めるには､上部メニューの **[Directory and subscription filter]\(ディレクトリとサブスクリプション フィルター\)** をクリックし、自分の Azure AD B2C テナントを含むディレクトリを選択することによって､自分の Azure AD B2C テナントを含む適切なディレクトリを使用していることを確認します｡

    新しい Azure B2C テナントが最初からリストに表示されない場合、ブラウザー ウィンドウを更新し、上部メニューでもう一度 **[Directory and subscription filter]\(ディレクトリとサブスクリプション フィルター\)** を選択します。

    ![テナント ディレクトリに切り替える](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク

次に、新しいテナントに Web アプリケーションを登録する方法を学びます。

> [!div class="nextstepaction"]
> [アプリケーションの登録 >](tutorial-register-applications.md)
