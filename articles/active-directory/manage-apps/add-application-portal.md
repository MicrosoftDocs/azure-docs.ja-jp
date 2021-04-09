---
title: クイック スタート:Azure Active Directory (Azure AD) テナントにアプリケーションを追加する
description: このクイック スタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントにギャラリー アプリケーションを追加します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 2d3b0be88a23bbbb0573b4d11a2e5a39dc6254da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259069"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>クイック スタート:Azure Active Directory (Azure AD) テナントにアプリケーションを追加する

Azure Active Directory (Azure AD) には、何千もの事前統合されたアプリケーションを含むギャラリーがあります。 組織で使用しているアプリケーションの多くは、おそらく既にギャラリーにあります。

アプリケーションを Azure AD テナントに追加すると、次のことが可能になります。

- アプリ用にプロパティを構成する。
- 条件付きアクセス ポリシーを使用してアプリへのユーザー アクセスを管理する。
- ユーザーが Azure AD の資格情報でアプリにサインインできるように、シングル サインオンを構成する。

## <a name="prerequisites"></a>前提条件

Azure AD テナントにアプリケーションを追加するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- (省略可能: [アプリの表示](view-applications-portal.md)の完了)。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用することをお勧めします。

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Azure AD テナントにアプリを追加する

Azure AD テナントにアプリケーションを追加するには、次のようにします。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
2. **[Azure Active Directory]** ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。 **[すべてのアプリケーション]** ウィンドウが開き、Azure AD テナントのアプリケーションのランダム サンプルが表示されます。
3. **[エンタープライズ アプリケーション]** ペインで、 **[新しいアプリケーション]** を選択します。 
    ![[新しいアプリケーション] を選択して、テナントにギャラリー アプリケーションを追加する](media/add-application-portal/new-application.png)
4. 新しいギャラリー プレビュー エクスペリエンスに切り替えます。 **[アプリケーションの追加]** ページの上部にあるバナーで、 **[Click here to try out the new and improved app gallery] (改善された新しいアプリ ギャラリーを試すには、こちらをクリックしてください)** というリンクを選択します。
5. **[Azure AD ギャラリーの参照]** (プレビュー) ペインが開き、クラウド プラットフォーム、オンプレミス アプリケーション、および注目のアプリケーションのタイルが表示されます。 **[注目のアプリケーション]** セクションに一覧表示されているアプリケーションには、フェデレーション シングル サインオン (SSO) とプロビジョニングをサポートしているかどうかを示すアイコンがあります。 
    ![名前またはカテゴリーでアプリを検索する](media/add-application-portal/browse-gallery.png)
6. 追加するアプリケーションはギャラリーで参照できます。または、検索ボックスに名前を入力してアプリケーションを検索します。 次に、結果からアプリケーションを選択します。 
7. 次の手順は、アプリケーションの開発者がシングル サインオン (SSO) を実装した方法によって異なります。 シングル サインオンは、4 つの方法でアプリ開発者が実装できます。 その 4 つの方法とは、SAML、OpenID Connect、パスワード、リンクです。 アプリを追加するときは、スクリーンショットに示されているように、特定の SSO 実装を使用しているアプリのみをフィルター処理して表示するように選択できます。 たとえば、SSO を実装する一般的な標準は、Security Assertion Markup Language (SAML) と呼ばれます。 もう 1 つの一般的な標準は、OpenId Connect (OIDC) と呼ばれています。 これらの標準を使用して SSO を構成する方法は異なるため、追加するアプリによって実装される SSO の種類をメモしてください。

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="SSO の種類のセレクターを示すスクリーンショット。" lightbox="media/add-application-portal/sso-types.png":::

    - アプリの開発者が SSO に **OIDC 標準** を使用している場合は、 **[サインアップ]** を選択します。 設定ページが表示されます。 次に、OIDC ベースのシングル サインオンの設定に関するクイックスタートに進んでください。
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="OIDC ベースの SSO アプリの追加を示すスクリーンショット。":::

    - アプリの開発者が SSO に **SAML 標準** を使用している場合は、 **[作成]** を選択します。 [作業の開始] ページが表示され、組織のアプリケーションを構成するためのオプションが表示されます。 フォームでは、組織のニーズに合わせてアプリケーションの名前を編集できます。 次に、SAML ベースのシングル サインオンの設定に関するクイックスタートに進んでください。
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="SAML ベースの SSO アプリの追加を示すスクリーンショット。":::


> [!IMPORTANT]
> SAML ベースと OIDC ベースの SSO 実装には、いくつかの重要な違いがあります。 SAML ベースのアプリでは、同じアプリの複数のインスタンスを追加できます。 たとえば、GitHub1、GitHub2 などです。 OIDC ベースのアプリでは、追加できるアプリのインスタンスは 1 つだけです。 OIDC ベースのアプリを既に追加していて、同じアプリを再度追加しようとして同意を 2 回入力しても、テナントに再び追加されることはありません。

探しているアプリケーションがギャラリーにない場合は、 **[独自のアプリケーションの作成]** のリンクを選択して、 **[アプリケーションでどのような操作を行いたいですか?]** で **[ギャラリーに見つからないその他のアプリケーションを統合します]** を選択します。 Microsoft は既に多くのアプリケーション開発者と協力して、Azure AD と連携するように事前に構成しています。 事前に構成済みのアプリがギャラリーに表示されます。 ただし、追加するアプリが一覧に表示されない場合は、新しい汎用アプリを作成し、自分で、またはそれを作成した開発者のガイダンスに従って構成することができます。

アプリケーションの追加が完了しました。 次のクイックスタートでは、アプリケーションのロゴを変更し、他のプロパティを編集する方法を示します。

> [!TIP]
> Graph API を使用してアプリの管理を自動化できます。[Microsoft Graph API によるアプリ管理の自動化](/graph/application-saml-sso-configure-api)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クイックスタート シリーズを続行しない場合は、アプリを削除してテスト テナントをクリーンアップすることを検討してください。 アプリの削除については、このシリーズの最後のクイックスタートである[アプリの削除](delete-application-portal.md)に関する記事で説明されています。

## <a name="next-steps"></a>次のステップ

次の記事に進み、アプリを構成する方法を学習してください。
> [!div class="nextstepaction"]
> [アプリを構成する](add-application-portal-configure.md)