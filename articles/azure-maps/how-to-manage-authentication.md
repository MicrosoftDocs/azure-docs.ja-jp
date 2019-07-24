---
title: Azure Maps での認証の管理 | Microsoft Docs
description: Azure portal を使用して、Azure Maps での認証を管理することができます。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259926"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps での認証の管理

Azure Maps アカウントを作成した後、Azure Active Directory (Azure AD) または共有キー認証のいずれかをサポートするため、クライアント ID とキーが作成されます。

## <a name="view-authentication-details"></a>認証の詳細を表示する

認証の詳細は、Azure portal で確認できます。 自分のアカウントにアクセスして、**[設定]** メニューで **[認証]** を選択します。

![認証の詳細](./media/how-to-manage-authentication/how-to-view-auth.png)

 詳細については、「[Azure Maps による認証](https://aka.ms/amauth)」を参照してください。


## <a name="set-up-azure-ad-app-registration"></a>Azure AD アプリの登録を設定する

Azure Maps アカウントを作成したら、Azure AD テナントと Azure Maps リソース間のリンクを確立する必要があります。

1. Azure AD ブレードに移動し、アプリの登録を作成します。 登録の名前を指定します。 **[サインオン URL]** ボックスに、Web アプリ/API のホーム ページを指定します (例: https:\//localhost/)。 登録済みのアプリが既にある場合は、手順 2 に進みます。

    ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

    ![アプリの登録の詳細](./media/how-to-manage-authentication/app-create.png)

2. 委任された API アクセス許可を Azure Maps に割り当てるには、**[アプリの登録]** の下にあるアプリケーションに移動して **[設定]** を選択します。  **[必要なアクセス許可]**、**[追加]** の順に選択します。 **[API を選択します]** で「**Azure Maps**」を検索して選択し、**[選択]** ボタンを選択します。

    ![アプリの API アクセス許可](./media/how-to-manage-authentication/app-permissions.png)

3. **[アクセス許可の選択]** の下で **[Access Azure Maps]\(Azure Maps へのアクセス\)** を選び、**[選択]** ボタンを選択します。

    ![アプリの API アクセス許可の選択](./media/how-to-manage-authentication/select-app-permissions.png)

4. 使用する認証方法に応じて、手順 a または b を完了します。

    1. アプリケーションで Azure Maps Web SDK とユーザー トークン認証を使用する場合は、アプリの登録の詳細ページの [マニフェスト] セクションで `oauthEnableImplicitFlow` を true に設定し、これを有効にします。
    
       ![アプリ マニフェスト](./media/how-to-manage-authentication/app-manifest.png)

    2. アプリケーションでサーバー/アプリケーション認証を使用する場合は、アプリの登録の **[キー]** ブレードに移動し、パスワードを作成するか、公開キー証明書をアプリの登録にアップロードします。 パスワードを作成する場合は、**[保存]** を選択した後に、後のためにパスワードをコピーして安全に保管します。 Azure AD からトークンを取得するには、このパスワードを使用します。

       ![アプリ キー](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Azure Maps に RBAC を付与する

Azure Maps アカウントをお使いの Azure AD テナントに関連付けると、1 つまたは複数の Azure Maps のアクセス制御ロールにユーザーまたはアプリケーションを割り当てることで、アクセス制御を付与することができます。

1. **[アクセス制御 (IAM)]** に移動し、**[ロールの割り当て]**、**[ロールの割り当ての追加]** の順に選択します。

    ![RBAC の付与](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **[ロールの割り当ての追加]** ウィンドウの **[ロール]** の下で、**[Azure Maps Date Reader (Preview)]\(Azure Maps Date Reader (プレビュー)\)** を選択します。 **[アクセスの割り当て先]** で **[Azure AD user, group, or service principal]\(Azure AD のユーザー、グループ、またはサービス プリンシパル\)** を選択します。 **[選択]** の下でユーザーまたはアプリケーションを選択します。 **[保存]** を選択します。

    ![ロールの割り当ての追加](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>使用可能な Azure Maps RBAC ロールを表示する

Azure Maps に使用できるロールベースのアクセス制御 (RBAC) ロールを表示するには、**[アクセス制御 (IAM)]** に移動し、**[ロール]** を選択し、「**Azure Maps**」で始まるロールを検索します。 これらがアクセス権を付与できるロールです。

![使用可能なロールの表示](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC を表示する

RBAC は詳細なアクセス制御を提供します。

Azure Maps の RBAC が付与されているユーザーまたはアプリを表示するには、**[アクセス制御 (IAM)]** に移動し、**[ロールの割り当て]** を選択して、「**Azure Maps**」でフィルター処理します。

![RBAC が付与されたユーザーとアプリのビュー](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps のトークンを要求する

アプリを登録して Azure Maps と関連付けると、アクセス トークンを要求することができます。

* アプリケーションで Azure Maps Web SDK とユーザー トークン認証を使用する場合、Azure Maps クライアント ID と Azure AD アプリ ID を使って HTML ページを構成する必要があります。

* アプリケーションでサーバー/アプリケーションの認証を使用する場合、Azure AD ログイン エンドポイント `https://login.microsoftonline.com` から、Azure AD リソース ID `https://atlas.microsoft.com/`、Azure Maps クライアント ID、Azure AD アプリ ID、および Azure AD アプリ登録のパスワードまたは証明書を使ってトークンを要求します。

Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、「[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)」をご覧ください。


## <a name="next-steps"></a>次の手順

* Azure AD の認証と Azure Maps Web SDK の詳細については、[Azure AD と Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) に関するページをご覧ください。