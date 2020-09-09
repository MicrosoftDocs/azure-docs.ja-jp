---
title: Amazon アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Amazon アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388444"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Amazon アカウントでのサインアップおよびサインインを設定する

## <a name="create-an-app-in-the-amazon-developer-console"></a>Amazon 開発者コンソールでアプリを作成する

Azure Active Directory B2C (Azure AD B2C) でフェデレーション ID プロバイダーとして Amazon アカウントを使用するには、「[Amazon Developer Services and Technologies](https://developer.amazon.com)」でアプリケーションを作成する必要があります。 まだ Amazon アカウントを持っていない場合は、[https://www.amazon.com/](https://www.amazon.com/) でサインアップできます。

> [!NOTE]  
> 以下の**手順 8** で次の URL を使用します。`your-tenant-name` はご利用のテナントの名前に置き換えてください。 テナント名を入力するときに、テナントが Azure AD B2C に大文字で定義されている場合でも、すべてに小文字を使用します。
> - **[許可されたオリジン]** には、「`https://your-tenant-name.b2clogin.com`」を入力します 
> - **[Allowed Return URLs]\(許可された戻り先 URL\)** には、「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>ID プロバイダーとして Amazon アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Amazon]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Amazon*」とします。
1. **[クライアント ID]** には、前に作成した Amazon アプリケーションのクライアント ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。
