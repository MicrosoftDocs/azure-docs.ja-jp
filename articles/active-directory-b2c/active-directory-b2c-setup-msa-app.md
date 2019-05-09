---
title: Microsoft アカウントでのサインアップとサインインを設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Microsoft アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 13e199a56a3cdd4f8e5a21f162fe0397c6f397cd
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428257"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Microsoft アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-microsoft-account-application"></a>Microsoft アカウント アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oidc.md)として Microsoft アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Microsoft アカウントを持っていない場合は、[https://www.live.com/](https://www.live.com/) で取得できます。

1. Microsoft アカウントの資格情報で [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)にサインインします。
2. 右上隅から **[Add an app (アプリの追加)]** を選択します。
3. アプリケーションの **[名前]** を入力します。 たとえば、 *MSAapp1* です。
4. **[新しいパスワードを生成する]** を選択し、ID プロバイダーを構成するときに使用するパスワードをコピーします。 **[アプリケーション ID]** もコピーします。 
5. **[プラットフォームの追加]** を選択し、**[Web]** を選択します。
4. **[リダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。
5. **[保存]** を選択します。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>ID プロバイダーとして Microsoft account アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*MSA*」と入力します。
6. **[ID プロバイダーの種類]** を選択し、**[Microsoft アカウント]** を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したアプリケーション ID を入力し、前に作成した Microsoftアカウント アプリケーションの **[クライアント シークレット]** として記録したパスワードを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Microsoft アカウントの構成を保存します。

