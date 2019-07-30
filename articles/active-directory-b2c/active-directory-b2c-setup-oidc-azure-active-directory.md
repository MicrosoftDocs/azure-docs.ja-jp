---
title: Azure Active Directory 組織用のサインインを設定する - Azure Active Directory B2C
description: Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b867a7f9ffeab3d243c8c094830aa0984cffd04a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654195"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定する

>[!NOTE]
> この機能はパブリック プレビュー段階にあります。 運用環境で、この機能を使用しないでください。

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Azure AD を使用するには、それを表すアプリケーションを作成する必要があります。 この記事では、Azure AD B2C のユーザー フローを使用して、特定の Azure AD 組織のユーザーがサインインできるようにする方法について説明します。

## <a name="create-an-azure-ad-app"></a>Azure AD アプリの作成

特定の Azure AD 組織のユーザーのサインインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。このテナントは、お使いの Azure AD B2C テナントと同じではありません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご自分の Azure AD テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリとサブスクリプション] フィルター**を選択し、ご利用の Azure AD テナントが含まれるディレクトリを選択します。 これは、Azure AD B2C テナントと同じテナントではありません。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
4. **[新規登録]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
6. このアプリケーションには **[この組織のディレクトリ内のアカウントのみ]** の選択をそのまま使用します。
7. **[リダイレクト URL]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。 例: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    ここでは、すべての URL で [b2clogin.com](b2clogin.md) を使用してください。

8. **[登録]** をクリックします。 後で使用するために **[アプリケーション (クライアント) ID]** をコピーします。
9. アプリケーション メニューで **[証明書とシークレット]** を選択してから、 **[新しいクライアント シークレット]** を選択します。
10. クライアント シークレットの名前を入力します。 たとえば、「 `Azure AD B2C App Secret` 」のように入力します。
11. 有効期限を選択します。 このアプリケーションには、 **[1 年]** の選択をそのまま使用します。
12. **[追加]** を選択して、後で使用するために表示されている新しいクライアント シークレットの値をコピーします。

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリとサブスクリプション] フィルター**を選択し、ご利用の Azure AD B2C テナントが含まれるディレクトリを選択します。
2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
3. **[ID プロバイダー]** 、 **[追加]** の順に選択します。
4. **[名前]** を入力します。 たとえば、「 `Contoso Azure AD`」のように入力します。
5. **[ID プロバイダーの種類]** を選択し、 **[Open ID Connect (Preview)]\(Open ID Connect (プレビュー))** を選択して、 **[OK]** をクリックします。
6. **[この ID プロバイダーをセットアップします]** を選択します
7. **Metadata url** には、次の URL を入力します。`your-AD-tenant-domain` は、Azure AD テナントのドメイン名で置き換えます。 例: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. **[クライアント ID]** には、先ほどメモしたアプリケーション ID を入力し、 **[クライアント シークレット]** には、先ほどメモしたクライアント シークレットを入力します。
9. 必要に応じて、**Domain_hint** に値を入力します。 たとえば、「 `ContosoAD` 」のように入力します。 この値は､要求で *domain_hint* を使用するこの ID プロバイダーを参照するときに使用します｡
10. Click **OK**.
11. **[この ID プロバイダーの要求をマップする]** を選択し、次の要求を設定します。

    - **User ID** には `oid` を入力します｡
    - **Display Name** には `name` を入力します｡
    - **Given name** には `given_name` を入力します｡
    - **Surname** には `family_name` を入力します｡
    - **Email** には `unique_name` を入力します｡

12. **[OK]** をクリックし、 **[作成]** をクリックして構成を保存します。
