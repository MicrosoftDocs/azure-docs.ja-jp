---
title: トークンの構成 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でトークンの有効期間と互換性の設定を構成する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83f8051fa31b6431d4a8515e2c0912cc1872a402
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064383"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でトークンを構成する

この記事では、Azure Active Directory B2C (Azure AD B2C) で[トークンの有効期間と互換性](active-directory-b2c-reference-tokens.md)を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[ユーザー フローを作成](tutorial-create-user-flows.md)して、ユーザーがアプリケーションにサインアップおよびサインインできるようにします。

## <a name="configure-token-lifetime"></a>トークンの有効期間を構成する

トークンの有効期間は任意のユーザー フローで構成できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー (ポリシー)]** を選択します。
5. あらかじめ作成しておいたユーザー フローを開きます。
6. **[プロパティ]** を選択します。
7. **[トークンの有効期間]** で、アプリケーションのニーズに合わせて次のプロパティを調整します。

    ![Azure portal のトークンの有効期間プロパティの設定](./media/configure-tokens/token-lifetime.png)

8. **[Save]** をクリックします。

## <a name="configure-token-compatibility"></a>トークンの互換性を構成する

1. **[ユーザー フロー (ポリシー)]** を選択します。
2. あらかじめ作成しておいたユーザー フローを開きます。
3. **[プロパティ]** を選択します。
4. **[トークンの互換性設定]** で、アプリケーションのニーズに合わせて次のプロパティを調整します。

    ![Azure portal のトークンの互換性プロパティの設定](./media/configure-tokens/token-compatibility.png)

5. **[Save]** をクリックします。

## <a name="next-steps"></a>次の手順

[アクセス トークンを使用する](active-directory-b2c-access-tokens.md)方法の詳細を学習してください。



