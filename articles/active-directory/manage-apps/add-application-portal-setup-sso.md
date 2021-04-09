---
title: クイックスタート:Azure Active Directory (Azure AD) テナント内のアプリケーションへの SAML ベースのシングル サインオン (SSO) を設定する
description: このクイックスタートでは、Azure Active Directory (Azure AD) テナント内のアプリケーションへの SAML ベースのシングル サインオン (SSO) を設定するプロセスについて説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 44a838bdce4721d24bd0bdbb45ac7b36ceb34e71
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102488371"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>クイックスタート:Azure Active Directory (Azure AD) テナント内のアプリケーションへの SAML ベースのシングル サインオン (SSO) を設定する

Azure Active Directory (Azure AD) テナントに追加したアプリケーションへのシングル サインオン (SSO) を設定することで、簡略化されたユーザー ログインの利用を開始します。 SSO を設定すると、ユーザーが自分の Azure AD の資格情報を使用してアプリケーションにサインインできるようになります。 SSO は Azure AD の Free Edition に含まれています。

## <a name="prerequisites"></a>前提条件

Azure AD テナントに追加したアプリケーションへの SSO を設定するには、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 既に事前構成済みで Azure AD ギャラリーに追加されている、SSO をサポートするアプリケーション。 ほとんどのアプリでは、SSO に Azure AD を使用できます。 Azure AD ギャラリー内のアプリは事前構成されています。 ご自分のアプリが一覧に表示されていない場合、またはカスタム開発されたアプリの場合でも、Azure AD で使用できます。 目次にあるチュートリアルやその他のドキュメントを確認してください。 このクイックスタートは、SSO 用に事前構成され、アプリ開発者によって Azure AD ギャラリーに追加されたアプリに重点を置いています。
- 省略可能:[アプリの表示](view-applications-portal.md)の完了。
- 省略可能:[アプリの追加](add-application-portal.md)の完了。
- 省略可能:[アプリの構成](add-application-portal-configure.md)の完了。
- 省略可能:[アプリへのユーザーの割り当て](add-application-portal-assign-users.md)の完了。


>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用してください。

## <a name="enable-single-sign-on-for-an-app"></a>アプリへのシングル サインオンを有効にする

Azure AD テナントへのアプリケーションの追加が完了すると、概要ページが表示されます。 既に追加されているアプリケーションを構成する場合は、最初のクイックスタートを参照してください。 テナントに追加されているアプリケーションを表示する手順が説明されています。

アプリケーションへのシングル サインオンを設定するには、次を行います。

1. Azure AD portal で、 **[エンタープライズ アプリケーション]** を選択します。 シングル サインオンを設定するアプリケーションを見つけて選択します。
1. **[管理]** セクションで、 **[シングル サインオン]** を選択して編集用の **[シングル サインオン]** ペインを開きます。

    > [!IMPORTANT]
    > アプリで SSO に OpenID Connect (OIDC) が使用される場合、ナビゲーションにシングル サインオン オプションが表示されません。 OIDC ベース SSO に関するクイックスタートを参照し、設定方法を習得してください。

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Azure AD ポータル上のシングル サインオンの構成ページを示すスクリーンショット。":::

1. **[SAML]** を選択して SSO の構成ページを開きます。 この例では、SSO 用に構成しているアプリケーションは GitHub です。 GitHub が設定されると、ユーザーは自分の Azure AD テナントの資格情報を使用して GitHub にサインインできるようになります。

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="GitHub 上のシングル サインオンの構成ページを示すスクリーンショット。":::

1. SAML ベースの SSO に Azure AD を使用するようにアプリケーションを構成するプロセスは、アプリケーションによって異なります。 GitHub に関するガイダンスへのリンクが表示されています。 他のアプリに関するガイドは、「[SaaS アプリケーションと Azure Active Directory の統合に関するチュートリアル](/azure/active-directory/saas-apps/)」で見つけることができます。
1. アプリケーションへの SSO を設定するには、そのガイドに従ってください。 多くのアプリケーションには、SSO 機能に関する特定のサブスクリプション要件があります。 たとえば、GitHub の場合は Enterprise サブスクリプションが必要です。
    > [!TIP]
    > SAML 構成オプションの詳細については、「[SAML ベースのシングル サインオンの構成](configure-saml-single-sign-on.md)」を参照してください。

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="GitHub の価格設定ページの Enterprise サブスクリプション内に表示されたシングル サインオン オプションを示すスクリーンショット。":::

> [!TIP]
> Graph API を使用してアプリの管理を自動化できます。[Microsoft Graph API によるアプリ管理の自動化](/graph/application-saml-sso-configure-api)に関するページを参照してください。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタート シリーズを完了したら、アプリを削除してテスト テナントをクリーンアップすることを検討してください。 アプリの削除については、このシリーズの最後のクイックスタートである[アプリの削除](delete-application-portal.md)に関する記事で説明されています。

## <a name="next-steps"></a>次のステップ

次の記事に進み、アプリを削除する方法を学習してください。
> [!div class="nextstepaction"]
> [アプリを削除する](delete-application-portal.md)
