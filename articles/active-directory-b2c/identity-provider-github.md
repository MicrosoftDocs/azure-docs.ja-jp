---
title: GitHub アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで GitHub アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388189"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して GitHub アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>GitHub OAuth アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](authorization-code-flow.md)として GitHub アカウントを使用するには、それを表すアプリケーションをテナント内に作成する必要があります。 まだ GitHub アカウントを持っていない場合は、[https://www.github.com/](https://www.github.com/) でサインアップできます。

1. GitHub 資格情報を使用して [GitHub Developer](https://github.com/settings/developers) Web サイトにサインインします。
1. **[OAuth Apps]\(OAuth アプリ\)** を選択し、**[New OAuth App]\(新規 OAuth アプリ\)** を選択します。
1. **[Application name]\(アプリケーション名\)** と **[Homepage URL]\(ホームページ URL\)** を入力します。
1. **[Authorization callback URL]**\(承認コールバック URL\) に `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` を入力します。 `your-tenant-name`を Azure AD B2C テナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用します。
1. **[Register application]\(アプリケーションを登録する\)** をクリックします。
1. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 ID プロバイダーをテナントに追加するには、両方が必要です。

## <a name="configure-a-github-account-as-an-identity-provider"></a>ID プロバイダーとして GitHub アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[GitHub (プレビュー)]** を選択します。
1. **[名前]** を入力します。 たとえば、「*GitHub*」とします。
1. **[クライアント ID]** には、前に作成した GitHub アプリケーションのクライアント ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。
