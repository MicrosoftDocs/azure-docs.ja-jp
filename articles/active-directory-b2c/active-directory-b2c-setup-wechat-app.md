---
title: Azure Active Directory B2C を使用して WeChat アカウントでのサインアップおよびサインインを設定する
description: Azure Active Directory B2C を使用するアプリケーションで WeChat アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ad5e5b766c38b2ce3c48c22e62dd1fd2b9a79853
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064959"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して WeChat アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>WeChat アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で ID プロバイダーとして WeChat アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ WeChat アカウントを持っていない場合は、[https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) で情報を取得できます。

### <a name="register-a-wechat-application"></a>WeChat アプリケーションを登録する

1. WeChat 資格情報を使用して、[ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) にサインインします。
1. **[管理中心 (管理センター)]** を選択します。
1. 新しいアプリケーションを登録するための手順に従います。
1. **[授权回调域 (コールバック URL)]** に、「`https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 たとえば、テナント名が contoso の場合、URL を`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`に設定します。
1. **アプリ ID** と**アプリ キー**をコピーします。 ID プロバイダーをテナントに追加するには、これらが必要です。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして WeChat を構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[WeChat (プレビュー)]** を選択します。
1. **[名前]** を入力します。 たとえば、「*WeChat*」とします。
1. **[クライアント ID]** には、前に作成した WeChat アプリケーションのアプリ ID を入力します。
1. **[クライアント シークレット]** には、記録したアプリ キーを入力します。
1. **[保存]** を選択します。
