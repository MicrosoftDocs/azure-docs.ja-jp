---
title: チュートリアル - Azure Active Directory B2C テナントを作成する | Microsoft Docs
description: Azure portal を使用して Azure Active Directory B2C テナントを作成し、アプリケーションの登録の準備をする方法について説明します。
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8878eccb079bf78c45ff9c1e4040659d109b1ab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152932"
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
2. 上部メニューの **[Directory and subscription filter]\(ディレクトリとサブスクリプション フィルター\)** をクリックし、ご自分のサブスクリプションを含むディレクトリを選択することによって､適切なサブスクリプションを含むディレクトリを使用していることを確認します｡ このディレクトリは、Azure AD B2C テナントを含むディレクトリと異なります。

    ![サブスクリプション ディレクトリに切り替える](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Azure portal の左上にある **[リソースの作成]** を選択します。
4. **Active Directory B2C** を検索して選択し、**作成**をクリックします。
5. **[新しい Azure AD B2C テナントを作成する]** を選択し、テナント名に使用されている組織名と初期のドメイン名を入力して､国 (後で変更不可) を選択して、**[作成]** をクリックします。

    ![テナントの作成](./media/tutorial-create-tenant/create-tenant.png)

    この例では、テナント名は contoso0926Tenant.onmicrosoft.com です

6. **[新しい B2C テナントを作成または既存のテナントへのリンク]** ページで **[既存の Azure AD B2C テナントを Azure サブスクリプションにリンクする]** を選択し、作成したテナントを選択してから、**[新規作成]** をクリックします。
7. テナントを含むリソース グループの名前を入力し、場所を選択してから、**[作成]** をクリックします。
8. 新しいテナントを使い始めるには､上部メニューの **[Directory and subscription filter]\(ディレクトリとサブスクリプション フィルター\)** をクリックし、自分の Azure AD B2C テナントを含むディレクトリを選択することによって､自分の Azure AD B2C テナントを含む適切なディレクトリを使用していることを確認します｡

    ![テナント ディレクトリに切り替える](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク

> [!div class="nextstepaction"]
> [アプリケーションの登録](tutorial-register-applications.md)
