---
title: Azure Active Directory 組織用のサインインを設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0da27c5ce872d1b4e1b4c63f6f4207bb5ca4d6ef
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428076"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定する

>[!NOTE]
> この機能はパブリック プレビュー段階にあります。 運用環境で、この機能を使用しないでください。

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Azure AD を使用するには、それを表すアプリケーションを作成する必要があります。 この記事では、Azure AD B2C のユーザー フローを使用して、特定の Azure AD 組織のユーザーがサインインできるようにする方法について説明します。

## <a name="create-an-azure-ad-app"></a>Azure AD アプリの作成

特定の Azure AD 組織のユーザーのサインインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。このテナントは、お使いの Azure AD B2C テナントと同じではありません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある [ディレクトリとサブスクリプション フィルター] をクリックして、お使いの Azure AD テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、**[アプリの登録]** を検索して選択します。
4. **[新しいアプリケーションの登録]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
6. **[アプリケーションの種類]** には `Web app / API` を選択します。
7. **[サインオン URL]** には、次の URL を全部小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に変更します。 例: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    ここでは、すべての URL で [b2clogin.com](b2clogin.md) を使用してください。

8. **Create** をクリックしてください。 後で使用するために **[アプリケーション ID]** をコピーします。
9. アプリケーションを選択し、**[設定]** を選択します。
10. **[キー]** を選択し、キーの説明を入力し、期間を選択し、**[保存]** をクリックします。 後で使用するために、表示されているキーの値をコピーします。

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

1. Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
3. **[ID プロバイダー]**、**[追加]** の順に選択します。
4. **[名前]** を入力します。 たとえば､"Contoso Azure AD" を入力します｡
5. **[ID プロバイダーの種類]** を選択し、**[Open ID Connect (Preview)]\(Open ID Connect (プレビュー))** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーをセットアップします]** をクリックします。
7. **Metadata url** には、次の URL を入力します。`your-AD-tenant-domain` は、Azure AD テナントのドメイン名で置き換えます。 例: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. **クライアント ID** には、先ほどメモしたアプリケーション ID を入力し、**クライアント シークレット**には、先ほどメモしたキーの値を入力します。
9. 必要に応じて、**Domain_hint** に値を入力します。 たとえば、「 `ContosoAD` 」のように入力します。 この値は､要求で *domain_hint* を使用するこの ID プロバイダーを参照するときに使用します｡ 
10. Click **OK**.
11. **[この ID プロバイダーの要求をマップする]** を選択し、次の要求を設定します。
    
    - **User ID** には `oid` を入力します｡
    - **Display Name** には `name` を入力します｡
    - **Given name** には `given_name` を入力します｡
    - **Surname** には `family_name` を入力します｡
    - **Email** には `unique_name` を入力します｡

12. **[OK]** をクリックし、**[作成]** をクリックして構成を保存します。
